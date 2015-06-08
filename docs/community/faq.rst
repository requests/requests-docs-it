.. _faq:

Frequently Asked Questions
==========================

Questa parte della documentazione cerca di rispondere alle domande
più comuni su Requests

Encoding dei dati?
------------------

Requests decomprime automaticamente le risposte compresse gzip, e
fa del suo meglio per decodificare il contenuto delle risposte in Unicode
quando è possibile.

Se serve, potete anche accedere al contenuto raw delle risposte (e pure al
socket sottostante)


Custom User-Agents?
-------------------

Requests vi consente di sovrascrivere in maniera semplice l'header User-Agent, così
come ogni altro header HTTP.


Perchè Requests e non Httplib2?
-------------------------------

Chris Adams ha scritto un'eccellente nota a riguardo su
`Hacker News <http://news.ycombinator.com/item?id=2884406>`_:

    httplib2 è parte del motivo per cui dovreste usare requests: è assai rispettabile come client
    ma non è documentata sufficienza e richiede ancora troppo codice per effettuare anche operazioni
    di base. Apprezzo quello che httplib2 cerca di fare, che la costruzione di una libreria HTTP
    moderna richiede miriadi di scocciature in codice di basso livello, ma in maniera franca vi dico:
    usate requests. Kenneth Reitz è molto motivato e ha un'idea precisa di quanto le cose più usate 
    debbano essere semplici, mentre httplib2 sembra più un esercizio accademico che un qualcosa che
    la gente può usare per costruire sistemi reali[1].

    Vi rivelo una cosa: sono elencato nel file AUTHORS di requests ma posso accreditarmi solo, oh,
    circa lo 0.0001% della sua magnificenza.

    1. http://code.google.com/p/httplib2/issues/detail?id=96 è un ottimo esempio:
    un bug rognoso che ha infastidito tante persone, una fix è rimasta disponibile per mesi e
    ha funzionato alla grande quando l'ho inclusa in una fork e ho scaricato un paio di TB con
    essa, ma ci ha messo più di un anno per essere inclusa nel trunk e ancora più tempo ad 
    arrivare su PyPI, dove tutti gli altri progetti che richiedevano "httplib2" la attendevano.


Supporto per Python 3?
----------------------

Si! Ecco una lista di versioni Python che sono ufficialmente supportate:

* Python 2.6
* Python 2.7
* Python 3.1
* Python 3.2
* Python 3.3
* Python 3.4
* PyPy 1.9
* PyPy 2.2

Cosa significano gli errori "hostname doesn't match"?
-----------------------------------------------------

Questi errori accadono quando la :ref:`verifica del certificato SSL <verification>`
fallisce: non c'è matching tra il certificato inviato in risposta dal server e l'hostname
che Requests crede di contattare. Se siete sicuri che il setup SSL del server è corretto
(per sempio, perchè riuscite a visitare il sito con il vostro browser) e
state utilizzando Python 2.6 o 2.7, una soluzione possibile è abilitare la
Server-Name-Indication.

`Server-Name-Indication`_, o SNI, è un'estensione ufficiale di SSL che prevede che i
client dicano al server quale hostname stanno contattando. Questo è importante se
i server usano il `Virtual Hosting`_. Infatti quando questi server ospitano più di un
sito su SSL devono essere in grado di ritornare il certificato SSL giusto sulla base
dell'hostname a cui i client si connettono.

Il modulo SSL di Python3 include il supporto nativo per SNI. Questa feature non è stata
riportata su Python2. Per maggiori dettagli sull'uso di SNI con Requests su Python2
si faccia riferimento a questa `risposta su Stack Overflow`_.

.. _`Server-Name-Indication`: https://en.wikipedia.org/wiki/Server_Name_Indication
.. _`virtual hosting`: https://en.wikipedia.org/wiki/Virtual_hosting
.. _`Stack Overflow answer`: https://stackoverflow.com/questions/18578439/using-requests-with-tls-doesnt-give-sni-support/18579484#18579484
