.. Requests documentation master file, created by
   sphinx-quickstart on Sun Feb 13 23:54:25 2011.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Requests: HTTP per gli Esseri Umani
===================================

Release v\ |version|. (:ref:`Installazione <install>`)

Requests è una libreria HTTP con licenza :ref:`Apache2 <apache2>`, scritta in
Python per gli Esseri Umani.

Il modulo urllib2 della libreria standard Python mette a disposizione
quasi tutte le principali funzionalità HTTP ma la sua interfaccia è molto
frastagliata. Quel modulo è stato creato per tempi diversi - e un web diverso.
Serve molto lavoro (addirittura anche l'overriding di metodi) per realizzare il
più semplice dei task.

Le cose non dovrebbero funzionare così. Non in Python.

::

    >>> r = requests.get('https://api.github.com/user', auth=('user', 'pass'))
    >>> r.status_code
    200
    >>> r.headers['content-type']
    'application/json; charset=utf8'
    >>> r.encoding
    'utf-8'
    >>> r.text
    u'{"type":"User"...'
    >>> r.json()
    {u'private_gists': 419, u'total_private_repos': 77, ...}

Guarda `un codice che fa le stesse cose, ma senza Requests <https://gist.github.com/973705>`_.

Requests si fa carico di tutto il lavoro per implementare HTTP/1.1 su Python -
rendendo immediata l'integrazione delle tue applicazioni con i web services.
Non c'è bisogno di aggiungere manualmente query string agli URL, o di fare
form-encoding dei dati di POST. Il Keep-alive e il pooling delle connessioni HTTP
sono 100% automatici, tutto ciò grazie a `urllib3 <https://github.com/shazow/urllib3>`_,
che è contenuta dentro Requests.


Testimonial
-----------

Il Governo di Sua Maestà, Amazon, Google, Twilio, Runscope, Mozilla, Heroku,
PayPal, NPR, Obama for America, Transifex, Native Instruments, il Washington
Post, Twitter, SoundCloud, Kippt, Readability, Sony e Istituzioni Federali degli
Stati Uniti che preferiscono rimanere anonime usano Requests al loro interno.
E' stato scaricato da PyPI più di 40.000.000 di volte.

**Armin Ronacher**
    Requests è l'esempio perfetto di quanto un'API possa essere bella con il giusto livello di astrazione.

**Matt DeBoard**
    In un modo o nell'altro, mi farò tatuare addosso il modulo Python requests di @kennethreitz.
    Il modulo intero.

**Daniel Greenfeld**
    Ho rimpiazzato una libreria di 1200 righe di codice spaghetti con sole 10 righe grazie alla libreria
    requests di @kennethreitz's. Oggi è stata una giornata INCREDIBILE.

**Kenny Meyers**
    HTTP con Python: se avete dubbi, o se non ne avete, usate Requests. Bella, semplice, Pythonica.


Features
--------

Requests è pronto per il web moderno.

- Domini e URL internazionali
- Keep-Alive e Pooling delle connessioni
- Sessioni persistenti attraverso i cookie
- Verifica SSL come la fanno i browser
- Autenticazione Basic/Digest
- Cookie chiave/valore
- Decompressione automatica dei dati
- Corpo delle risposte in Unicode
- Upload di file multipart
- Timeout sulle connessioni
- Supporto per ``.netrc``
- Supporto per Python 2.6—3.4
- Thread-safety.


Documentazione per l'utente
---------------------------

Questa parte della documentazione, che è per lo più in forma di prosa, inizia
contestualizzando Requests e prosegue dando istruzioni passo-passo per utilizzare
Requests al meglio.

.. toctree::
   :maxdepth: 2

   user/intro
   user/install
   user/quickstart
   user/advanced
   user/authentication


Documentazione per la community
-------------------------------

Questa parte della documentazione, che è per lo più in forma di prosa, spiega
i dettagli dell'ecosistema e della community attorno a Requests.

.. toctree::
   :maxdepth: 1

   community/faq
   community/recommended
   community/out-there
   community/support
   community/vulnerabilities
   community/updates
   community/release-process

Documentazione dell'API
-----------------------

Se cerchi informazioni su una funzione, una classe o un metodo in particolare,
questa parte della documentazione è il posto giusto.

.. toctree::
   :maxdepth: 2

   api


Documentazione per i collaboratori
----------------------------------

Se desiderate contribuire al progetto, questa parte della documentazione è il
punto di partenza. 

.. toctree::
   :maxdepth: 1

   dev/contributing
   dev/philosophy
   dev/todo
   dev/authors
