.. _api:

API per gli sviluppatori
========================

.. module:: requests

Questa parte della documentazione copre tutte le interfacce di Requests.
Le interfacce con librerie esterne sono documentate a grandi linee e vengono
forniti link alla loro documentazione canonica.


Interfaccia principale
----------------------

Tutte le funzionalità di Requests sono accessibili da questi 7 metodi.
Tutti ritornano un'istanza dell'oggetto :class:`Response <Response>`.

.. autofunction:: request

.. autofunction:: head
.. autofunction:: get
.. autofunction:: post
.. autofunction:: put
.. autofunction:: patch
.. autofunction:: delete


Classi di livello più basso
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. autoclass:: requests.Request
   :inherited-members:

.. autoclass:: Response
   :inherited-members:

Sessioni 
--------

.. autoclass:: Session
   :inherited-members:

.. autoclass:: requests.adapters.HTTPAdapter
   :inherited-members:

Autenticazione
--------------

.. autoclass:: requests.auth.AuthBase
.. autoclass:: requests.auth.HTTPBasicAuth
.. autoclass:: requests.auth.HTTPProxyAuth
.. autoclass:: requests.auth.HTTPDigestAuth

Eccezioni
~~~~~~~~~

.. autoexception:: requests.exceptions.RequestException
.. autoexception:: requests.exceptions.ConnectionError
.. autoexception:: requests.exceptions.HTTPError
.. autoexception:: requests.exceptions.URLRequired
.. autoexception:: requests.exceptions.TooManyRedirects
.. autoexception:: requests.exceptions.ConnectTimeout
.. autoexception:: requests.exceptions.ReadTimeout
.. autoexception:: requests.exceptions.Timeout


Ricerca degli status code
~~~~~~~~~~~~~~~~~~~~~~~~~

.. autofunction:: requests.codes

::

    >>> requests.codes['temporary_redirect']
    307

    >>> requests.codes.teapot
    418

    >>> requests.codes['\o/']
    200

Cookie
~~~~~~

.. autofunction:: requests.utils.dict_from_cookiejar
.. autofunction:: requests.utils.cookiejar_from_dict
.. autofunction:: requests.utils.add_dict_to_cookiejar

.. autoclass:: requests.cookies.RequestsCookieJar
   :inherited-members:

.. autoclass:: requests.cookies.CookieConflictError
   :inherited-members:


Encoding
~~~~~~~~

.. autofunction:: requests.utils.get_encodings_from_content
.. autofunction:: requests.utils.get_encoding_from_headers
.. autofunction:: requests.utils.get_unicode_from_response


Classi
~~~~~~

.. autoclass:: requests.Response
   :inherited-members:

.. autoclass:: requests.Request
   :inherited-members:

.. autoclass:: requests.PreparedRequest
   :inherited-members:

.. _sessionapi:

.. autoclass:: requests.Session
   :inherited-members:

.. autoclass:: requests.adapters.HTTPAdapter
   :inherited-members:


Migrazione alla versione 1.x
----------------------------

Questa sezione illustra le principali differenze tra le versioni 0.x e 1.x
e il suo scopo è facilitare l'upgrading.


Cambiamenti nell'API
~~~~~~~~~~~~~~~~~~~~

* ``Response.json`` ora è una callable e non più una property di una response.

  ::

      import requests
      r = requests.get('https://github.com/timeline.json')
      r.json()   # Questa *chiamata* lancia un'eccezione se il decoding del JSON fallisce

* La ``Session`` API è cambiata. Gli oggetti Sessione non accettano più parametri.
  ``Session`` ora è indicata con la lettera maiuscola ma può ancora essere istanziata tramite
  ``session`` con l'iniziale minuscola per retrocompatibilità.

  ::

      s = requests.Session()    # prima, le sessioni accettavano parametri
      s.auth = auth
      s.headers.update(headers)
      r = s.get('http://httpbin.org/headers')

* Sono stati rimossi tutti gli hook delle richieste tranne 'response'.

* Le funzioni di supporto all'autenticazione sono state spostate in moduli
  separati. Si vedano requests-oauthlib_ e requests-kerberos_.

