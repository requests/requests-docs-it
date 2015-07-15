.. _quickstart:

Il primo impatto con Requests
=============================

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

Ora proviamo a leggere una pagina web. Ad esempio, leggiamo la timeline pubblica
di GitHub::

    >>> r = requests.get('https://api.github.com/events')

Abbiamo ottenuto un oggetto di classe :class:`Response <requests.Response>`
nella variabile ``r``. Possiamo ora
recuperare tutte le informazioni che ci servono da questo oggetto.

L'API semplicissima di Requests rende le richieste HTTP quasi ovvie.
Per esempio, questa è una HTTP POST::

    >>> r = requests.post("http://httpbin.org/post")

Bello, vero? Cosa accade per le altre tipologie di richieste HTTP: PUT, DELETE,
HEAD e OPTIONS? Sono anch'esse semplicissime::

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
Requests vi consente di passare i dati sotto forma di dizionario, usando la
keyword argument ``params``. A titolo di esempio, se volete passare
``key1=value1`` e ``key2=value2`` a ``httpbin.org/get``, usate::

    >>> payload = {'key1': 'value1', 'key2': 'value2'}
    >>> r = requests.get("http://httpbin.org/get", params=payload)

Potete printare l'URL per verificare che sia stato correttamente encoded::

    >>> print(r.url)
    http://httpbin.org/get?key2=value2&key1=value1

Le chiavi del dizionario di valore ``None`` non saranno aggiunte alla query
string dell'URL.

Per passare come valore una lista di elementi dovete segnalare il fatto che la
chiave si riferisce ad una lista appendendo ``[]`` alla chiave::

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

Requests decodificherà automaticamente il contenuto del server. La maggior
parte dei caratteri Unicode sono decodificati senza problemi.

Quando lanciate una richiesta, Requests fa delle ipotesi sull'encoding della
risposta sulla base degli header HTTP. L'encoding del testo utilizzato da
Requests si applica al contenuto di ``r.text``. Potete scoprire quale encoding
viene usato da Requests e cambiarlo usando la property ``r.encoding``::

    >>> r.encoding
    'utf-8'
    >>> r.encoding = 'ISO-8859-1'

Se modificate l'encoding, Requests userà il nuovo valore di ``r.encoding``
ogni volta che chiamate ``r.text``. Questo potrebbe esservi utile quando dovete
utilizzare una logica custom per determinare quale encoding avrà il contenuto.
Ad esempio, HTTP e XML hanno la possibilità di specificare il proprio encoding
nel body del documento. In situazioni come questa, dovreste usare
``r.content``
per ottenere l'encoding del documento e in seguito settare ``r.encoding``.
Questo permetterà
di usare ``r.text`` con l'encoding corretto.

Requests supporta anche l'impiego di encoding custom nel caso ne abbiate bisogno.
Se avete creato il vostro encoding e l'avete registrato nel modulo ``codecs``,
potete molto semplicemente usare il valore di ``r.encoding`` e Requests gestirà
direttamente il decoding della risposta per voi.

Contenuto binario delle Risposte
--------------------------------

Potete anche accedere ai byte che costituiscono il corpo delle risposte non
testuali::

    >>> r.content
    b'[{"repository":{"open_issues":0,"url":"https://github.com/...

I transfer-encodings ``gzip`` e ``deflate`` sono decodificati automaticamente.

Ad esempio, per creare un'immagine a partire dai dati binari ritornati da una
richiesta,
potete usare il seguente codice::

    >>> from PIL import Image
    >>> from StringIO import StringIO
    >>> i = Image.open(StringIO(r.content))


Contenuto JSON delle Risposte
-----------------------------

