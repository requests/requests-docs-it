Come contribuire
================

Requests è un progetto attivamente mantenuto, e i contributi sono bene accetti!

#. Verificate se ci sono issue aperte o apritene una nuova per iniziare la discussione su un baco.
   Le issue marcata con il tag Contributor Friendly sono l'ideale per chi non è ancora molto familiare
   con la codebase.
#. Forkate `il repository <https://github.com/kennethreitz/requests>`_ su GitHub e iniziate ad apportare
   le vostre modifiche su una nuova branch.
#. Scrivete un test che dimostra che il baco è stato risolto.
#. Inviate una pull request e rompete l'anima ai maintainer finchè questa non viene mergiata e resa pubblica. :)
   Non scordatevi di aggiungere il vostro nome al file `AUTHORS <https://github.com/kennethreitz/requests/blob/master/AUTHORS.rst>`_.

Congelamento delle feature
--------------------------


Dalla versione v1.0.0, le feature di Requests sono state congelate. Le richieste per nuove feature e le Pull Request
che le implementano non saranno più accettate.

Dipendenze per lo sviluppo
--------------------------

Dovrete installare py.test per far girare la test suite di Requests::

    $ pip install -r requirements.txt
    $ py.test
    platform darwin -- Python 2.7.3 -- pytest-2.3.4
    collected 25 items

    test_requests.py .........................
    25 passed in 3.50 seconds

Ambienti di runtime
-------------------

Requests al momento supporta le seguenti versioni di Python:

- Python 2.6
- Python 2.7
- Python 3.1
- Python 3.2
- Python 3.3
- PyPy 1.9

Il supporto per Python 3.1 e 3.2 può essere abbandonato in futuro.

Google App Engine non sarà mai supportato ufficialmente. Le Pull Requests di compatibilità saranno accettate
solo se non complicheranno la codebase.


Siete fuori di testa?
---------------------

- Il supporto a SPDY sarebbe fantastico. Nessuna estensione C però.

Repackaging Downstream
----------------------

Se redistribuite Requests nei vostri pacchetti, ricordate che dovete anche redistribuire il file ``cacerts.pem`` affinchè
SSL funzioni correttamente.
