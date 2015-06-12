.. _authentication:

Autenticazione
==============

Questo documento discute l'uso dei diversi tipi di autenticazione con Requests.

Molti servizi web richiedono autenticazione e ce ne sono varie tipologie.
Illustreremo le varie forme di autenticazione disponibili in Requests, dalla
più semplice alla più complessa.


Autenticazione Basic
--------------------

Molto servizi web che richiedono autenticazione accettano l'HTTP Basic Auth.
E' la più semplice tipologia di autenticazione e Requests la supporta di default.

Lanciare richieste con HHTTP Basic Auth è molto semplice::

    >>> from requests.auth import HTTPBasicAuth
    >>> requests.get('https://api.github.com/user', auth=HTTPBasicAuth('user', 'pass'))
    <Response [200]>

L'uso di HTTP Basic Auth è così comune che Request fornisce una scorciatoia
molto comoda per usarla::

    >>> requests.get('https://api.github.com/user', auth=('user', 'pass'))
    <Response [200]>

Fornire le credenziali in una tupla in questo modo funziona esattamente come
l'esempio sopra con ``HTTPBasicAuth``.


Autenticazione con netrc
~~~~~~~~~~~~~~~~~~~~~~~~

Se nessun metodo di autenticazione è specificato per il parametro ``auth``,
Requests tenta di recuperare le credenziali di autenticazione per l'hostname
dell'URL dal file netrc dell'utente.

Se vengono trovate delle credenziali per l'hostname, la richiesta è inviata
con HTTP Basic Auth.


Autenticazione con Digest
-------------------------
Un'altra tipologia molto popolare di autenticazione HTTP è quella con Digest,
e Requests supporta anche questa di default::

    >>> from requests.auth import HTTPDigestAuth
    >>> url = 'http://httpbin.org/digest-auth/auth/user/pass'
    >>> requests.get(url, auth=HTTPDigestAuth('user', 'pass'))
    <Response [200]>


Autenticazione con OAuth 1
--------------------------
Un'altra forma comune di autenticazione per dicerse API web è OAuth. La libreria
``requests-oauthlib`` consente agli utenti di Requests di fare richieste con
OAuth::

    >>> import requests
    >>> from requests_oauthlib import OAuth1

    >>> url = 'https://api.twitter.com/1.1/account/verify_credentials.json'
    >>> auth = OAuth1('YOUR_APP_KEY', 'YOUR_APP_SECRET',
                      'USER_OAUTH_TOKEN', 'USER_OAUTH_TOKEN_SECRET')

    >>> requests.get(url, auth=auth)
    <Response [200]>

Per maggiori informazioni sul workflow di autenticazione OAuth, fate riferimento
al sito werb officiale di `OAuth`_. Per esempi e documentazione su
requests-oauthlib, consultate il repository GitHub `requests_oauthlib`_


Altre forme di Autenticazione
-----------------------------

Requests è strutturato per essere agganciabile in maniera semplice con altri
metodi di autenticazione.

La community open-source scrive di frequente nuovi handler di autenticazione
per forme di autenticazione più complesse o usate meno comunemente.
Alcuni dei migliori sono stati riuniti su GitHub sotto
l'`organizzazione Requests`_, tra cui:

- Kerberos_
- NTLM_

Se volete usare queste forme di autenticazione, visitate la loro pagina
GitHub e seguite le istruzioni relative.


Nuove forme di autenticazione
-----------------------------

Se non riuscite a trovare un'implementazione di una forma di autenticazione che
vi soddisfi, potete crearla voi stessi. Requests rende facile l'aggiunta di
una forma di autenticazione custom.

Per farlo, create una sottoclasse di :class:`AuthBase <requests.auth.AuthBase>` 
e implementate il metodo ``__call__()``::

    >>> import requests
    >>> class MyAuth(requests.auth.AuthBase):
    ...     def __call__(self, r):
    ...         # Implementate la vostra autenticazione
    ...         return r
    ...
    >>> url = 'http://httpbin.org/get'
    >>> requests.get(url, auth=MyAuth())
    <Response [200]>

Quando un handler di autenticazione è incluso in una richiesta, viene invocato
durante il setup della richiesta. Il metodo ``__call__`` deve quindi fare tutto 
ciò che è possibile per fare funzionare l'autenticazione. Alcune forme di
autenticazione aggiungeranno hook addizionali per mettere a disposizione
ulteriori funzionalità.

Potete trovare altri esempi su GitHub sotto l'`organizzazione Requests`_  e nel
file ``auth.py``.

.. _OAuth: http://oauth.net/
.. _requests_oauthlib: https://github.com/requests/requests-oauthlib
.. _Kerberos: https://github.com/requests/requests-kerberos
.. _NTLM: https://github.com/requests/requests-ntlm
.. _organizzazione Requests: https://github.com/requests