Se dovete gestire dati in formato JSON, è presente anche un decoder JSON
builtin::

    >>> import requests
    >>> r = requests.get('https://api.github.com/events')
    >>> r.json()
    [{u'repository': {u'open_issues': 0, u'url': 'https://github.com/...

Nel caso in cui la decodifica JSON fallisca, ``r.json`` solleva un'eccezione.
Ad esempio, se la risposta è un 401 (Unauthorized), l'accesso a ``r.json``
solleva un ``ValueError: No JSON object could be decoded``


Contenuto raw delle Risposte
----------------------------

Nel remoto caso in cui vi servisse il socket raw della risposta del server,
potete accedere a ``r.raw``. Se lo fate, ricordatevi di impostare ``stream=True``
nell'effettuare la richiesta.. Quindi potete fare::

    >>> r = requests.get('https://api.github.com/events', stream=True)
    >>> r.raw
    <requests.packages.urllib3.response.HTTPResponse object at 0x101194810>
    >>> r.raw.read(10)
    '\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03'

Tuttavia, in generale, dovreste usare questo pattern per salvare su un file i
dati in arrivo in streaming::

    with open(filename, 'wb') as fd:
        for chunk in r.iter_content(chunk_size):
            fd.write(chunk)

Usare ``Response.iter_content`` vi risparmierà di dover gestire ``Response.raw``
in modo diretto. Quando effettuate un download in streaming, questo metodo è
da prefersi ed è anzi quello raccomandato.


Header Custom
-------------

Se volete aggiungere header HTTP ad una richiesta, semplicemente passate un
``dict`` come valore del parametro ``headers``.

Ad esempio, nel codice precedente non abbiamo specificato il content-type::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> headers = {'user-agent': 'my-app/0.0.1'}

    >>> r = requests.get(url, headers=headers)

Nota: gli header custom hanno minore priorità rispetto a sorgenti di
informazione più specifiche. Ad esempio:

* Gli header Authorization saranno sovrascritti se le credenziali sono passate
  attraverso il parametro ``auth`` o sono specificate in un file ``.netrc``
  accessibile nell'ambiente.
* Gli header Authorization saranno rimossi se la risposta è un redirect verso
  un host diverso.
* Gli header Proxy-Authorization saranno sovrascritti con le credenziali del
  proxy fornite nell'URL.
* Gli header Content-Length saranno sovrascritti quando è possibile determinare
  la lunghezza del contenuto.

Inoltre, Requests non modifica in alcun modo il suo comportamento sulla base
degli header custom che specificate. Gli header sono semplicemente inseriti
nella richiesta conclusiva.


Richieste POST più complesse
----------------------------

Solitamente in POST si invia dati form-encoded - proprio come in un form HTML.
Per farlo, basta passare un dizionario come valore dell'argomento ``data``.
Il vostro dizionario sarà automaticamente form-encoded quando la richiesta sarà
lanciata::

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

In altri casi potreste voler inviare dati non form-encoded. Se passate una 
``string`` al posto di un ``dict``, i dati saranno POSTati direttamente.

Ad esempio, l'API di GitHub API v3 accetta dati con encoding JSON per le
richieste POST/PATCH::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> payload = {'some': 'data'}

    >>> r = requests.post(url, data=json.dumps(payload))


Postare un file Multipart-Encoded
---------------------------------

Requests rende semplice l'invio di file Multipart-encoded::

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

Potete specificare esplicitamente il nome del file, il content_type e gli
headers:

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

Se volete,  potete inviare stringhe al posto di file veri e propri::

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

Nel caso in cui dobbiate Postare un file molto grande con una richiesta 
``multipart/form-data``, dovreste inviare la richiesta in streaming.
Di default, ``requests`` non lo supporta ma esiste un package a parte per questo
- ``requests-toolbelt``. Per maggiori dettagli su come usarlo, leggete la
`documentazione di toolbelt <https://toolbelt.readthedocs.org>`_ .

Per inviare file multipli in una sola richiesta fate riferimento alla sezione
:ref:`avanzate <advanced>`.


Status code delle risposte
--------------------------

Possiamo controllare lo status code delle risposte::

    >>> r = requests.get('http://httpbin.org/get')
    >>> r.status_code
    200

Requests offre anche un oggetto built-in per fare il lookup veloce degli status
code::

    >>> r.status_code == requests.codes.ok
    True

Se una richiesta non va a buon fine (errore 4XX del client o 5XX del server),
possiamo sollevare eccezioni con 
:meth:`Response.raise_for_status() <requests.Response.raise_for_status>`::

    >>> bad_r = requests.get('http://httpbin.org/status/404')
    >>> bad_r.status_code
    404

    >>> bad_r.raise_for_status()
    Traceback (most recent call last):
      File "requests/models.py", line 832, in raise_for_status
        raise http_error
    requests.exceptions.HTTPError: 404 Client Error

Ma se lo ``status_code`` di ``r`` fosse ``200``, invocando ``raise_for_status()``
otterremmo::

    >>> r.raise_for_status()
    None

e dunque tutto OK.


Header delle risposte
---------------------

Possiamo visionare gli header delle risposte del server tramite un dizionario
Python::

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

Questo dizionario è tuttavia speciale: è fatto apposta per gli header HTTP.
Secondo la `RFC 7230 <http://tools.ietf.org/html/rfc7230#section-3.2>`_, i nomi
degli header HTTP sono case-insensitive.

Dunque, possiamo accedere agli header usando qualsiasi case::

    >>> r.headers['Content-Type']
    'application/json'

    >>> r.headers.get('content-type')
    'application/json'

Il dizionario è speciale anche perchè il server potrebbe aver inviato lo stesso
header più di una volta con valori differenti, ma requests li combina in modo
che possano essere rappresentati nel dizionario con un singolo schema di
mappatura, così come dice la `RFC 7230 <http://tools.ietf.org/html/rfc7230#section-3.2>`_:

> Un ricevente PUO' combinare più campi header aventi lo stesso nome in una 
> singola coppia "nome-campo: valore-campo" senza cambiare la semantica del
> messaggio, assegnando come valore del campo combinato l'unione di tutti
> i valori successivi separati da virgola.

Cookie
------

Se una risposta contiene dei cookie, potete ottenerli facilmente::

    >>> url = 'http://example.com/some/cookie/setting/url'
    >>> r = requests.get(url)

    >>> r.cookies['example_cookie_name']
    'example_cookie_value'

Per inviare i vostri cookie al server usate il parametro ``cookies``::

    >>> url = 'http://httpbin.org/cookies'
    >>> cookies = dict(cookies_are='working')

    >>> r = requests.get(url, cookies=cookies)
    >>> r.text
    '{"cookies": {"cookies_are": "working"}}'


Redirezione e History
---------------------

Di default Requests effettua la redirezione dell'host per tutti i verbi HTTP
ad eccezione di HEAD.


Possiamo usare la property ``history`` dell'oggetto Risposta per tracciare le
redirezioni.

La lista :meth:`Response.history <requests.Response.history>` contiene gli
oggetti di tipo :class:`Response <requests.Response>` che sono stati creati per
completare le richieste. La lista è ordinata a partire dalla risposta meno 
recente fino a quella più recente.

Per esempio, GitHub redirige tutte le richieste HTTP su HTTPS::

    >>> r = requests.get('http://github.com')
    >>> r.url
    'https://github.com/'
    >>> r.status_code
    200
    >>> r.history
    [<Response [301]>]

Se state usando GET, OPTIONS, POST, PUT, PATCH o DELETE potete disabilitare la
redirezione attraverso il parametro``allow_redirects``::

    >>> r = requests.get('http://github.com', allow_redirects=False)
    >>> r.status_code
    301
    >>> r.history
    []

Se state usando HEAD potete nello stesso modo abilitare la redirezione::

    >>> r = requests.head('http://github.com', allow_redirects=True)
    >>> r.url
    'https://github.com/'
    >>> r.history
    [<Response [301]>]


Timeout
-------

Potete fare in modo che Requests smetta di attendere una risposta dopo un
certo numero di secondi attraverso il parametro ``timeout``::

    >>> requests.get('http://github.com', timeout=0.001)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    requests.exceptions.Timeout: HTTPConnectionPool(host='github.com', port=80): Request timed out. (timeout=0.001)


.. caveat:: Nota

    ``timeout`` non è un limite di tempo per la ricezione dell'intera risposta;
    un'eccezione è sollevata se il server non ha inviato una risposta entro 
    ``timeout`` secondi (più precisamente, se nessun byte è stato ricevuto sul
    socket sottostante per ``timeout`` secondi).


Errori ed Eccezioni
-------------------

Nel caso di problemi di rete (es: il DNS non risponde, la connessione è 
rigettata, etc), Requests solleverà un'eccezione di tipo
 :class:`~requests.exceptions.ConnectionError`.

Nel raro caso di una risposta HTTP invalida, Requests solleverà un'eccezione
di tipo :class:`~requests.exceptions.HTTPError`.

Se una richiesta va in timeout, viene sollevata un'eccezione di tipo
:class:`~requests.exceptions.Timeout`.

Se una richiesta eccede il numero massimo configurato di redirezioni, è sollevata
un'eccezione di tipo :class:`~requests.exceptions.TooManyRedirects`.

Tutte le eccezioni esplicitamente sollevate da Requests ereditano dalla classe
:class:`requests.exceptions.RequestException`.

-----------------------

Siete pronti ad andare più nel dettaglio? Passate alla sezione
:ref:`avanzate <advanced>`.
