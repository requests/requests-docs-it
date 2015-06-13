.. :changelog:

Storico delle release
---------------------

2.7.0 (2015-05-03)
++++++++++++++++++

Questa è la prima release che segue il nuovo processo di release.
Per maggiori dettagli si veda la `nostra documentazione
<http://docs.python-requests.org/en/latest/community/release-process/>`_.

**Fix di bachi**

- Aggiornata urllib3 a 1.10.4, risolvendo vari bachi sull'encoding nei
  trasferimenti chunked e sul framing delle risposte.

2.6.2 (2015-04-23)
++++++++++++++++++

**Fix di bachi**

- Fix di una regressione dove dati compressi inviati in maniera chunked
  non erano decompressi correttamente. (#2561)

2.6.1 (2015-04-22)
++++++++++++++++++

**Fix di bachi**

- Rimossa il meccanismo di VendorAlias sugli import introdotto in v2.5.2.

- Semplificata l'API di PreparedRequest.prepare: non si richiede più che
  l'utente passi una lista vuota al keyword argument hooks (c.f. #2552)

- Risolve redirects ora riceve e inoltra tutti gli argomenti originali
  all'adapter. (#2503)

- Gestione degli UnicodeDecodeErrors quando si cerca di trattare un URL
  Unicode che non puà essere ASCII-endoded. (#2540)

- Durante l'autenticazione Digest viene popolato il path del campo URI (#2426)

- Si può copiare in maniera più affidabile il CookieJar di una PreparedRequest
  quando questo non è un'istanza di RequestsCookieJar. (#2527)

2.6.0 (2015-03-14)
++++++++++++++++++

**Fix di bachi**

- CVE-2015-2296: Fix della gestione dei cookie sulle redirezioni. In precedenza,
  un cookie senza il valore host avrebbe usato l'hostname dell'URL di redirezione
  esponendo così gli utenti di Requests ad attacchi di fixing delle sessioni e
  al potenziale furto dei cookie. Questo è stato comunicato privatamente da
  Matthew Daley di `BugFuzz <https://bugfuzz.com>`_. Questo baco riguarda tutte
  le versioni di Requests dalla v2.1.0 alla v2.5.3 (estremi inclusi).

- Fix dell'errore che accade quando Requests è una dipendenza ``install_requires``
  e si esegue ``python setup.py test``. (#2462)

- Fix dell'errore che si ha quando urllib3 non è inclusa nella distribuzione e
  Requests continua ad utilizzare la locazione della versione bundled.

- Fix alla gestione degli header da parte di ``urllib3``.

- Ora il modo con cui Requests gestisce le dipendenze non incluse è più restrittivo

**Feature e Miglioramenti**

- Supporto al passaggio di bytearrays come parametri nell'argomento``files``.
  (#2468)

- Evitata duplicazione dei dati quando si crea una richiesta con valori di tipo 
  ``str``, ``bytes``o ``bytearray`` per l'argomento ``files``.

2.5.3 (2015-02-24)
++++++++++++++++++

**Fix di bachi**

- Rollback della modifica al bundle dei certificati. Per più informazioni
  si vedano (#2455, #2456, and http://bugs.python.org/issue23476)

2.5.2 (2015-02-23)
++++++++++++++++++

**Feature e Miglioramenti**

- Aggiunto il sopporto alle checksum sha256. (`shazow/urllib3#540`_)

- Migliorata la performance degli header. (`shazow/urllib3#544`_)

**Fix di bachi**

- Copiato il meccanismo degli import di pip. Quando i redistributori downstream
  rimuovono requests.packages.urllib3 il meccanismo di import farà sì che
  gli stessi simboli continuino a funzionare. Gli esempi d'uso della 
  documentazione di Requests e librerie di terze parti che si basano su copie
  redistribuite di urllib3 continueranno a funzionare senza dover passare alla
  urllib3 di sistema.

- Tentativo di fare quoting delle parti di un URL sulle redirezioni, se
  l'unquoting e in seguito il quoting falliscono. (#2356)

- Fix del check sul tipo di nome del file sugli upload di dati multipart
  form. (#2411)

- Viene correttamente gestito il caso in cui un server che invia challenges
  per autenticazione Digest fornisce sia valori-qop auth che auth-int.
  (#2408)

- Fix del leak di un socket. (`shazow/urllib3#549`_)

- Fix di svariati header ``Set-Cookie``. (`shazow/urllib3#534`_)

- Disabilitata la verifica built-in dell'hostname. (`shazow/urllib3#526`_)

- Fix del decoding di uno stream dati vuoto. (`shazow/urllib3#535`_)

**Sicurezza**

- Pullato un ``cacert.pem`` aggiornato.

- Rimossa RC4 dalla lista degli algoritmi di cifratura di default. (`shazow/urllib3#551`_)

.. _shazow/urllib3#551: https://github.com/shazow/urllib3/pull/551
.. _shazow/urllib3#549: https://github.com/shazow/urllib3/pull/549
.. _shazow/urllib3#544: https://github.com/shazow/urllib3/pull/544
.. _shazow/urllib3#540: https://github.com/shazow/urllib3/pull/540
.. _shazow/urllib3#535: https://github.com/shazow/urllib3/pull/535
.. _shazow/urllib3#534: https://github.com/shazow/urllib3/pull/534
.. _shazow/urllib3#526: https://github.com/shazow/urllib3/pull/526

2.5.1 (2014-12-23)
++++++++++++++++++

**Modifiche al comportamento**

- In raise_for_status vengono catturati solo gli HTTPErrors (#2382)

**Fix di bachi**

- Gestione di LocationParseError di urllib3 (#2344)
- Gestione dei nomi dei file-like object che non sono stringhe (#2379)
- Nell'handler di HTTPDigestAuth handler viene consentito di negoziare
  nuovi nonce (#2389)

2.5.0 (2014-12-01)
++++++++++++++++++

**Miglioramenti**

- Consentito l'uso dell'oggetto Retry di urllib3 negli HTTPAdapters (#2216)
- Il metodo ``iter_lines`` su una risposta ora accetta un delimitatore con il quale
  splittare il contenuto (#2295)

**Modifiche al comportamento**

- Aggiunto warning di deprecazione alle funzioni di requests.utils che saranno
  rimosse in in 3.0 (#2309)
- Le Sessioni usate dall'API funzionale vengono sempre chiuse (#2326)
- Le richieste sono state ristrette ai soli HTTP/1.1 e HTTP/1.0
  (non più HTTP/0.9) (#2323)

**Fix di bachi**

- Gli URL vengono parsati una volta sola (#2353)
- L'header Content-Length può essere sempre sovrascritto (#2332)
- Gestione corretta dei file handle nell'autenticazione HTTP Digest (#2333)
- Limitazione della dimensione di redirect_cache per prevenire abusi di memoria (#2299)
- Fix della gestione delle redirezioni dopo autenticazione corretta con
  HTTP Digest (#2253)
- Fix del crash quando si danno parametri custom Session.request (#2317)
- Fix su come gli header Link sono parsati usando la libreria per le espressioni
  regolari (#2271)

**Documentazione**

- Aggiunte più riferimenti per l'interlinking (#2348)
- Aggiornato il CSS del tema (#2290)
- Aggiornata la larghezza dei pulsanti e della sidebar (#2289)
- Sostituiti i riferimenti a Gittip con quelli a Gratipay (#2282)
- Aggiunto nella sidebar un link al changelog (#2273)

2.4.3 (2014-10-06)
++++++++++++++++++

**Fix di bachi**

- Miglioramenti agli URL Unicode per Python2.
- Re-ordinamento del parametro JSON per retrocompatibilità.
- Deframmentazione automatica degli schemi di autenticazione dagli URI con host/password.
  (`#2249 <https://github.com/kennethreitz/requests/issues/2249>`_)


2.4.2 (2014-10-05)
++++++++++++++++++

**Miglioramenti**

- FINALMENTE! Aggiunto il parametro json parameter per gli upload!
  (`#2258 <https://github.com/kennethreitz/requests/pull/2258>`_)
- Supporto per i bytestring URL su Python 3.x
  (`#2238 <https://github.com/kennethreitz/requests/pull/2238>`_)

**Fix di bachi**

- Rimossa situazione di loop infinito
  (`#2244 <https://github.com/kennethreitz/requests/pull/2244>`_)
- Varie chiamate a iter* fallivano con un errore non autodescrittivo.
  (`#2240 <https://github.com/kennethreitz/requests/issues/2240>`_,
  `#2241 <https://github.com/kennethreitz/requests/issues/2241>`_)

**Documentazione**

- Corretta l'introduzione alla redirezione
  (`#2245 <https://github.com/kennethreitz/requests/pull/2245/>`_)
- Aggiunto esempio di come inviare più file in una sola richiesta
  (`#2227 <https://github.com/kennethreitz/requests/pull/2227/>`_)
- Spiegato meglio come passare un set di certificati custom
  (`#2248 <https://github.com/kennethreitz/requests/pull/2248/>`_)



2.4.1 (2014-09-09)
++++++++++++++++++

- Ora c'è un set di extras di nome "security", ``$ pip install requests[security]``
- Requests ora usa Certifi se è disponibile.
- Viene catturato e ri-sollevato l'errore ProtocolError di urllib3
- Fix al baco per cui alcune cercano di redirigere a se stesse all'infinito (ma che c...)


2.4.0 (2014-08-29)
++++++++++++++++++

**Modifiche al comportamento**

- L'header ``Connection: keep-alive`` ora è inviato in maniera automatica.

**Miglioramenti**

- Supporto per il timeout delle connessioni! Timeout ora accetta una tupla di forma
  (connect, read) usata per settare i timeout individuali di connessione e lettura.
- E' consentito compiare una PreparedRequests senza headers/cookies.
- Aggiornata la dipendenza a urllib3.
- Refactoring nel caricamneto dei setting dall'ambiente:
  introdotto `Session.merge_environment_settings`.
- Gestione degli errori dei socket dentro iter_content.


2.3.0 (2014-05-16)
++++++++++++++++++

**Modifiche all'API**

- Nuova property ``is_redirect`` per ``Response``: è true quando la libreria
  avrebbe potuto
  processare la risposta come una redirezione (che lo abbia fatto o meno).
- Il parametro ``timeout`` ora impatta le richieste sia con ``stream=True`` che
  ``stream=False`` senza distinzione.
- Rollback della modifica fatta in v2.0.0 che richiedeva esplicitazione di URL
  scheme per i proxy. Questi ora defaultano a ``http://``.
- Il ``CaseInsensitiveDict`` usato per gli header HTTP ora si comporta come un
  normale dizionario quando si riferisce a stringhe o viene visto tramite
  l'interprete.

**Fix di bachi**

- Gli header Authorization e Proxy-Authorization non sono più esposti in caso
  di redirezioni. Fix CVE-2014-1829 e CVE-2014-1830 rispettivamente.
- L'autorizzazione è ri-effettuata ad ogni redirezione.
- Sulle redirezioni, gli URL sono passati come stringhe native.
- Fall-back sull'encoding auto-rilevato per il JSON quando la rilevazione
  Unicode fallisce.
- Gli headers di valore ``None`` nelle ``Session`` non vengono inviati.
- Viene onorata in modo corretto ``decode_unicode`` anche se non era usata
  in precedenza nella stessa risposta
- Il Content-Encoding ``compress`` non è più supportato.
- Il parametro ``Response.history`` ora è sempre una lista.
- Tanti, tanti fix a bachi di ``urllib3``.

2.2.1 (2014-01-23)
++++++++++++++++++

**Fix di bachi**

- Fix del parsing scorretto delle credenziali proxy che contengono un carattere '#'
  letterale o encoded.
- Vari fix a urllib3.

2.2.0 (2014-01-09)
++++++++++++++++++

**Modifiche all'API**

- Nuova eccezione: ``ContentDecodingError``. Sollevato al posto dell'eccezione
  ``DecodeError`` di ``urllib3``.

**Fix di bachi**

- Evitato il sollevamento di un sacco di eccezioni sulla debole implementazione di
  ``proxy_bypass`` su OS X per Python 2.6.
- Evitato il crashing mentre si cerca di ottenere credenziali di autenticazione da
  ~/.netrc se si impersona un utente senza una home directory.
- Uso della dimensione corretta per i pool di connessioni ai proxy.
- Fix sull'iterazione degli oggetti ``CookieJar``.
- Ora i cookie sono salvati durante i redirect.
- Ritorniamo ad usare chardet, dal momento che si è fuso con charade.

2.1.0 (2013-12-05)
++++++++++++++++++

- Ovviamente, aggiornato il bundle dei certificati.
- I cookie impostati su singole richieste attraverso una ``Session`` (es: con
  ``Session.get()``) non sono più salvati sulla ``Session``.
- Non c'è più leak della connessioni quando si verificano problemi sugli upload
  chunked.
- Le connessioni vengono ritornate nel pool quando un upload chunked va a buon fine.
- Implementate le recommendation HTTPbis per le redirezioni HTTP 301.
- Rimossa l'attesa indefinita sugli upload in streaming con autenticazione Digest e
  un 401 è ricevuto.
- I valori degli header impostati da Requests sono ora tipi stringa nativi.
- Fix: il supporto SNI era rotto.
- Fix: accesso ai proxy HTTP usando l'autenticazione ai proxy
- Decodifica degli username e password HTTP Basic estratti dagli URL.
- Supporto ai range di indirizzi IP nella variabile d'ambiente no_proxy.
- Corretto il parsing degli header quando gli utenti sovrascrivono l'header ``Host:``
  di default.
- Nessun URL-munging in caso di server case-sensitive.
- Gestione degli URL più rilassata per gli URL non-HTTP/HTTPS.
- I metodi Unicode sono accettati in Python 2.6 e 2.7.
- Gestione cookie più robusta agli errori.
- Gli oggetti ``Response`` sono serializzabili con Pickle.
- Ora sono stati davvero (diversamente dalla volta scorsa) aggiunte le sessioni MD5
  all'autenticazione Digest.
- Aggiornata la dipendenza a urllib3.
- Fix: la mancanza di senso estetico di @Lukasa.

2.0.1 (2013-10-24)
++++++++++++++++++

- Aggiornato il bundle dei certificati con nuovi provider parzialmente fidati e un
  processo automatico
- Aggiunte sessioni MD5 all'autenticazione Digest
- Vengono accettati headers per per ogni singolo file nelle POST di file multipli
- Fix: non veniva inviato l'URL intero sulle CONNECT
- Fix: lo URL schema nei redirect viene messo correttamente in lowercase
- Fix: i cookie impostati attraverso l'API funzionale non erano salvati
- Fix: tradotto l'errore di ProxyError urllib3 in un errore ProxyError di
  Requests derivato da ConnectionError.
- Aggiornate le dipendenze a urllib3 e chardet.

2.0.0 (2013-09-24)
++++++++++++++++++

**Cambiamenti all'API:**

- Le chiavi nel dizionario Headers sono stringhe native in tutte le versioni di
  Python, es: bytestrings su Python 2, Unicode su Python 3.
- Gli URL dei Proxy ora *devono* avere uno schema esplicito. In caso contrario,
  un'eccezione ``MissingSchema`` è solelvata.
- I timeout ora si applicano al tempo di lettura dei dati se ``Stream=False``.
- ``RequestException`` è ora sottoclasse di ``IOError``, non ``RuntimeError``.
- Aggiunto nuovo metodo agli oggetti ``PreparedRequest``: ``PreparedRequest.copy()``.
- Aggiunto nuovo metodo agli oggetti ``Session``: ``Session.update_request()``.
  Questo metodo aggiorna un oggetto ``Request`` con i dati (es: cookie) salvati
  sulla ``Session``.
- Aggiunto nuovo metodo agli oggetti ``Session``:
  ``Session.prepare_request()``. Questo metodo aggiorna e prepara un oggetto
  ``Request`` e ritorna il corrispondente oggetto ``PreparedRequest``.
- Aggiunto nuovo metodo agli oggetti ``HTTPAdapter``:
  ``HTTPAdapter.proxy_headers()``. Non dovrebbe essere invocato direttamente, ma
  migliora l'interfaccia delle sottoclassi.
- Le eccezioni ``httplib.IncompleteRead`` causate da un encoding dei chunk ora
  sollevano un'eccezione ``ChunkedEncodingError`` di Requests.
- Sequenze di percent-escape invalide causano ora un'eccezione ``InvalidURL``
  di Requests.
- HTTP 208 non usa più il messaggio di spiegazione ``"im_used"``. Usa invece
  correttamente ``"already_reported"``.
- Aggiunto messaggio di spiegazione ad HTTP 226 (``"im_used"``).

**Fix di bachi:**

- Migliorato sensibilmente il supporto ai proxy, incluso il verbo CONNECT. Un
  ringraziamento speciale ai tanti collaboratori che hanno lavorato su questa
  miglioria.
- I cookies ora sono gestiti bene quando si ricevono risposte 401.
- Fix all'encoding chunked.
- Supporto per schemi URL mixed-case.
- Migliorata la gestione dei download in streaming.
- Vengono recuperati i proxy ambientali da più locazioni.
- Fix di minore entità sui cookie.
- Migliorato il comportamento di redirezione.
- Migliorato il comportamento dello streaming, in particolare con dati compressi
- Fix su vari piccoli bachi sul text encoding su Python 3.
- ``.netrc`` non sovrascrive più le forme di autenticazione esplicite.
- I cookie impostati dagli hook vengono ora correttamente salvati nelle sessioni
- Fix di un baco sui cookie per cui si specificava il numero di porta nel loro
  campo host
- ``BytesIO`` può essere usato per fare upload in streaming
- Parsing più generoso della variabile di ambiente ``no_proxy``.
- E' possibile passare oggetti diversi da stringhe come dati insieme ai file.

1.2.3 (2013-05-25)
++++++++++++++++++

- Semplice fix sul packaging


1.2.2 (2013-05-23)
++++++++++++++++++

- Semplice fix sul packaging


1.2.1 (2013-05-20)
++++++++++++++++++

- Le redirezioni 301 e 302 cambiano tutti i verbi - non solo POST - in GET,
  migliorando la compatibilità con i browser
- Compatibilità con Python 3.3.2
- Gli header Location sono sempre percent-encoded
- Fix: i primi connection adapter ad essere matchati sono i più specifici
- nuovo argomento per il connection adapter di default per passare un argomento
  block
- quando non ci sono header Link non viene sollevato un KeyError

1.2.0 (2013-03-31)
++++++++++++++++++

- Fix sui cookie durante le sessioni e sulle richieste
- Pesantemente modificato il modo in cui gli hoook sono invocati - ora gli hook
  ricevono tutti gli argomenti specificati dall'utente quando lancia una richiesta
  così gli hook possono lanciare una richiesta secondaria con gli stessi
  parametri. Questo è particolarmente necessario per gli autori di handler di
  autenticazione.
- Il supporto a certifi è stato rimosso
- Fix al baco per cui non venivano inviati dati usando OAuth1 con il corpo
  ``signature_type``
- Grosso lavoro sui proxy grazie a @Lukasa incluso il parsing dei dati di 
  autenticazione a partire dall'URL del proxy
- Fix baco sulla gestione di troppo 401 con autenticazione DigestAuth
- Aggiornata la urllib3 per includere fix di bachi SSL
- Ora i keyword arguments possono essere passati a ``json.loads()`` attraverso
  il metodo ``Response.json()``
- Di default non viene inviato l'header ``Content-Length`` sulle richieste
  ``GET`` o ``HEAD``
- Aggiunto l'attributo ``elapsed`` agli oggetti ``Response`` per misurare quanto
  tempo ha impiegato una richiesta ad essere evasa
- Fix a ``RequestsCookieJar``
- Sessioni e Adapter ora sono serializzabili con Pickle, es: possono essere
  usati con la libreria multiprocessing
- Aggiornato charade alla versione 1.0.3

La modifica nel modo in cui gli hoook sono invocati molto probabilmente causerà
un gran numero di issues.

1.1.0 (2013-01-10)
++++++++++++++++++

- RICHIESTE CHUNKED
- Supporto per corpo delle risposte iterabili
- Assunzione che i server memorizzino i parametri di redirect
- Ora è possibile specificare dei content type espliciti per i file
- Durante il lookup delle chiavi, merge_kwargs è case-insensitive

1.0.3 (2012-12-18)
++++++++++++++++++

- Fix baco sull'encoding durante upload di file
- Fix sul comportamento dei cookie

1.0.2 (2012-12-17)
++++++++++++++++++

- Fix sul proxy per HTTPAdapter.

1.0.1 (2012-12-17)
++++++++++++++++++

- Fix baco di verifica dei certificati.
- Fix sul proxy per HTTPAdapter.

1.0.0 (2012-12-17)
++++++++++++++++++

- Un micchio di refactoring e semplificazione
- Adozione della licenza Apache 2.0
- Connection Adapters sostituibili
- Connection Adapters montabili sulle sessioni
- Catena delle ProcessedRequest è mutabile
- /s/prefetch/stream
- Rimozione di tutta la configurazione
- Logging attraverso la Standard library
- Ora Response.json() è una callable, non una property
- Utilizzo del nuovo progetto charade project, che fornisce la chardet simultanea
  per python 2 e 3
- Rimozione di tutti gli hook ad eccezione di'response'
- Rimozione di tutti gli helper di autenticazione (OAuth, Kerberos)

Questa release non è retrocompatibile.

0.14.2 (2012-10-27)
+++++++++++++++++++

- Migliorata la gestione del JSON mime-compatibile
- Fix sui Proxy
- Fix sull'hacking dei path
- Headers Content-Encoding sono ora case-insensitive
- Supporto per i parametri CJK nel POST-ing dei form


0.14.1 (2012-10-01)
+++++++++++++++++++

- Compatibilità con Python 3.3
- Semplice valore di default per accept-encoding
- Fix di bachi


0.14.0 (2012-09-02)
++++++++++++++++++++

- iter_content non dà più errori se il contenuto è stato già scaricato.

0.13.9 (2012-08-25)
+++++++++++++++++++

- Fix su OAuth + POSTs
- Rimosso occultamento delle eccezioni da dispatch_hook
- Fix di bachi

0.13.8 (2012-08-21)
+++++++++++++++++++

- Supporto pazzesco agli header Link :)

0.13.7 (2012-08-19)
+++++++++++++++++++

- Supporto per liste in formato (key, value) dovunque.
- Miglioramenti sull'autenticazione Digest.
- Ora l'esclusione dei proxy funziona correttamente.
- Eccezioni UnicodeError più chiare.
- Casting automatico degli URL a stringhe
- Fix di bachi.

0.13.6 (2012-08-06)
+++++++++++++++++++

- Fix atteso da lungo tempo sulle connessioni in stallo!

0.13.5 (2012-07-27)
+++++++++++++++++++

- Fix sul packaging

0.13.4 (2012-07-27)
+++++++++++++++++++

- Autenticazione GSSAPI/Kerberos!
- Fix all'App Engine 2.7!
- Fix baco sui leak delle connessioni (segue dall'update di urllib3)
- Fix dell'hacking sui path in OAuthlib
- Fix dei parametri degli URL in OAuthlib.

0.13.3 (2012-07-12)
+++++++++++++++++++

- Uso di simplejson se disponibile.
- Non nascondere gli SSLErrors dietro ai Timeouts.
- Gestione dei parametri fissi con URL che contengono fragments.
- Migliorato sensibilmente il contenuto di User Agent.
- I certificati dei client sono ignorati quando verify=False

0.13.2 (2012-06-28)
+++++++++++++++++++

- Nessuna dipendenza (di nuovo)!
- Nuovo: Response.reason
- Firma dei parametri di query in OAuth 1.0
- I certificati dei client non vengono più ignorati quando verify=False
- Aggiunto supporto ai certificati openSUSE

0.13.1 (2012-06-07)
+++++++++++++++++++

- E' possibile passare un file o un oggetto file-like come dati.
- Gli hook possono ritornare risposte che indicano errori.
- Fix su Response.text e Response.json per risposte senza corpo.

0.13.0 (2012-05-29)
+++++++++++++++++++

- Rimozione di Requests.async in favore di `grequests <https://github.com/kennethreitz/grequests>`_
- E' ora possibile disabilitare la persistenza dei cookie.
- Nuova immplementazione di safe_mode
- cookies.get ora supporta argomenti di default
- I cookie di sessione non sono salvati quando Session.request è invocata con 
  return_response=False
- Variabili di ambiente: supporto a no_proxy.
- Miglioramenti a RequestsCookieJar.
- Fix a vari bachi.

0.12.1 (2012-05-08)
+++++++++++++++++++

- Nuova propery ``Response.json``.
- Possibilità di aggiungere upload di file sotto forma di stringhe.
- Fix baco out-of-range su iter_lines.
- Fix sulla dimensione di default di iter_content.
- Fix baco su redirezioni POST che contengono file.

0.12.0 (2012-05-02)
+++++++++++++++++++

- SUPPORTO SPERIMANTALE A OAUTH!
- Migliorata interfaccia (dict-like) con i cookie CookieJar.
- Fix baco su lentezza dei chunk di contenuto che non vengono iterati.
- Spostato ``pre_request`` in una locazione più usabile.
- Nuovo hook ``pre_send``.
- Encoding lazy di dati, parametri, files.
- Caricamento del bundle di certificati di sistema se ``certify`` non è
  disponibile.
- Pulizia del codice, fix.

0.11.2 (2012-04-22)
+++++++++++++++++++

- Tentativo di utilizzo del bundle di certificati del sistema operativo se
  ``certifi`` non è disponibile.
- Fix baco su redirezione infinita su autenticazione Digest.
- Miglioramenti nell'upload di file Multi-part.
- Fix baco su decoding degli %encodings invalidi sugli URL.
- Se non c'è contenuto in una risposta non viene sollevato un errore la seconda
  volta che si prova a leggere il contenuto.
- Upload di dati nelle redirezioni.

0.11.1 (2012-03-30)
+++++++++++++++++++

* Le redirect su POST ora non seguono la RFC e fanno come i browser: proseguono
  con una GET.
* Nuova configurazione ``strict_mode`` per disabilitare il nuovo comportamento
  di redirezione.


0.11.0 (2012-03-14)
+++++++++++++++++++

* Supporto ai certificati privati su SSL
* Rimosso select.poll dal monkeypatching di Gevent
* Rimosso un generatore ridondante nell'encoding dei trasferimenti chunked
* Fix: Response.ok sollevava Timeout Exception in safe_mode

0.10.8 (2012-03-09)
+++++++++++++++++++

* Fix sulla generazione di ValueError chunked
* Configurazione dei Proxy tramite variabili d'ambiente
* Semplificazione di iter_lines.
* Nuova configurazione `trust_env` per disabilitare i suggerimenti di
  sistema/ambiente.
* Soppressione degli errori sui cookie.

0.10.7 (2012-03-07)
+++++++++++++++++++

* `encode_uri` = False

0.10.6 (2012-02-25)
+++++++++++++++++++

* '=' è consentito nei cookies.

0.10.5 (2012-02-25)
+++++++++++++++++++

* Fix baco su corpo delle risposte con 0 content-length.
* Nuovo async.imap.
* Fix crash su utilizzo di netrc.


0.10.4 (2012-02-20)
+++++++++++++++++++

* Viene utilizzato netrc.

0.10.3 (2012-02-20)
+++++++++++++++++++

* Le richieste HEAD non seguono più le redirect.
* raise_for_status() non solleva più gli errori 3xx.
* Gli oggetti Session sono serializzabili con Pickle.
* ValueError per gli URL con schema invalido.

0.10.2 (2012-01-15)
+++++++++++++++++++

* Profondo miglioramento al quoting degli URL.
* Consentiti più valori per le chiavi dei cookie.
* Tentativo di fix per l'errore "Too many open files"
* Sostituzione degli errori Unicode alla prima passata, secondo passata non più
  necessario.
* Concatenamento di '/' agli URL con solo dominio prima dell'inserimento della
  query.
* Ora le Eccezioni ereditano da RuntimeError.
* Fix su upload binari uploads e autenticazione.
* Fix di bachi.


0.10.1 (2012-01-23)
+++++++++++++++++++

* SUPPORTO A PYTHON 3!
* Abbandonato supporto a Python 2.5. (*Non retrocompatibile*)

0.10.0 (2012-01-21)
+++++++++++++++++++

* ``Response.content`` ritorna ora solo bytes. (*Non retrocompatibile*)
* ``Response.text`` ora ritorna solo Unicode.
* Se non è specificato un ``Response.encoding`` e ``chardet`` è disponibile,
  ``Response.text`` tenta di indovinare l'encoding.
* Default sull'encoding ISO-8859-1 (Western) per i sottotipi di "text".
* Rimozione di `decode_unicode`. (*Non retrocompatibile*)
* Nuovo sistema ad hoook multipli.
* Nuovo metodo ``Response.register_hook`` per registrare hook all'interno della
  pipeline.
* ``Response.url`` ora ritorna Unicode.

0.9.3 (2012-01-18)
++++++++++++++++++

* Fix baco su verify=False di SSL (apparente sulle macchine Windows).

0.9.2 (2012-01-18)
++++++++++++++++++

* Metodo async.send è ora asincrono.
* Supporto per la corretta delimitazione degli stream di chunks.
* Argomento session per le classi Session.
* Stampa delle intere traceback, non solo dell'istanza dell'eccezione
* Fix response.iter_lines quando è in attesa delle prossima linea.
* Fix baco sull'autenticazione HTTP-digest con URI con query string.
* Fix nella sezione Hook degli Eventi.
* Aggiornamento di Urllib3.


0.9.1 (2012-01-06)
++++++++++++++++++

* danger_mode quando Response.raise_for_status() è automatico
* Refactoring di Response.iter_lines

0.9.0 (2011-12-28)
++++++++++++++++++

* La verifica SSL è fatta di default.


0.8.9 (2011-12-28)
++++++++++++++++++

* Fix sul packaging.


0.8.8 (2011-12-28)
++++++++++++++++++

* VERIFICA DEI CERTIFICATI SSL!
* Release di Certifi: la lista di certificati di Mozilla.
* Nuovo argoento 'verify' per le richieste SSL.
* Aggiornamento di Urllib3.

0.8.7 (2011-12-24)
++++++++++++++++++

* Fix sul troncamento dell'ultima riga con iter_lines
* Viene forzato safe_mode per le richieste asincrone
* Gestione più consistente delle eccezioni in safe_mode
* Fix sull'iterazione delle risposte nulle in safe_mode

0.8.6 (2011-12-18)
++++++++++++++++++

* Fix sui timeout a livello socket.
* Supporto all'autorizzazione per i Proxy.

0.8.5 (2011-12-14)
++++++++++++++++++

* Response.iter_lines!

0.8.4 (2011-12-11)
++++++++++++++++++

* Fix baco sul Prefetch.
* Aggiunta licenza per la versione installata.

0.8.3 (2011-11-27)
++++++++++++++++++

* Semplificazione del sistema di autenticazione per l'uso di oggetti callable.
* Nuovo parametro session per i metodi dell'API
* Visualizzazine dell'URl intero nei log.

0.8.2 (2011-11-19)
++++++++++++++++++

* Nuovo sistema di decoding Unicode, basato su `Response.encoding`, che è
  overridable
* Gestione corretta del quoting degli slash negli URL.
* I cookie contenenti ``[``, ``]``, e ``_`` sono ora consentiti.

0.8.1 (2011-11-15)
++++++++++++++++++

* UFix sul path dell'URL nelle richieste
* Fix sui Proxy.
* Fix sui Timeouts.

0.8.0 (2011-11-13)
++++++++++++++++++

* Supporto al Keep-alive!
* Rimozione completa di Urllib2
* Rimozione completa di Poster
* Rimozione completa di CookieJars
* Nuovo modo di sollevare ConnectionError 
* Safe_mode per il catching degli errori
* Prefetch dei parametri per i metodi di richiesta
* Supporto a OPTION
* Tuning asincrono delle dimensioni del pool
* Gli upload dei file inviano nomi reali
* Inserita dipendenza a urllib3

0.7.6 (2011-11-07)
++++++++++++++++++

* Fix baco su autenticazione Digest (concatenamento dei dati di query al path)

0.7.5 (2011-11-04)
++++++++++++++++++

* Response.content = None se c'è stata una risposta invalida.
* Gestione della redirezione in sede di autenticazione.

0.7.4 (2011-10-26)
++++++++++++++++++

* Fix sugli hook delle sessioni.

0.7.3 (2011-10-23)
++++++++++++++++++

* Fix sull'autenticazione Digest.


0.7.2 (2011-10-23)
++++++++++++++++++

* Fix su PATCH.


0.7.1 (2011-10-23)
++++++++++++++++++

* L'handling delle autenticazionidi urllib2 non è più usato.
* Rimozione completa di AuthManager, AuthObject, etc.
* Nuovo sistema di autenticazione basato su tuple e esecuzione di callback.


0.7.0 (2011-10-22)
++++++++++++++++++

* Le sessioni sono ora l'interfaccia primaria.
* InvalidMethodException è ora deprecata.
* Fix su PATCH.
* Nuovo sistema di configurazione (non si usano più setting globali)


0.6.6 (2011-10-19)
++++++++++++++++++

* Fix baco sui parametri di sessione (merging dei parametri).


0.6.5 (2011-10-18)
++++++++++++++++++

* Suite di test offline (veloce).
* Merging degli argomenti dei dizionari di sessione.


0.6.4 (2011-10-13)
++++++++++++++++++

* Decoding automatico di Unicode, sulla base degli header HTTP.
* Nuovo setting ``decode_unicode``.
* Rimozione dei metodi ``r.read/close``.
* Nuova interfaccia ``r.faw`` per un uso avanzato delle risposte.
* Espansione automatica degli header parametrizzati.


0.6.3 (2011-10-13)
++++++++++++++++++

* modulo ``requests.async``, per inviare richieste asincrone con gevent.


0.6.2 (2011-10-09)
++++++++++++++++++

* GET/HEAD onorano allow_redirects=False.


0.6.1 (2011-08-20)
++++++++++++++++++

* Migliorata l'esperienza d'uso degli status code ``\o/``
* Specifica del numero massimo di redirezioni (``settings.max_redirects``)
* Supporto completo agli URL Unicode
* Supporto alle redirezioni protocol-less.
* E' possibile inviare tipologie arbitrarie di richiesta.
* Fix di bachi


0.6.0 (2011-08-17)
++++++++++++++++++

* Nuovo sistema per l'hooking delle callback
* Nuovi oggetti sessioni permanenti e nuovo context manager
* Gestione trasparente Dict-cookie
* Oggetto per il riferimento agli status code
* Rimosso Response.cached
* Aggiunto Response.request
* Tutti gli argomenti sono kwargs
* Supporto alle redirezioni relative
* Miglioramenti alla gestione degli HTTPError
* Migliorato il testing di HTTPS
* Fix di bachi


0.5.1 (2011-07-23)
++++++++++++++++++

* Supporto ai Nomi a Dominio Internazionali!
* Accesso agli headers senza dover recuperare l'intero corpo (``read()``)
* Uso di liste come dicts per i parametri
* Aggiunta autenticazione Basic Forzata
* L'autenticazione Basic Forzata è ora quella di default
* ``python-requests.org`` è lo User-Agent header di default
* caching lower-case di CaseInsensitiveDict
* Fix baco su Response.history


0.5.0 (2011-06-21)
++++++++++++++++++

* Supporto a PATCH
* Support ai Proxy
* Suite di test con HTTPBin
* Fix sulle redirezioni
* Stream in scrittura con settings.verbose
* Querystrings per tutti i metodi
* Gli URLErrors (Connection Refused, Timeout, Invalid URLs) sono trattati come
  se fossero esplicitamente sollevati dalla libreria
  ``r.requests.get('hwe://blah'); r.raise_for_status()``


0.4.1 (2011-05-22)
++++++++++++++++++

* Migliorata la gestione delle redirezioni
* Nuovo parametro 'allow_redirects' per seguire le redirezioni non-GET/HEAD
* Refactoring del modulo dei settings


0.4.0 (2011-05-15)
++++++++++++++++++

* Response.history: lista di risposte in seguito a redirezioni
* I dizionari degli header ora sono case-insensitive!
* URL Unicode


0.3.4 (2011-05-14)
++++++++++++++++++

* Fix baco di ricorsione nella HTTPAuthentication di Urllib2 (Basic/Digest)
* Refactoring interno
* Fix baco di upload di bytes



0.3.3 (2011-05-12)
++++++++++++++++++

* Timeout sulle richieste
* Dati url-encoded con Unicode
* Gestore e modulo per la configurazione del contesto


0.3.2 (2011-04-15)
++++++++++++++++++

* Decompressione automatica del contenuto GZip
* Supporto per AutoAuth Support per l'autenticazione HTTP tramite tupla


0.3.1 (2011-04-01)
++++++++++++++++++

* Modifiche ai cookie
* Response.read()
* Fix su Poster


0.3.0 (2011-02-25)
++++++++++++++++++

* Cambiamento automatico dell'API di Autenticazione
* Parametrizzazione più intelligente delle URL query
* E' possibile uploadare file e POST-are dati contemporaneamente
* Nuovo sistema di gestione dell'autenticazione
    - Sistema Basic HTTP più semplice
    - Supporta tutti i meccanismi builtin di autenticazione in urllib2
    - Possibilità di usare handler di autenticazione custom


0.2.4 (2011-02-19)
++++++++++++++++++

* Supporto per Python 2.5
* Supporto per PyPy-c v1.4
* Test per Auto-Autenticazione
* Migliorato il costruttore degli oggetti di tipo Request

0.2.3 (2011-02-15)
++++++++++++++++++

* Nuovi metodi per HTTPHandling
    - Response.__nonzero__ (false se ho HTTP Status di errore)
    - Response.ok (True se ho HTTP Status OK)
    - Response.error (Logga un HTTPError se ho HTTP Status di errore)
    - Response.raise_for_status() (Solleva un HTTPError archiviato)


0.2.2 (2011-02-14)
++++++++++++++++++

* Le richieste vengono comunque gestite in caso di un HTTPError. (Issue #2)
* Supporto al monkeypatching con Eventlet e Gevent.
* Supporto ai Cookie (Issue #1)


0.2.1 (2011-02-14)
++++++++++++++++++

* Aggiunto l'attributo file alle richieste POST e PUT per upload di file
  multipart-encoded.
* Aggiunto l'attributo Request.url per il contesto e le redirect


0.2.0 (2011-02-14)
++++++++++++++++++

* Nascita!


0.0.1 (2011-02-13)
++++++++++++++++++

* Frustrazione
* Idea iniziale

