.. _install:

Installazione
=============

Questa parte della documentazione illustra l'installazione di Requests.
Il primo passo per usare qualsiasi pacchetto software è installarlo nel modo corretto.


Distribute e Pip
----------------

Installare Requests è semplice con `pip <https://pip.pypa.io>`_, basta eseguire in un terminale::

    $ pip install requests

oppure, con `easy_install <http://pypi.python.org/pypi/setuptools>`_::

    $ easy_install requests

Ma `non dovreste proprio farlo <https://stackoverflow.com/questions/3220404/why-use-pip-over-easy-install>`_.


Ottenere il codice
------------------

Requests è sotto sviluppo attivo su GitHub, dove il codice è `sempre disponibile <https://github.com/kennethreitz/requests>`_.

Potete in alternativa clonare il repository pubblico::

    $ git clone git://github.com/kennethreitz/requests.git

Scaricare la `tarball <https://github.com/kennethreitz/requests/tarball/master>`_::

    $ curl -OL https://github.com/kennethreitz/requests/tarball/master

Oppure, scaricare la `zipball <https://github.com/kennethreitz/requests/zipball/master>`_::

    $ curl -OL https://github.com/kennethreitz/requests/zipball/master


Una volta ottenuta una copia dei sorgenti, potete includerlo nel vostro package Python,
o installarli facilmente nei vostri site-packages::

    $ python setup.py install
