Requests: HTTP per gli Esseri Umani
===================================

.. image:: https://img.shields.io/pypi/v/requests.svg
    :target: https://pypi.python.org/pypi/requests

.. image:: https://img.shields.io/pypi/dm/requests.svg
        :target: https://pypi.python.org/pypi/requests




Requests è una libreria HTTP con licenza Apache2, scritta in Python per gli
Esseri Umani.

La maggior parte dei moduli Python per inviare richieste HTTP sono fortemente
prolissi e complicati da usare. Il modulo urllib2 della libreria standard Python
mette a disposizione quasi tutte le principali funzionalità HTTP ma la sua
interfaccia è molto frastagliata. Serve molto lavoro (addirittura anche
l'overriding di metodi) per realizzare il più semplice dei task.

Le cose non dovrebbero funzionare così. Non in Python.

.. code-block:: python

    >>> r = requests.get('https://api.github.com', auth=('user', 'pass'))
    >>> r.status_code
    204
    >>> r.headers['content-type']
    'application/json'
    >>> r.text
    ...

Guardate `un codice che fa le stesse cose, ma senza Requests <https://gist.github.com/973705>`_.

Requests vi consente di inviare richieste HTTP/1.1. Potete aggiungere headers,
form data, file multipart e parametri tramite semplici dizionari Python, e potete
accedere ai dati della risposta nello stesso modo. Si basa su httplib e `urllib3
<https://github.com/shazow/urllib3>`_, ma fa tutto il lavoraccio dietro le
quinte di modo che non ne dobbiate sappiare niente.


Caratteristiche
---------------

- Domini e URL Internazionali
- Keep-Alive e Connection Pooling
- Sessioni basate sui cookie
- Verifica SSL come la fanno i browser
- Autenticazione Basic/Digest
- Cookie chiave/valore
- Decompressione automatica dei dati
- Corpo delle risposte in Unicode
- Upload di file multipart
- Timeout sulle connessioni
- Thread-safety
- Supporto ai proxy HTTP(S)


Installazione
-------------

Installare Requests è semplice:

.. code-block:: bash

    $ pip install requests


Documentazione
--------------

La documentazione è disponibile all'indirizzo: http://docs.python-requests.org/.


Come contribuire
----------------

#. Cercate una issue aperta o apritene una nuova per iniziare a discutere di unanuova feature o di un baco individuato. Le issue taggate con `Contributor Friendly`_  sono l'ideale per cominciare se non siete ancora molto familiari con la codebase.
#. Se non sapete come procedere su una issue o sulle vostre modifiche, siate liberi di contattare @sigmavirus24 e lui vi darà supporto via email, Skype, pair-programming da remoto o qualsiasi mezzo che vi sia congeniale.
#. Forkate `il repository`_ su GitHub per iniziare a fare le vostre modifiche nella **master** branch (o su una branch nuova creata da essa).
#. Scrivete un test che dimostra che il baco è stato risolto o che la nuova feature si comporta come ci si attende.
#. Inviate una pull request e rompete l'anima ai maintainer finchè non viene incorporata e resa pubblica :) Ricordatevi di aggiungere il vostro nome alla lista degli AUTHORS_.

.. _`il repository`: http://github.com/kennethreitz/requests
.. _AUTHORS: https://github.com/kennethreitz/requests/blob/master/AUTHORS.rst
.. _Contributor Friendly: https://github.com/kennethreitz/requests/issues?direction=desc&labels=Contributor+Friendly&page=1&sort=updated&state=open