.. _requests-oauthlib: https://github.com/requests/requests-oauthlib
.. _requests-kerberos: https://github.com/requests/requests-kerberos

* Il nome del parametro per le richieste streaming è cambiato da ``prefetch`` a
  ``stream`` e la logica è stata invertita. In più, ``stream`` viene ora
  richiesto per la lettura delle risposte raw.

  ::

      # nella versione 0.x, il passaggio di prefetch=False avrebbe dato lo stesso risultato
      r = requests.get('https://github.com/timeline.json', stream=True)
      for chunk in r.iter_content(8192):
          ...

* Il parametro ``config`` della funzione requests è stato rimosso. Alcune delle
  opzioni vengono ora configurate su una ``Session`` (es: keep-alive e numero
  massimo di redirezioni). L'opzione di verbosità dovrebbe essere gestita tramite
  configurazione del logging.

  ::

      import requests
      import logging

      # queste due righe abilitano il debugging a livello di httplib (requests->urllib3->httplib)
      # si vedranno la REQUEST, con HEADERS e DATA, e la RESPONSE con gli HEADERS ma senza DATA.
      # l'unico elemento mancante è il response.body che non viene loggato.
      import httplib
      httplib.HTTPConnection.debuglevel = 1

      logging.basicConfig() # occorre inizializzare il logging, altrimenti non si vedrà nulla delle richieste
      logging.getLogger().setLevel(logging.DEBUG)
      requests_log = logging.getLogger("requests.packages.urllib3")
      requests_log.setLevel(logging.DEBUG)
      requests_log.propagate = True

      requests.get('http://httpbin.org/headers')



Licenza
~~~~~~~

Una differenza fondamentale che non impatta l'API è il cambio di licenza da ISC_ 
ad `Apache 2.0`_ . La licenza Apache 2.0 garantisce che i contributi a Requests siano 
anch'essi licenziati con Apache 2.0.

.. _ISC: http://opensource.org/licenses/ISC
.. _Apache 2.0: http://opensource.org/licenses/Apache-2.0


Migrazione alla versione 2.x
----------------------------

Rispetto alla release 1.0, ci sono state relativamente poche modifiche non
retrocompatibili ma ci sono ancora alcuni problemi di questa major release di
cui è bene sapere.

Per ulteriore dettaglio sulle modifiche in questa release, comprese nuove APIs,
link alle issues relative su GitHub e il fixing di alcuni bachi, si legga il 
blog_ di Cory.

.. _blog: http://lukasa.co.uk/2013/09/Requests_20/


Modifiche nell'API
~~~~~~~~~~~~~~~~~~

* Ci sono stati un paio di modifiche sul modo con cui Requests gestisce le eccezioni.
  ``RequestException`` ora è una sottoclasse di ``IOError`` invece che di 
  ``RuntimeError`` perchè così viene meglio indicato il tipo di situazione erronea.
  In più, ora una sequenza di escaping degli URL non valida solleva l'istanza di una
  sottoclasse di ``RequestException`` piuttosto che di ``ValueError``.

  ::

      requests.get('http://%zz/')   # solleva requests.exceptions.InvalidURL

  Infine, le eccezioni di tipo ``httplib.IncompleteRead`` causate da un encoding scorretto
  sui chunks sollevano ora un'istanza di ``ChunkedEncodingError`` di Requests.

* L'API per i proxy è stata modificata lievemente. Viene ora richiesto di
  specificare lo schema per l'URL dei proxy.

  ::

      proxies = {
        "http": "10.10.1.10:3128",    # ora va utilizzato http://10.10.1.10:3128
      }

      # Nelle versioni 1.x di requests, questo codice era eseguito senza problemi mentre
      # nelle versioni 2.x solleva un'eccezione requests.exceptions.MissingSchema
      requests.get("http://example.org", proxies=proxies)


Modifiche al comportamento
~~~~~~~~~~~~~~~~~~~~~~~~~~

* Le chiavi nel dizionario ``headers`` sono ora stringhe native in tutte le
  versioni di Python es. bytestrings in Python 2 e unicode in Python 3. Se le
  chiavi non sono stringhe native (unicode in Python2 oppure bytestrings in
  Python 3), queste vengono convertite in stringhe native utilizzando UTF-8 come
  encoding.

