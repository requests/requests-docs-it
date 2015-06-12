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

Autenticazione custom
---------------------

Requests vi permette di specificare un meccanismo di autenticazione custom.

Ogni callable che passerete all'argomento ``auth`` di un metodo di richiesta 
avrà la possibilità di modificare la richiesta prima che questa venga inviata.

Le implementazioni del meccanismo di autenticazione devono essere sottoclassi di 
``requests.auth.AuthBase``, e sono semplici da realizzare. Requests mette a 
disposizione in ``requests.auth`` due schemi di autenticazione di uso comune:
``HTTPBasicAuth`` e ``HTTPDigestAuth``.

Mettiamoci nell'ipotetico caso di avere un web service che risponde solamente
se l'header ``X-Pizza`` contiene una username. Abbastanza improbabile, ma 
andiamo avanti.

::

    from requests.auth import AuthBase

    class PizzaAuth(AuthBase):
        """Aggiunge l'Autenticazione HTTP Pizza a questa istanza di Request."""
        def __init__(self, username):
            # setup any auth-related data here
            self.username = username

        def __call__(self, r):
            # modifico e ritorno la richiesta
            r.headers['X-Pizza'] = self.username
            return r

A questo punto possiamo fare richieste usando la nostra classe Pizza Auth::

    >>> requests.get('http://pizzabin.org/admin', auth=PizzaAuth('kenneth'))
    <Response [200]>

.. _streaming-requests:

Richieste in streaming
----------------------

Con :class:`requests.Response.iter_lines()` potete iterare in maniera semplice
su API in streaming come l'`API Streaming di Twitter <https://dev.twitter.com/streaming/overview>`_.
Basta impostare ``stream`` a ``True`` e iterare sulla risposta con
:class:`~requests.Response.iter_lines()`::

    import json
    import requests

    r = requests.get('http://httpbin.org/stream/20', stream=True)

    for line in r.iter_lines():

        # printa le nuove righe in streaming
        if line:
            print(json.loads(line))

.. caveat::

    :class:`~requests.Response.iter_lines()` non è un metodo rientrante.
    Invocarlo più volte provoca la perdita di parte dei dati ricevuti. Nel caso 
    in cui serva invocarlo da più punti del vostro codice, usate piuttosto
    l'iteratore che risulta dalla sua invocazione::

        lines = r.iter_lines()
        # Memorizza la prima riga per dopo o skippala
        first_line = next(lines)
        for line in lines:
            print(line)

.. _proxies:

Proxy
----- 

Se dovete utilizzare un proxy, potete configurare ogni singolo metodo di 
richiesta con l'argomento ``proxies``::

    import requests

    proxies = {
      "http": "http://10.10.1.10:3128",
      "https": "http://10.10.1.10:1080",
    }

    requests.get("http://example.org", proxies=proxies)

Potete anche configurare i proxy attraverso le variabili di ambiente
``HTTP_PROXY`` e ``HTTPS_PROXY``.

::

    $ export HTTP_PROXY="http://10.10.1.10:3128"
    $ export HTTPS_PROXY="http://10.10.1.10:1080"
    $ python
    >>> import requests
    >>> requests.get("http://example.org")

Per usare la HTTP Basic Authentication con il vostro proxy, servitevi della
sintassi `http://user:password@host/`::

    proxies = {
        "http": "http://user:pass@10.10.1.10:3128/",
    }

Notate che gli URL dei proxy devono includere lo schema.

.. _compliance:

Conformità
----------

Requests è pensato per essere conforme con tutte le specifiche e le RFC rilevanti
laddove tale conformità non causi difficoltà d'utilizzo per gli utenti. Questa
attenzione alla specifica può portare ad un comportamento che potrebbe sembrare
inusuale a coloro che non sono familiari con le specifiche stesse.

Encoding
^^^^^^^^

Quando ricevete una risposta, Requests cerca di capire l'encoding da usare per 
decodificarla quando accedete l'attributo :attr:`Response.text
<requests.Response.text>`. Requests dapprima cercherà un encoding specifico negli
header HTTP, e se non ne trova, allora si servirà `chardet
<http://pypi.python.org/pypi/chardet>`_ per cercare di indovindare l'encoding.

L'unica situazione in cui Requests non seguirà questa procedura è quando non 
è specificato un charset esplicito negli header HTTP **e** l'header
``Content-Type`` contiene ``text``. In tale situazione, la `RFC 2616
<http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.7.1>`_ specifica
che il charset di default deve essere ``ISO-8859-1``. Requests si conforma alla
specifica in questo caso. Se avete bisogno di un encoding diverso, potete
settare manualmente la property :attr:`Response.encoding <requests.Response.encoding>`
o usare l'oggetto :attr:`Response.content <requests.Response.content>` raw.

