.. _quickstart:

Quickstart
==========

.. module:: requests.models

Impazienti di cominciare? Questa pagina vi introdurrà all'uso di Requests.

Prima di tutto, assicuratevi che:

* Requests sia :ref:`installato <install>`
* Requests sia :ref:`aggiornato <updates>`


Iniziamo con alcuni semplici esempi.


Effettuare una Richiesta
------------------------

Lanciare una richiesta HTTP con Requests è molto semplice.

Per prima cosa importate il modulo Requests::

    >>> import requests

Ora proviamo a leggere una pagina web. Ad esempio, leggiamo la timeline pubblica di GitHub::

    >>> r = requests.get('https://api.github.com/events')

Abbiamo ottenuto un oggetto di classe :class:`Response <requests.Response>` nella variabile ``r``. Possiamo ora
recuperare tutte le informazioni che ci servono da questo oggetto.

L'API semplicissima di Requests rende le richieste HTTP quasi ovvie. Per esempio, questa è una HTTP POST::

    >>> r = requests.post("http://httpbin.org/post")

Bello, vero? Cosa accade per le altre tipologie di richieste HTTP: PUT, DELETE, HEAD e
OPTIONS? Sono anch'esse semplicissime::

    >>> r = requests.put("http://httpbin.org/put")
    >>> r = requests.delete("http://httpbin.org/delete")
    >>> r = requests.head("http://httpbin.org/get")
    >>> r = requests.options("http://httpbin.org/get")

Finora tutto benissimo, ma è solo un assaggio di ciò che Requests può fare.


Passare parametri negli URL
---------------------------

Spesso dovrete inviare dati nella parte di query string degli URL. Se costruite
gli URL a mano, questi dati sarebbero piazzati come coppie chiave/valore dopo
il punto interrogativo, es: ``httpbin.org/get?key=val``.
Requests vi consente di passare i dati sotto forma di dizionario, usando la keyword
argument ``params``. A titolo di esempio, se volete passare ``key1=value1`` e 
``key2=value2`` a ``httpbin.org/get``, usate::

    >>> payload = {'key1': 'value1', 'key2': 'value2'}
    >>> r = requests.get("http://httpbin.org/get", params=payload)

Potete printare l'URL per verificare che è stato correttamente encoded::

    >>> print(r.url)
    http://httpbin.org/get?key2=value2&key1=value1

Le chiavi del dizionario di valore ``None`` non saranno aggiunte alla query string dell'URL.

Per passare come valore una lista di elementi dovete segnalare il fatto che la chiave
si riferisce ad una lista appendendo ``[]`` alla chiave::

    >>> payload = {'key1': 'value1', 'key2[]': ['value2', 'value3']}
    >>> r = requests.get("http://httpbin.org/get", params=payload)
    >>> print(r.url)
    http://httpbin.org/get?key1=value1&key2%5B%5D=value2&key2%5B%5D=value3

Contenuto delle Risposte
------------------------

Possiamo leggere il contenuto della risposta del server. Leggiamo di nuovo la
timeline di GitHub::

    >>> import requests
    >>> r = requests.get('https://api.github.com/events')
    >>> r.text
    u'[{"repository":{"open_issues":0,"url":"https://github.com/...

Requests decodificherà automaticamente il contenuto del server. La maggior parte dei
caratteri Unicode sono decodificati senza problemi.

Quando lanciate una richiesta, Requests fa delle ipotesi sull'encoding della risposta
sulla base degli header HTTP. L'encoding del testo utilizzato da Requests si applica
al contenuto di ``r.text``. Potete scoprire quale encoding viene usato da Requests e
cambiarlo usando la property ``r.encoding``::

    >>> r.encoding
    'utf-8'
    >>> r.encoding = 'ISO-8859-1'

Se modificate l'encoding, Requests userà il nuovo valore di ``r.encoding``
ogni volta che chiamate ``r.text``. Questo potrebbe esservi utile quando dovete
utilizzare una logica custom per determinare quale encoding avrà il contenuto.
Ad esempio, HTTP e XML hanno la possibilità di specificare il proprio encoding
nel body del documento. In situazioni come questa, like this, dovreste usare ``r.content``
per ottenere l'encoding del documento e in seguito settare ``r.encoding``. Questo permetterà
di usare ``r.text`` con l'encoding corretto.

Requests supporta anche encoding custom nel caso ne abbiate bisogno.
Se avete creato il vostro encoding e l'avete registrato nel modulo ``codecs``, potete 
molto semplicemente usare il valore di ``r.encoding`` e Requests gestirà direttamente il
decoding della risposta per voi.

Contenuto binario delle Risposte
--------------------------------

