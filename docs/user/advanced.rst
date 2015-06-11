.. _advanced:

Uso avanzato di Requests
========================

Questo documento illustra alcune delle caratteristiche avanzate di Requests.

.. _session-objects:

Oggetti Session
---------------

L'oggetto Session vi consente di tenere traccia di alcuni parametri tra una
richiesta e le successive. Esso memorizza anche i cookie tra le diverse richieste
effettuate con esso.

Un oggetto Session ha tutti i metodi dell'API canonica di Requests.

Proviamo a memorizzare dei cookie tra le richieste::

    s = requests.Session()

    s.get('http://httpbin.org/cookies/set/sessioncookie/123456789')
    r = s.get("http://httpbin.org/cookies")

    print(r.text)
    # '{"cookies": {"sessioncookie": "123456789"}}'


Le sessioni possono essere usate anche per fornire dati di default ai metodi
di richiesta. Basta passare i dati alle property di un oggetto Session::

    s = requests.Session()
    s.auth = ('user', 'pass')
    s.headers.update({'x-test': 'true'})

    # sia 'x-test' che 'x-test2' sono settati
    s.get('http://httpbin.org/headers', headers={'x-test2': 'true'})


I dizionari che passate a un metodo di richiesta saranno incorporati nei valori
specifici della sessione che avete impostato. I parametri a livello di metodo
sovrascrivono i corrispondenti parametri a livello di sessione.

.. caveat:: Rimozione di un valore da un dizionario di parametri

    A volte potrebbe servirvi di rimuovere chiavi da un dizionario di parametri
    a livello di sessione. Per farlo, impostate il valore di tali chiavi a 
    ``None`` nel parametro a livello di metodo. Le chiavi saranno automaticamente
    omesse.

Tutti i valori contenuti all'interno di una sessione sono direttamente
disponibili. Per saperne di più leggete la :ref:`documentazione dell'API delle
sessioni <sessionapi>`.

.. _request-and-response-objects:

Oggetti Request e Response
--------------------------

Quando si invocano ``requests.get()`` e metodi di richiesta simili accadono
due cose importanti. Primo, state costruendo un oggetto ``Request`` che verrà
inviato ad un server per ottenere un qualche genere di risorsa. Secondo, un
oggetto ``Response`` è generato quando ``requests`` riceve una risposta dal
server. L'oggetto Response contiene tutte le informazioni ritornate dal server
e contiene anche l'oggetto ``Request`` creato in origine.
Questa è una semplice richiesta che ottiene informazioni molti importanti
dai server di Wikipedia::

    >>> r = requests.get('http://en.wikipedia.org/wiki/Monty_Python')