.. _http-verbs:

Verbi HTTP
----------

Requests dà accesso a quasi tutto il range di verbi HTTP: GET, OPTIONS,
HEAD, POST, PUT, PATCH e DELETE. Di seguito vengono illustrati esempi dettagliati
di come usare questi verbi in Requests, usando l'API di GitHub.

Cominceremo con il verbo di più comune utilizzo: GET. HTTP GET è un metodo
idempotente che ritorna una risorsa da un URL specifico. Dunque è il verbo che
dovete usare quando cercate di ottenere dati da un indirizzo web. Un esempio
d'uso potrebbe essere recuperare informazioni su una specifica commit da
GitHub. Immaginate di volere la commit ``a050faf`` con Requests. La potremmo
recuperare così::

    >>> import requests
    >>> r = requests.get('https://api.github.com/repos/kennethreitz/requests/git/commits/a050faf084662f3a352dd1a941f2c7c9f886d4ad')

Dovremmo controllare se GitHub ha risposto correttamente. Se sì, wogliamo
capire quale tipo di contenuto ha ritornato. Facciamo così::

    >>> if r.status_code == requests.codes.ok:
    ...     print(r.headers['content-type'])
    ...
    application/json; charset=utf-8

Dunque GitHub ritorna JSON. Grandioso, possiamo usare il metodo :meth:`r.json
<requests.Response.json>` per convertirlo in un oggetto Python.

::

    >>> commit_data = r.json()
    >>> print(commit_data.keys())
    [u'committer', u'author', u'url', u'tree', u'sha', u'parents', u'message']
    >>> print(commit_data[u'committer'])
    {u'date': u'2012-05-10T11:10:50-07:00', u'email': u'me@kennethreitz.com', u'name': u'Kenneth Reitz'}
    >>> print(commit_data[u'message'])
    makin' history

Finora tutto semplice. Ora investighiamo l'API di GitHub un po' più nel dettaglio.
Potremmo guardare la documentazione, ma sarebbe più divertente usare Requests.
Possiamo servirci del verbo OPTIONS per vedere quali metodi HTTP sono consentiti
sull'URL che abbiamo appena usato.

::

    >>> verbs = requests.options(r.url)
    >>> verbs.status_code
    500

Cosa? Questo non ci serve! Sembra che GitHub, come molti provider di API, non
implementi il metodo OPTIONS. E' una cosa noiosa, ma OK, possiamo sempre
annoiarci a leggere la documentazione. Se GitHub avesse implementato OPTIONS,
avrebbe ritornato negli header i metodi consentiti. As esempio

::

    >>> verbs = requests.options('http://a-good-website.com/api/cats')
    >>> print(verbs.headers['allow'])
    GET,HEAD,POST,OPTIONS

Se leggiamo la documentazione, notiamo che l'unico altro metodo consentito
sulle commit è POST, che crea una nuova commit. Dato che stiamo usando il repo
di Requests, dovremmo evitare di creare a mano delle nuovo commit. Usiamo invece
le Issue di GitHub.

Il documento che state leggendo è stato aggiunto in risposta alla Issue #482.
Dal momento che tale issue esiste già, la useremo come esempio. Cominciamo con
il leggerla.

::

    >>> r = requests.get('https://api.github.com/repos/kennethreitz/requests/issues/482')
    >>> r.status_code
    200
    >>> issue = json.loads(r.text)
    >>> print(issue[u'title'])
    Feature any http verb in docs
    >>> print(issue[u'comments'])
    3

Grande, abbiamo tre commenti. Diamo un'occhiata all'ultimo.

::

    >>> r = requests.get(r.url + u'/comments')
    >>> r.status_code
    200
    >>> comments = r.json()
    >>> print(comments[0].keys())
    [u'body', u'url', u'created_at', u'updated_at', u'user', u'id']
    >>> print(comments[2][u'body'])
    Probably in the "advanced" section

Bè, non sembra la migliore delle sezioni! Postiamo un nuovo commento per dire
all'autore del commento che è un cretino. Ma... chi è l'autore?

::

    >>> print(comments[2][u'user'][u'login'])
    kennethreitz

OK, diciamo a questo tizio di nome Kenneth che crediamo che l'esempio debba andare
nella sezione quickstart. La documentazione dell'API di GitHub, il modo giusto
è POST-are su un thread. Facciamolo

::

    >>> body = json.dumps({u"body": u"Sounds great! I'll get right on it!"})
    >>> url = u"https://api.github.com/repos/kennethreitz/requests/issues/482/comments"
    >>> r = requests.post(url=url, data=body)
    >>> r.status_code
    404