Potete anche accedere ai byte che costituiscono il corpo delle risposte non testuali::

    >>> r.content
    b'[{"repository":{"open_issues":0,"url":"https://github.com/...

I transfer-encodings ``gzip`` e ``deflate`` sono decodificati automaticamente.

Ad esempio, per creare un'immagine a partire dai dati binari ritornati da una richiesta,
potete usare il seguente codice::

    >>> from PIL import Image
    >>> from StringIO import StringIO
    >>> i = Image.open(StringIO(r.content))


Contenuto JSON delle Risposte
-----------------------------

Se dovete gestire dati in formato JSON, è presente anche un decoder JSON builtin::

    >>> import requests
    >>> r = requests.get('https://api.github.com/events')
    >>> r.json()
    [{u'repository': {u'open_issues': 0, u'url': 'https://github.com/...

Nel caso in cui la decodifica JSON fallisca, ``r.json`` solleva un'eccezione. Ad esempio, se
la risposta è un 401 (Unauthorized), l'accesso a ``r.json`` solleva un ``ValueError:
No JSON object could be decoded``


Raw Response Content
--------------------

In the rare case that you'd like to get the raw socket response from the
server, you can access ``r.raw``. If you want to do this, make sure you set
``stream=True`` in your initial request. Once you do, you can do this::

    >>> r = requests.get('https://api.github.com/events', stream=True)
    >>> r.raw
    <requests.packages.urllib3.response.HTTPResponse object at 0x101194810>
    >>> r.raw.read(10)
    '\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03'

In general, however, you should use a pattern like this to save what is being
streamed to a file::

    with open(filename, 'wb') as fd:
        for chunk in r.iter_content(chunk_size):
            fd.write(chunk)

Using ``Response.iter_content`` will handle a lot of what you would otherwise
have to handle when using ``Response.raw`` directly. When streaming a
download, the above is the preferred and recommended way to retrieve the
content.


Custom Headers
--------------

If you'd like to add HTTP headers to a request, simply pass in a ``dict`` to the
``headers`` parameter.

For example, we didn't specify our content-type in the previous example::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> headers = {'user-agent': 'my-app/0.0.1'}

    >>> r = requests.get(url, headers=headers)

Note: Custom headers are given less precedence than more specific sources of information. For instance:

* Authorization headers will be overridden if credentials are passed via the ``auth`` parameter or are specified in a ``.netrc`` accessible in the environment.
* Authorization headers will be removed if you get redirected off-host.
* Proxy-Authorization headers will be overridden by proxy credentials provided in the URL.
* Content-Length headers will be overridden when we can determine the length of the content.

Furthermore, Requests does not change its behavior at all based on which custom headers are specified. The headers are simply passed on into the final request.


More complicated POST requests
------------------------------

Typically, you want to send some form-encoded data — much like an HTML form.
To do this, simply pass a dictionary to the ``data`` argument. Your
dictionary of data will automatically be form-encoded when the request is made::

    >>> payload = {'key1': 'value1', 'key2': 'value2'}
    >>> r = requests.post("http://httpbin.org/post", data=payload)
    >>> print(r.text)
    {
      ...
      "form": {
        "key2": "value2",
        "key1": "value1"
      },
      ...
    }

There are many times that you want to send data that is not form-encoded. If
you pass in a ``string`` instead of a ``dict``, that data will be posted directly.

For example, the GitHub API v3 accepts JSON-Encoded POST/PATCH data::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> payload = {'some': 'data'}

    >>> r = requests.post(url, data=json.dumps(payload))


POST a Multipart-Encoded File
-----------------------------

Requests makes it simple to upload Multipart-encoded files::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': open('report.xls', 'rb')}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "<censored...binary...data>"
      },
      ...
    }

You can set the filename, content_type and headers explicitly:

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': ('report.xls', open('report.xls', 'rb'), 'application/vnd.ms-excel', {'Expires': '0'})}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "<censored...binary...data>"
      },
      ...
    }

If you want, you can send strings to be received as files::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': ('report.csv', 'some,data,to,send\nanother,row,to,send\n')}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "some,data,to,send\\nanother,row,to,send\\n"
      },
      ...
    }

In the event you are posting a very large file as a ``multipart/form-data``
request, you may want to stream the request. By default, ``requests`` does not
support this, but there is a separate package which does -
``requests-toolbelt``. You should read `the toolbelt's documentation
<https://toolbelt.readthedocs.org>`_ for more details about how to use it.

For sending multiple files in one request refer to the :ref:`advanced <advanced>`
section.


Response Status Codes
---------------------

We can check the response status code::

    >>> r = requests.get('http://httpbin.org/get')
    >>> r.status_code
    200

Requests also comes with a built-in status code lookup object for easy
reference::

    >>> r.status_code == requests.codes.ok
    True

