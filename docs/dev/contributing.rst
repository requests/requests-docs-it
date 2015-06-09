.. _contributing:

Guida per i collaboratori
=========================

Se state leggendo questa pagina probabilmente siete interessati a contribuire a
Requests. Come prima cosa, vi diciamo: grazie! I progetti open source vivono e
muoiono sul supporto che ricevono dalle altre persone, e il fatto stesso che
stiate considerando di supportare Requests è molto generoso da parte vostra.

Questo documento individua alcune linee guida e alcuni consigli per contribuire
a Requests. Se state considerando di contribuire, cominciate a leggere bene
questo documento e a farvi un'idea di come si fa a contribuire al progetto. Per
ogni domanda, sentitevi liberi di contattare `Ian Cordasco`_ o `Cory Benfield`_,
i maintainer principali.

Questa guida è dicisa in sezioni sulla tipologia di contributo che state
considerando di fare, con una sezione che tratta le linee guida generali per
tutti i collaboratori.

.. _Ian Cordasco: http://www.coglib.com/~icordasc/
.. _Cory Benfield: https://lukasa.co.uk/about


Per ogni genere di contributo
-----------------------------

Siate cordiali
~~~~~~~~~~~~~~

**Siate cordiali o state lontani.**

Requests ha una regola importante che governa tutte le tipologie di contributo,
inclusi i bug report o la richiesta di feature nuove. Questa regola d'oro è
`siate cordiali o state lontani`_. **Tutti i contributi sono bene accetti**,
fintanto che tutte le persone coinvolte vengono trattate con rispetto.

.. _siate cordiali o state lontani: http://kennethreitz.org/be-cordial-or-be-on-your-way/

.. _early-feedback:

Cercate un feedback rapido
~~~~~~~~~~~~~~~~~~~~~~~~~~

Se contribuite, non tenete per voi il vostro lavoro fino a che non è perfetto e 
completo. Date una mano a tutti se cercate di avere feedback sul vostro lavoro
il prima possible. Inviare una versione draft e incompleta del vostro lavoro per
ottenere feedback non pregiudicherà in alcun modo le vostre chance di ottenere
che esso venga accettato, anzi può evitarvi di fare un mucchio di lavoro su
un contributo che in realtà potrebbe non essere adatto al progetto.

Idoneità dei contributi
~~~~~~~~~~~~~~~~~~~~~~~

Il project maintainer ha l'ultima parola sull'idoneità di un contributo al
progetto Requests. Tutti i contributi saranno considerati, ma certe volte 
capiterà che alcuni contributi saranno rigettati perchè non sono adatti al
progetto.

Se il vostro contributo viene rigettato, non disperate! Se avete seguito queste
linee guida avrete la miglior chance di vedere accettato il vostro prossimo
contributo.


Contributi al codice
--------------------

Passi
~~~~~

Se contribuite al codice, servitevi di questa checklist:

1. Forkate il repository su GitHub.
2. Fate girare i test per stabilire se passano tutti nel vostro ambiente.
   In caso negativo, cercate di capire perchè falliscono. Se non riuscite a
   capirlo da soli, compilate un bug report seguendo i passi descritti in
   questo documento: :ref:`bug-reports`.
3. Scrivete dei test che dimostrino l'esistenza del baco o la mancanza della
   nuova feature. Accertatevi che questi test falliscano.
4. Apportate le vostre modifiche al codice.
5. Fate girare di nuovo l'intera test suite, avendo conferma che tutti i test
   passano *inclusi quelli che avete aggiunto in precedenza*.
6. Inviate una Pull Request su GitHub sulla ``master`` branch del repository
   principale. Le Pull Requests di GitHub sono il metodo che usiamo nel progetto
   per inviare i contributi al codice.

Le prossime sottosezioni entrano più in dettaglio su alcuni dei punti sopra
illustrati.

Revisione del codice
~~~~~~~~~~~~~~~~~~~~

I contributi non saranno mergiati nella codebase finchè il codice non sarà
rivisto. Dovreste implementare ogni feedback che ricevete a livello di revisione
del codice, a meno che non siate in forte disaccordo con essi. In tale caso,
dovreste spiegare il perchè in maniera chiara e pacata. Se, anche dopo averlo
fatto, il feedback non viene modificate, potete o implementare il feedback
oppure ritirare il vostro contributo.


Nuovi collaboratori
~~~~~~~~~~~~~~~~~~~

Se non avi siete mai accostati all'Open Source o siete relativamente nuovi,
benvenuti! Requests vuol essere un'introduzione gentile al mondo dell'Open
Source. Se siete indecisi su come contribuire al meglio, perfavore prendete in
considerazione di mandare una e-mail ad uno dei maintainer (riportati sopra) e
chiedere aiuto.

Leggete anche la sezione :ref:`early-feedback`.

Contributi alla documentazione
------------------------------

Le migliorie alla documentazione sono sempre le benvenute! I file della
documentazione risiedono nella cartella ``docs/`` della codebase. Sono scritti
in `reStructuredText`_, e utilizziamo `Sphinx`_ per generare l'intero corpo della
documentazione.

Quando contribuite alla documentazione, siete pregati di seguire lo stile dei
file di documentazione. Ciò significa un limitie di 79 colonne nei file di testo
e uno stile di prosa semi-formale.

.. _reStructuredText: http://docutils.sourceforge.net/rst.html
.. _Sphinx: http://sphinx-doc.org/index.html


.. _bug-reports:

Bug Report
----------

I bug report sono di vitale importanza! Prima di notificare un bug, perfavore
leggete le `issues di GitHub`_, **sia quelle aperte che quelle chiuse**, per
avere conferma che il baco non sia già stato segnalato in passato. I bug report
duplicati sono una grossa perdita di tempo per gli altri collabortori e
dovrebbero essere assolutamente evitati.

.. _issues di GitHub: https://github.com/kennethreitz/requests/issues


Richiesta di nuove feature
--------------------------

Requests è in un perpetuo stato di congelamento delle feature. I maintainers
credono che la libreria contenga tutte le principali feature utili alla
stragrande maggioranza degli utenti.

Se credete che manchi una feature, siate liberi di inviare una richiesta ma
sappiate che con ogni probabilità la richiesta non sarà accettata.