Mmmm, strano. Probabilmente dobbiamo autenticarci. Sarà un'impresa, vero? No,
sbagliato. Requests rende facile usare varie forme di autenticazione, inclusa
la comunissima HTTP Basic Authentication.

::

    >>> from requests.auth import HTTPBasicAuth
    >>> auth = HTTPBasicAuth('fake@example.com', 'not_a_real_password')
    >>> r = requests.post(url=url, data=body, auth=auth)
    >>> r.status_code
    201
    >>> content = r.json()
    >>> print(content[u'body'])
    Sounds great! I'll get right on it.

Fico! No, aspettate un attimo! Volevamo anche aggiungere che ci metteremo un po'
perchè dobbiamo dare da mangiare al nostro gatto. Se solo potessimo modificare
quel commento! Con piacere, GitHub ci permette di usare un altro verbo HTTP,
PATCH, per modificare quel commento. Facciamolo.

::

    >>> print(content[u"id"])
    5804413
    >>> body = json.dumps({u"body": u"Sounds great! I'll get right on it once I feed my cat."})
    >>> url = u"https://api.github.com/repos/kennethreitz/requests/issues/comments/5804413"
    >>> r = requests.patch(url=url, data=body, auth=auth)
    >>> r.status_code
    200

Eccellente. Ora, per continuare a torturare questo tizio di nome Kenneth, 
decidiamo di lasciarlo all'oscuro del fatto che stiamo lavorando sulla issue. 
Questo significa che vogliamo cancellare il commento usando il metodo DELETE,
dal nome incredibilmente azzeccato. Buttiamo via il commento.

::

    >>> r = requests.delete(url=url, auth=auth)
    >>> r.status_code
    204
    >>> r.headers['status']
    '204 No Content'

Perfetto. E' sparito. L'ultima cosa che vorremmo sapere è quanto siamo vicini al
numero limite di chiamate all'API. Vediamolo. GitHub invia questa informazione
negli headers, dunque al posto di scaricare un'intera pagina manderemo una
richiesta di tipo HEAD su di essa per recuperare solo gli headers.

::

    >>> r = requests.head(url=url, auth=auth)
    >>> print(r.headers)
    ...
    'x-ratelimit-remaining': '4995'
    'x-ratelimit-limit': '5000'
    ...

Eccellente. E' il momento di scrivere un programma Python che abusi dell'API
di GitHub in un sacco di modi divertenti, 4995 altre volte.

.. _link-headers:

Header Link
-----------

Molte API web usano header di tipo Link. Questi rendono le API più esplicite
da comprendere ed esplorabili.

L'API di GitHub li usa per la `paginazione <http://developer.github.com/v3/#pagination>`_
dei dati, ad esempio::

    >>> url = 'https://api.github.com/users/kennethreitz/repos?page=1&per_page=10'
    >>> r = requests.head(url=url)
    >>> r.headers['link']
    '<https://api.github.com/users/kennethreitz/repos?page=2&per_page=10>; rel="next", <https://api.github.com/users/kennethreitz/repos?page=6&per_page=10>; rel="last"'

Requests leggerà automaticamente gli header Link e li renderà facilmente usabili::

    >>> r.links["next"]
    {'url': 'https://api.github.com/users/kennethreitz/repos?page=2&per_page=10', 'rel': 'next'}

    >>> r.links["last"]
    {'url': 'https://api.github.com/users/kennethreitz/repos?page=7&per_page=10', 'rel': 'last'}

.. _transport-adapters:

Adapter di Trasporto
--------------------

Dalla versione v1.0.0, Requests ha adottato un design interno modulare. Uno dei
motivi alla base di questo è l'implementazione di Adapter di Trasporto, in origine
`descritti qui`_. Gli Adapter di Trasporto forniscono un meccanismo per definire
i metodi di interazione con un servizio HTTP. Nello specifico, permettono di
utilizzare una configurazione specifica per ogni servizio.

Requests contiene un singolo Adapter di Trasporto, :class:`HTTPAdapter
<requests.adapters.HTTPAdapter>`. Questo adapter implementa l'interazione di
default di Requests con HTTP e HTTPS servendosi della poderosa libreria
`urllib3`_ library. Ogni volta che una :class:`Session <requests.Session>`
è inizializzata, un adapter è allegato all'oggetto :class:`Session <requests.Session>`
per HTTP e un secondo adapter per HTTPS.

Requests consente agli utenti di creare e usare i propri Adapter di Trasporto per
esporre funzionalità custom. Una volta creato, un Adapter di Trasporto può essere
montato su un oggetto Session insieme all'indicazione di quali servizi web si
dovrebbe applicare.

::

    >>> s = requests.Session()
    >>> s.mount('http://www.github.com', MyAdapter())