If we made a bad request (a 4XX client error or 5XX server error response), we
can raise it with
:meth:`Response.raise_for_status() <requests.Response.raise_for_status>`::

    >>> bad_r = requests.get('http://httpbin.org/status/404')
    >>> bad_r.status_code
    404

    >>> bad_r.raise_for_status()
    Traceback (most recent call last):
      File "requests/models.py", line 832, in raise_for_status
        raise http_error
    requests.exceptions.HTTPError: 404 Client Error

But, since our ``status_code`` for ``r`` was ``200``, when we call
``raise_for_status()`` we get::

    >>> r.raise_for_status()
    None

All is well.


Response Headers
----------------

We can view the server's response headers using a Python dictionary::

    >>> r.headers
    {
        'content-encoding': 'gzip',
        'transfer-encoding': 'chunked',
        'connection': 'close',
        'server': 'nginx/1.0.4',
        'x-runtime': '148ms',
        'etag': '"e1ca502697e5c9317743dc078f67693f"',
        'content-type': 'application/json'
    }

The dictionary is special, though: it's made just for HTTP headers. According to
`RFC 7230 <http://tools.ietf.org/html/rfc7230#section-3.2>`_, HTTP Header names
are case-insensitive.

So, we can access the headers using any capitalization we want::

    >>> r.headers['Content-Type']
    'application/json'

    >>> r.headers.get('content-type')
    'application/json'

It is also special in that the server could have sent the same header multiple
times with different values, but requests combines them so they can be
represented in the dictionary within a single mapping, as per
`RFC 7230 <http://tools.ietf.org/html/rfc7230#section-3.2>`_:

    > A recipient MAY combine multiple header fields with the same field name
    > into one "field-name: field-value" pair, without changing the semantics
    > of the message, by appending each subsequent field value to the combined
    > field value in order, separated by a comma.

Cookies
-------

If a response contains some Cookies, you can quickly access them::

    >>> url = 'http://example.com/some/cookie/setting/url'
    >>> r = requests.get(url)

    >>> r.cookies['example_cookie_name']
    'example_cookie_value'

To send your own cookies to the server, you can use the ``cookies``
parameter::

    >>> url = 'http://httpbin.org/cookies'
    >>> cookies = dict(cookies_are='working')

    >>> r = requests.get(url, cookies=cookies)
    >>> r.text
    '{"cookies": {"cookies_are": "working"}}'


Redirection and History
-----------------------

By default Requests will perform location redirection for all verbs except
HEAD.

We can use the ``history`` property of the Response object to track redirection.

The :meth:`Response.history <requests.Response.history>` list contains the
:class:`Response <requests.Response>` objects that were created in order to
complete the request. The list is sorted from the oldest to the most recent
response.

For example, GitHub redirects all HTTP requests to HTTPS::

    >>> r = requests.get('http://github.com')
    >>> r.url
    'https://github.com/'
    >>> r.status_code
    200
    >>> r.history
    [<Response [301]>]


If you're using GET, OPTIONS, POST, PUT, PATCH or DELETE, you can disable
redirection handling with the ``allow_redirects`` parameter::

    >>> r = requests.get('http://github.com', allow_redirects=False)
    >>> r.status_code
    301
    >>> r.history
    []

If you're using HEAD, you can enable redirection as well::

    >>> r = requests.head('http://github.com', allow_redirects=True)
    >>> r.url
    'https://github.com/'
    >>> r.history
    [<Response [301]>]


Timeouts
--------

You can tell Requests to stop waiting for a response after a given number of
seconds with the ``timeout`` parameter::

    >>> requests.get('http://github.com', timeout=0.001)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    requests.exceptions.Timeout: HTTPConnectionPool(host='github.com', port=80): Request timed out. (timeout=0.001)


.. admonition:: Note

    ``timeout`` is not a time limit on the entire response download;
    rather, an exception is raised if the server has not issued a
    response for ``timeout`` seconds (more precisely, if no bytes have been
    received on the underlying socket for ``timeout`` seconds).


Errors and Exceptions
---------------------

In the event of a network problem (e.g. DNS failure, refused connection, etc),
Requests will raise a :class:`~requests.exceptions.ConnectionError` exception.

In the rare event of an invalid HTTP response, Requests will raise an
:class:`~requests.exceptions.HTTPError` exception.

If a request times out, a :class:`~requests.exceptions.Timeout` exception is
raised.

If a request exceeds the configured number of maximum redirections, a
:class:`~requests.exceptions.TooManyRedirects` exception is raised.

All exceptions that Requests explicitly raises inherit from
:class:`requests.exceptions.RequestException`.

-----------------------

Ready for more? Check out the :ref:`advanced <advanced>` section.