Se volessimo accedere agli header che il server ci ha inviato, useremmo::

    >>> r.headers
    {'content-length': '56170', 'x-content-type-options': 'nosniff', 'x-cache':
    'HIT from cp1006.eqiad.wmnet, MISS from cp1010.eqiad.wmnet', 'content-encoding':
    'gzip', 'age': '3080', 'content-language': 'en', 'vary': 'Accept-Encoding,Cookie',
    'server': 'Apache', 'last-modified': 'Wed, 13 Jun 2012 01:33:50 GMT',
    'connection': 'close', 'cache-control': 'private, s-maxage=0, max-age=0,
    must-revalidate', 'date': 'Thu, 14 Jun 2012 12:59:39 GMT', 'content-type':
    'text/html; charset=UTF-8', 'x-cache-lookup': 'HIT from cp1006.eqiad.wmnet:3128,
    MISS from cp1010.eqiad.wmnet:80'}

Mentre se volessimo accedere agli header che abbiamo inviato noi al server
dovremmo accedere prima alla request e quindi agli header della richiesta::

    >>> r.request.headers
    {'Accept-Encoding': 'identity, deflate, compress, gzip',
    'Accept': '*/*', 'User-Agent': 'python-requests/1.2.0'}

.. _prepared-requests:

Richieste preparate
-------------------

Ogni volta che ricevete un oggetto :class:`Response <requests.Response>` dalla
chamata all'API o a Session, l'attributo ``request`` contiene la 
``PreparedRequest`` che è stata utilizzata.

In alcuni casi potreste dover manipolare il corpo o gli headers (e a dire il 
vero qualsiasi altra cosa) di una richiesta prima di inviarla.
Un modo semplice per farlo è il seguente::

    from requests import Request, Session

    s = Session()
    req = Request('GET', url,
        data=data,
        headers=header
    )
    prepped = req.prepare()

    # fate qualcosa con prepped.body
    # fate qualcosa con prepped.headers

    resp = s.send(prepped,
        stream=stream,
        verify=verify,
        proxies=proxies,
        cert=cert,
        timeout=timeout
    )

    print(resp.status_code)

Visto che non dovete fare nulla di speciale con l'oggetto ``Request``, lo
preparate da subito e modificate l'oggetto ``PreparedRequest``. A questo punto
inviate questo oggetto insieme gli altri parametri che avreste passato ai
metodi ``requests.*`` o ``Session.*``.

Tuttavia, il codice qui sopra perde i vantaggi di avere un oggetto
:class:`Session <requests.Session>` in Requests. Nello specifico, lo stato a
livello di :class:`Session <requests.Session>`, come ad esempio i cookie, non
viene riportato sulla vostra richiesta. Per ottenere una
:class:`PreparedRequest <requests.PreparedRequest>` contenente quello stato, 
sostituite la chiamata a :meth:`Request.prepare()
<requests.Request.prepare>` con la chiamata a
:meth:`Session.prepare_request() <requests.Session.prepare_request>`, in questo
modo::

    from requests import Request, Session

    s = Session()
    req = Request('GET',  url,
        data=data
        headers=headers
    )

    prepped = s.prepare_request(req)

    # fate qualcosa con prepped.body
    # fate qualcosa con prepped.headers

    resp = s.send(prepped,
        stream=stream,
        verify=verify,
        proxies=proxies,
        cert=cert,
        timeout=timeout
    )

    print(resp.status_code)

.. _verification:

Verifica dei certificati SSL
----------------------------

Requests può verificare i certificati SSL per le richieste HTTPS, esattamente
come i browser. Per verificare il certificato SSL di un host potete usare 
l'argomento ``verify``::

    >>> requests.get('https://kennethreitz.com', verify=True)
    requests.exceptions.SSLError: hostname 'kennethreitz.com' doesn't match either of '*.herokuapp.com', 'herokuapp.com'

Non ho impostato SSL su quel dominio, per cui la richiesta fallisce. Ottimo.
Github tuttavia ha SSL::

    >>> requests.get('https://github.com', verify=True)
    <Response [200]>

Potete specificare per ``verify`` il path ad un file CA_BUNDLE contenente
certificati di una Certification Authority di fiducia. La lista di bundle delle
CA di fiducia può essere anche secificata attraverso la variabile di ambiente 
``REQUESTS_CA_BUNDLE``.

Requests può anche ignorare la verifica dei certificati SSL se impostata 
``verify`` a False.

::

    >>> requests.get('https://kennethreitz.com', verify=False)
    <Response [200]>

Di default, ``verify`` è True. L'opzione ``verify`` si applica solo a certi host.

Potete anche specificare un certificato locale da usare come verifica client
side, sia sotto forma di file locale (contenente la chiave privata e il 
certificato) che di tupla contenente i path ad entrambi i file::

    >>> requests.get('https://kennethreitz.com', cert=('/path/server.crt', '/path/key'))
    <Response [200]>

Se specificate un path errato o un certificato invalido::

    >>> requests.get('https://kennethreitz.com', cert='/wrong_path/server.pem')
    SSLError: [Errno 336265225] _ssl.c:347: error:140B0009:SSL routines:SSL_CTX_use_PrivateKey_file:PEM lib

.. _body-content-workflow:

Workflow di lettura del corpo delle risposte
--------------------------------------------

Di default, quando lanciate una richiesta il corpo della risposta è scaricato
immediatamente. Potete modificare questo comportamento e deferire il download
del corpo della risposta fino a quando non leggete il valore dell'attributo 
:class:`Response.content <requests.Response.content>` utilizzando il parametro
 ``stream``::

    tarball_url = 'https://github.com/kennethreitz/requests/tarball/master'
    r = requests.get(tarball_url, stream=True)

A questo punto solo gli header sono stati scaricati per la risposta e la
connessione rimane aperta, dunque lasciando il recupero del contenuto alla nostra
volontà::

    if int(r.headers['content-length']) < TOO_LONG:
      content = r.content
      ...

Potete ancora di più controllare il workflow usando i metodi :class:`Response.iter_content <requests.Response.iter_content>`
e :class:`Response.iter_lines <requests.Response.iter_lines>`.
In alternativa, potete leggere il corpo della risposta dalla sottostante urllib3
:class:`urllib3.HTTPResponse <urllib3.response.HTTPResponse>` disponibile 
invocando :class:`Response.raw <requests.Response.raw>`.

Se impostate ``stream`` a ``True`` quando inviate una richiesta, Requests non
può liberare la connessione in modo che ritorni nel pool fino a che non fruite
dei dati o non chiamate :class:`Response.close <requests.Response.close>`.
Questo comportamento può portare ad un uso inefficiente delle connessioni. Se
vi capita sovente di leggere solo parzialmente il corpo delle richieste (o di
non leggerlo per nulla) quando ``stream=True``, dovreste prendere in
considerazione l'uso di ``contextlib.closing`` (`documentato qui`_), in questo
modo::

    from contextlib import closing

    with closing(requests.get('http://httpbin.org/get', stream=True)) as r:
        # Usate la risposta

.. _`documented here`: http://docs.python.org/2/library/contextlib.html#contextlib.closing

.. _keep-alive:

Keep-Alive
----------

Grandi notizie — grazie a urllib3, il keep-alive delle connessioni è al 100%
automatico all'interno di una sessione!
Ogni richiesta che inviate all'interno di una sessione userà automaticamente la
stessa connessione!

Notate che le connessioni sono rilasciate e mess nel pool per il riuso solo
quando tutti i dati del corpo delle richieste sono stati letti; dunque 
accertatevi o di impostare ``stream`` a ``False`` o di leggere il ``content``
dei vostri oggetti ``Response``.

.. _streaming-uploads:

Upload in streaming
-------------------

Requests supporta gli upload in streaming, il che consente di inviare grossi
flussi di dati o grandi file senza doverli leggere in memoria. Per lanciare
un upload in streaming, vi basta fornire un file-like object come corpo della
richiesta::

    with open('massive-body', 'rb') as f:
        requests.post('http://some.url/streamed', data=f)

.. _chunk-encoding:

Richieste Chunk-Encoded
-----------------------

Requests supporta anche il trasferimento in chunks sia per le richieste in
uscita che per le risposte in ingresso.
Per inviare una richiesta chunk-encoded, dovete semplicemente fornire un
generatore (o qualsiasi iteratore di lunghezza indefinita) come corpo della
richiesta::


    def gen():
        yield 'hi'
        yield 'there'

    requests.post('http://some.url/chunked', data=gen())


.. _multipart:

POST-are più file Multipart-Encoded
-----------------------------------

Potete inviare più file in una singola richiesta. Ad esempio, immaginate di
voler uploadare dei file immagine da un form HTML con un campo file multiplo
di nome 'images':

    <input type="file" name="images" multiple="true" required="true"/>

Per fare lo stesso con Requests vi basta inserire i file in una lista di tuple
nella forma (nome_campo_del_form, tupla_con_info_sul_file):

    >>> url = 'http://httpbin.org/post'
    >>> multiple_files = [('images', ('foo.png', open('foo.png', 'rb'), 'image/png')),
                          ('images', ('bar.png', open('bar.png', 'rb'), 'image/png'))]
    >>> r = requests.post(url, files=multiple_files)
    >>> r.text
    {
      ...
      'files': {'images': 'data:image/png;base64,iVBORw ....'}
      'Content-Type': 'multipart/form-data; boundary=3131623adb2043caaeb5538cc7aa0b3a',
      ...
    }

.. _event-hooks:

Hook per gli eventi
-------------------

Requests ha un sistema di hook che potete usare per manipolare le fasi del
processo di richiesta o gestire eventi di segnalazione.

Hook disponibili:

``response``:
    La risposta generata a partire da una richiesta.

Potete impostare una funzione callback ad ogni singola richiesta, passando un
dizionario ``{hook_name: callback_function}`` al parametro ``hooks`` della
richiesta::

    hooks=dict(response=print_url)

La funzione ``callback_function`` riceverà come primo argomento la richiesta.

::

    def print_url(r, *args, **kwargs):
        print(r.url)

Se accade un errore durante l'esecuzione della vostra callback, viene sollevato
un warning.
Se la callback ritorna un valore, il contratto implicito è usare questo valore
per rimpiazzare i dati che sono stati passati come argomento. Se la callback
non ritorna nulla, nessuna azione è intrapresa.

Printiamo a runtime alcuni metodi di richiesta::

    >>> requests.get('http://httpbin.org', hooks=dict(response=print_url))
    http://httpbin.org
    <Response [200]>

.. _custom-auth:

Custom Authentication
---------------------

Requests allows you to use specify your own authentication mechanism.

Any callable which is passed as the ``auth`` argument to a request method will
have the opportunity to modify the request before it is dispatched.

Authentication implementations are subclasses of ``requests.auth.AuthBase``,
and are easy to define. Requests provides two common authentication scheme
implementations in ``requests.auth``: ``HTTPBasicAuth`` and ``HTTPDigestAuth``.

Let's pretend that we have a web service that will only respond if the
``X-Pizza`` header is set to a password value. Unlikely, but just go with it.

::

    from requests.auth import AuthBase

    class PizzaAuth(AuthBase):
        """Attaches HTTP Pizza Authentication to the given Request object."""
        def __init__(self, username):
            # setup any auth-related data here
            self.username = username

        def __call__(self, r):
            # modify and return the request
            r.headers['X-Pizza'] = self.username
            return r

Then, we can make a request using our Pizza Auth::

    >>> requests.get('http://pizzabin.org/admin', auth=PizzaAuth('kenneth'))
    <Response [200]>

.. _streaming-requests:

Streaming Requests
------------------

With :class:`requests.Response.iter_lines()` you can easily
iterate over streaming APIs such as the `Twitter Streaming
API <https://dev.twitter.com/streaming/overview>`_. Simply
set ``stream`` to ``True`` and iterate over the response with
:class:`~requests.Response.iter_lines()`::

    import json
    import requests

    r = requests.get('http://httpbin.org/stream/20', stream=True)

    for line in r.iter_lines():

        # filter out keep-alive new lines
        if line:
            print(json.loads(line))

.. warning::

    :class:`~requests.Response.iter_lines()` is not reentrant safe.
    Calling this method multiple times causes some of the received data
    being lost. In case you need to call it from multiple places, use
    the resulting iterator object instead::

        lines = r.iter_lines()
        # Save the first line for later or just skip it
        first_line = next(lines)
        for line in lines:
            print(line)

.. _proxies:

Proxies
-------

If you need to use a proxy, you can configure individual requests with the
``proxies`` argument to any request method::

    import requests

    proxies = {
      "http": "http://10.10.1.10:3128",
      "https": "http://10.10.1.10:1080",
    }

    requests.get("http://example.org", proxies=proxies)

You can also configure proxies by setting the environment variables
``HTTP_PROXY`` and ``HTTPS_PROXY``.

::

    $ export HTTP_PROXY="http://10.10.1.10:3128"
    $ export HTTPS_PROXY="http://10.10.1.10:1080"
    $ python
    >>> import requests
    >>> requests.get("http://example.org")

To use HTTP Basic Auth with your proxy, use the `http://user:password@host/` syntax::

    proxies = {
        "http": "http://user:pass@10.10.1.10:3128/",
    }

Note that proxy URLs must include the scheme.

.. _compliance:

Compliance
----------

Requests is intended to be compliant with all relevant specifications and
RFCs where that compliance will not cause difficulties for users. This
attention to the specification can lead to some behaviour that may seem
unusual to those not familiar with the relevant specification.

Encodings
^^^^^^^^^

When you receive a response, Requests makes a guess at the encoding to
use for decoding the response when you access the :attr:`Response.text
<requests.Response.text>` attribute. Requests will first check for an
encoding in the HTTP header, and if none is present, will use `chardet
<http://pypi.python.org/pypi/chardet>`_ to attempt to guess the encoding.

The only time Requests will not do this is if no explicit charset
is present in the HTTP headers **and** the ``Content-Type``
header contains ``text``. In this situation, `RFC 2616
<http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.7.1>`_ specifies
that the default charset must be ``ISO-8859-1``. Requests follows the
specification in this case. If you require a different encoding, you can
manually set the :attr:`Response.encoding <requests.Response.encoding>`
property, or use the raw :attr:`Response.content <requests.Response.content>`.

.. _http-verbs:

HTTP Verbs
----------

Requests provides access to almost the full range of HTTP verbs: GET, OPTIONS,
HEAD, POST, PUT, PATCH and DELETE. The following provides detailed examples of
using these various verbs in Requests, using the GitHub API.

We will begin with the verb most commonly used: GET. HTTP GET is an idempotent
method that returns a resource from a given URL. As a result, it is the verb
you ought to use when attempting to retrieve data from a web location. An
example usage would be attempting to get information about a specific commit
from GitHub. Suppose we wanted commit ``a050faf`` on Requests. We would get it
like so::

    >>> import requests
    >>> r = requests.get('https://api.github.com/repos/kennethreitz/requests/git/commits/a050faf084662f3a352dd1a941f2c7c9f886d4ad')

We should confirm that GitHub responded correctly. If it has, we want to work
out what type of content it is. Do this like so::

    >>> if r.status_code == requests.codes.ok:
    ...     print(r.headers['content-type'])
    ...
    application/json; charset=utf-8

So, GitHub returns JSON. That's great, we can use the :meth:`r.json
<requests.Response.json>` method to parse it into Python objects.

::

    >>> commit_data = r.json()
    >>> print(commit_data.keys())
    [u'committer', u'author', u'url', u'tree', u'sha', u'parents', u'message']
    >>> print(commit_data[u'committer'])
    {u'date': u'2012-05-10T11:10:50-07:00', u'email': u'me@kennethreitz.com', u'name': u'Kenneth Reitz'}
    >>> print(commit_data[u'message'])
    makin' history

So far, so simple. Well, let's investigate the GitHub API a little bit. Now,
we could look at the documentation, but we might have a little more fun if we
use Requests instead. We can take advantage of the Requests OPTIONS verb to
see what kinds of HTTP methods are supported on the url we just used.

::

    >>> verbs = requests.options(r.url)
    >>> verbs.status_code
    500

Uh, what? That's unhelpful! Turns out GitHub, like many API providers, don't
actually implement the OPTIONS method. This is an annoying oversight, but it's
OK, we can just use the boring documentation. If GitHub had correctly
implemented OPTIONS, however, they should return the allowed methods in the
headers, e.g.

::

    >>> verbs = requests.options('http://a-good-website.com/api/cats')
    >>> print(verbs.headers['allow'])
    GET,HEAD,POST,OPTIONS

Turning to the documentation, we see that the only other method allowed for
commits is POST, which creates a new commit. As we're using the Requests repo,
we should probably avoid making ham-handed POSTS to it. Instead, let's play
with the Issues feature of GitHub.

This documentation was added in response to Issue #482. Given that this issue
already exists, we will use it as an example. Let's start by getting it.

::

    >>> r = requests.get('https://api.github.com/repos/kennethreitz/requests/issues/482')
    >>> r.status_code
    200
    >>> issue = json.loads(r.text)
    >>> print(issue[u'title'])
    Feature any http verb in docs
    >>> print(issue[u'comments'])
    3

Cool, we have three comments. Let's take a look at the last of them.

::

    >>> r = requests.get(r.url + u'/comments')
    >>> r.status_code
    200
    >>> comments = r.json()
    >>> print(comments[0].keys())
    [u'body', u'url', u'created_at', u'updated_at', u'user', u'id']
    >>> print(comments[2][u'body'])
    Probably in the "advanced" section

Well, that seems like a silly place. Let's post a comment telling the poster
that he's silly. Who is the poster, anyway?

::

    >>> print(comments[2][u'user'][u'login'])
    kennethreitz

OK, so let's tell this Kenneth guy that we think this example should go in the
quickstart guide instead. According to the GitHub API doc, the way to do this
is to POST to the thread. Let's do it.

::

    >>> body = json.dumps({u"body": u"Sounds great! I'll get right on it!"})
    >>> url = u"https://api.github.com/repos/kennethreitz/requests/issues/482/comments"
    >>> r = requests.post(url=url, data=body)
    >>> r.status_code
    404

Huh, that's weird. We probably need to authenticate. That'll be a pain, right?
Wrong. Requests makes it easy to use many forms of authentication, including
the very common Basic Auth.

::

    >>> from requests.auth import HTTPBasicAuth
    >>> auth = HTTPBasicAuth('fake@example.com', 'not_a_real_password')
    >>> r = requests.post(url=url, data=body, auth=auth)
    >>> r.status_code
    201
    >>> content = r.json()
    >>> print(content[u'body'])
    Sounds great! I'll get right on it.

Brilliant. Oh, wait, no! I meant to add that it would take me a while, because
I had to go feed my cat. If only I could edit this comment! Happily, GitHub
allows us to use another HTTP verb, PATCH, to edit this comment. Let's do
that.

::

    >>> print(content[u"id"])
    5804413
    >>> body = json.dumps({u"body": u"Sounds great! I'll get right on it once I feed my cat."})
    >>> url = u"https://api.github.com/repos/kennethreitz/requests/issues/comments/5804413"
    >>> r = requests.patch(url=url, data=body, auth=auth)
    >>> r.status_code
    200

Excellent. Now, just to torture this Kenneth guy, I've decided to let him
sweat and not tell him that I'm working on this. That means I want to delete
this comment. GitHub lets us delete comments using the incredibly aptly named
DELETE method. Let's get rid of it.

::

    >>> r = requests.delete(url=url, auth=auth)
    >>> r.status_code
    204
    >>> r.headers['status']
    '204 No Content'

Excellent. All gone. The last thing I want to know is how much of my ratelimit
I've used. Let's find out. GitHub sends that information in the headers, so
rather than download the whole page I'll send a HEAD request to get the
headers.

::

    >>> r = requests.head(url=url, auth=auth)
    >>> print(r.headers)
    ...
    'x-ratelimit-remaining': '4995'
    'x-ratelimit-limit': '5000'
    ...

Excellent. Time to write a Python program that abuses the GitHub API in all
kinds of exciting ways, 4995 more times.

.. _link-headers:

Link Headers
------------

Many HTTP APIs feature Link headers. They make APIs more self describing and
discoverable.

GitHub uses these for `pagination <http://developer.github.com/v3/#pagination>`_
in their API, for example::

    >>> url = 'https://api.github.com/users/kennethreitz/repos?page=1&per_page=10'
    >>> r = requests.head(url=url)
    >>> r.headers['link']
    '<https://api.github.com/users/kennethreitz/repos?page=2&per_page=10>; rel="next", <https://api.github.com/users/kennethreitz/repos?page=6&per_page=10>; rel="last"'

Requests will automatically parse these link headers and make them easily consumable::

    >>> r.links["next"]
    {'url': 'https://api.github.com/users/kennethreitz/repos?page=2&per_page=10', 'rel': 'next'}

    >>> r.links["last"]
    {'url': 'https://api.github.com/users/kennethreitz/repos?page=7&per_page=10', 'rel': 'last'}

.. _transport-adapters:

Transport Adapters
------------------

As of v1.0.0, Requests has moved to a modular internal design. Part of the
reason this was done was to implement Transport Adapters, originally
`described here`_. Transport Adapters provide a mechanism to define interaction
methods for an HTTP service. In particular, they allow you to apply per-service
configuration.

Requests ships with a single Transport Adapter, the :class:`HTTPAdapter
<requests.adapters.HTTPAdapter>`. This adapter provides the default Requests
interaction with HTTP and HTTPS using the powerful `urllib3`_ library. Whenever
a Requests :class:`Session <requests.Session>` is initialized, one of these is
attached to the :class:`Session <requests.Session>` object for HTTP, and one
for HTTPS.

Requests enables users to create and use their own Transport Adapters that
provide specific functionality. Once created, a Transport Adapter can be
mounted to a Session object, along with an indication of which web services
it should apply to.

::

    >>> s = requests.Session()
    >>> s.mount('http://www.github.com', MyAdapter())

The mount call registers a specific instance of a Transport Adapter to a
prefix. Once mounted, any HTTP request made using that session whose URL starts
with the given prefix will use the given Transport Adapter.

Many of the details of implementing a Transport Adapter are beyond the scope of
this documentation, but take a look at the next example for a simple SSL use-
case. For more than that, you might look at subclassing
``requests.adapters.BaseAdapter``.

Example: Specific SSL Version
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Requests team has made a specific choice to use whatever SSL version is
default in the underlying library (`urllib3`_). Normally this is fine, but from
time to time, you might find yourself needing to connect to a service-endpoint
that uses a version that isn't compatible with the default.

You can use Transport Adapters for this by taking most of the existing
implementation of HTTPAdapter, and adding a parameter *ssl_version* that gets
passed-through to `urllib3`. We'll make a TA that instructs the library to use
SSLv3:

::

    import ssl

    from requests.adapters import HTTPAdapter
    from requests.packages.urllib3.poolmanager import PoolManager


    class Ssl3HttpAdapter(HTTPAdapter):
        """"Transport adapter" that allows us to use SSLv3."""

        def init_poolmanager(self, connections, maxsize, block=False):
            self.poolmanager = PoolManager(num_pools=connections,
                                           maxsize=maxsize,
                                           block=block,
                                           ssl_version=ssl.PROTOCOL_SSLv3)

.. _`described here`: http://www.kennethreitz.org/essays/the-future-of-python-http
.. _`urllib3`: https://github.com/shazow/urllib3

.. _blocking-or-nonblocking:

Blocking Or Non-Blocking?
-------------------------

With the default Transport Adapter in place, Requests does not provide any kind
of non-blocking IO. The :attr:`Response.content <requests.Response.content>`
property will block until the entire response has been downloaded. If
you require more granularity, the streaming features of the library (see
:ref:`streaming-requests`) allow you to retrieve smaller quantities of the
response at a time. However, these calls will still block.

If you are concerned about the use of blocking IO, there are lots of projects
out there that combine Requests with one of Python's asynchronicity frameworks.
Two excellent examples are `grequests`_ and `requests-futures`_.

.. _`grequests`: https://github.com/kennethreitz/grequests
.. _`requests-futures`: https://github.com/ross/requests-futures

.. _timeouts:

Timeouts
--------

Most requests to external servers should have a timeout attached, in case the
server is not responding in a timely manner. Without a timeout, your code may
hang for minutes or more.

The **connect** timeout is the number of seconds Requests will wait for your
client to establish a connection to a remote machine (corresponding to the
`connect()`_) call on the socket. It's a good practice to set connect timeouts
to slightly larger than a multiple of 3, which is the default `TCP packet
retransmission window <http://www.hjp.at/doc/rfc/rfc2988.txt>`_.

Once your client has connected to the server and sent the HTTP request, the
**read** timeout is the number of seconds the client will wait for the server
to send a response. (Specifically, it's the number of seconds that the client
will wait *between* bytes sent from the server. In 99.9% of cases, this is the
time before the server sends the first byte).

If you specify a single value for the timeout, like this::

    r = requests.get('https://github.com', timeout=5)

The timeout value will be applied to both the ``connect`` and the ``read``
timeouts. Specify a tuple if you would like to set the values separately::

    r = requests.get('https://github.com', timeout=(3.05, 27))

If the remote server is very slow, you can tell Requests to wait forever for
a response, by passing None as a timeout value and then retrieving a cup of
coffee.

.. code-block:: python

    r = requests.get('https://github.com', timeout=None)

.. _`connect()`: http://linux.die.net/man/2/connect

.. _ca-certificates:

CA Certificates
---------------

By default Requests bundles a set of root CAs that it trusts, sourced from the
`Mozilla trust store`_. However, these are only updated once for each Requests
version. This means that if you pin a Requests version your certificates can
become extremely out of date.

From Requests version 2.4.0 onwards, Requests will attempt to use certificates
from `certifi`_ if it is present on the system. This allows for users to update
their trusted certificates without having to change the code that runs on their
system.

For the sake of security we recommend upgrading certifi frequently!

.. _certifi: http://certifi.io/
.. _Mozilla trust store: https://hg.mozilla.org/mozilla-central/raw-file/tip/security/nss/lib/ckfw/builtins/certdata.txt