Questa chiamata registra un'istanza specifica di un Adapter di Trasporto ad un
prefisso. Una volta montato, ogni richiesta HTTP fatta usando la sessione il
cui URL inizia con il prefisso specificato si servirà dell'Adapter specificato.

Molti dei dettagli implementativi di un Adapter di Trasporto sono oltre lo
scopo di questa documentazione, ma date un'occhiata al prossimo esempio per
un semplice caso d'uso con SSL. Se volete andare ancora oltre, dovreste creare
una sottoclasse di ``requests.adapters.BaseAdapter``.

Esempio: usare una versione specifica di SSL
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Il team di Requests ha fatto la scelta specifica di usare qualsiasi versione di
SSL si trovi di default nella sottostante libreria (`urllib3`_). Ci potrebbero
essere volte in cui dovrete connettervi ad un servizio web che usa una versione
di SSL non compatibile con la vostra di default.

In questo caso potete usare gli Adapter di Trasporto riutilizzando la maggior
parte dell'implementazione di un HTTPAdapter e aggiungendo un parametro
*ssl_version* che viene passato ad `urllib3`. Creiamo un Adapter che dice alla
libreria di usare SSLv3:

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

Bloccante o Non-Bloccante?
--------------------------

Quando usa l'Adapter di Trasporto di default, Requests non provvede alcun tipo di
IO non-bloccante. La property :attr:`Response.content <requests.Response.content>`
si bloccherà fincho a che l'intera risposta non è stata scaricata. Se vi serve
pià granularità, le potenzialità di streaming della libreria (si veda
:ref:`streaming-requests`) vi consentiranno di recuperare piccoli pezzi della
risposta uno dopo l'altro. Tuttavia, queste chiamate sono sempre bloccanti.

Se intendete usare un paradigma di IO non-bloccante, ci sono molti progetti che
combinano Requests con uno dei framework asincroni per Python.
Due esempi d'eccellenza sono `grequests`_ e `requests-futures`_.

.. _`grequests`: https://github.com/kennethreitz/grequests
.. _`requests-futures`: https://github.com/ross/requests-futures

.. _timeouts:

Timeout
-------

La maggior parte delle richieste a server esterni dovrebbero contenere un timeout
nel caso in cui il server non risponda con le tempistiche che ci si attende.
Senza un timeout il vostro codice potrebbe rimanere in attesa per minuti o anche
più.

Il timeout di **connessione** è il numero di secondi che Requests attenderà
che il vostro client stabilisca una connessione alla macchina remota (corrisponde
alla chiamata `connect()`_) sul socket. E' buona pratica settare i timeout di
connessione poco oltre un multiplo di 3, che è la `finestra di default di TCP
per la ritrasmissione dei pacchetti <http://www.hjp.at/doc/rfc/rfc2988.txt>`_.

Quando il client è connesso al server e ha inviato la richiesta HTTP, il timeout
**di lettura** è il numero di secondi che il client attenderà che il server
invii una rispostsa (nello specifico, è il numero di secondi che il client 
attende *tra* ogni byte inviato dal server. Nel 99.9% dei casi, questo è il tempo
che passa prima che il server invii il primo byte).

Se specificate un valore singolo per il timeout, ad esempio così::

    r = requests.get('https://github.com', timeout=5)

Il valore di timeout sarà usato per entrambi i timeout di ``connect`` e ``read``.
Specificate una tupla se volete impostare i valori separatamente::

    r = requests.get('https://github.com', timeout=(3.05, 27))

Se il server remoto è molto lento, potete istruire Requests di attendere
indefinitamente per la risposta, passando None come valore di timeout e 
munendovi di una tazza di caffè.

.. code-block:: python

    r = requests.get('https://github.com', timeout=None)

.. _`connect()`: http://linux.die.net/man/2/connect

.. _ca-certificates:

Certificati delle CA
--------------------

Di default Requests contiene una raccolta di certificati root delle Certification
Authority che considera fidate, provenienti dal `Mozilla trust store`_. Tuttavia
questi certificati sono aggiornati solo ad ogni nuova versione di Requests. Ciò
significa che se utilizzate per molto tempo una specifica versione di Requests,
i certificati possono divenire molto obsoleti.

Dalla versione 2.4.0 in poi, Requests cerca di utilizzare i certificati da
`certifi`_ se questo è presente sul sistema. Ciò consente agli utenti di
aggiornare i loro certificati di fiducia senza dover modificare il codice che
gira sui loro sistemi.

A beneficio della sirucrezza, vi raccomandiamo di aggiornare certifi
frequentemente!

.. _certifi: http://certifi.io/
.. _Mozilla trust store: https://hg.mozilla.org/mozilla-central/raw-file/tip/security/nss/lib/ckfw/builtins/certdata.txt
