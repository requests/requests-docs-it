Rendere esplicite le vulnerabilità
==================================

Se pensate di aver individuato una potenziale falla di sicurezza i requests,
siete pregati di comunicarla direttamente via e-mail a `sigmavirus24 <mailto:graffatcolmingov@gmail.com>`_
e `Lukasa <mailto:cory@lukasa.co.uk>`_ . **Non aprite alcuna issue pubblica a
riguardo.**

Le nostre PGP Key fingerprints sono:

- 0161 BB7E B208 B5E0 4FDC  9F81 D9DA 0A04 9113 F853 (@sigmavirus24)

- 90DC AE40 FEA7 4B14 9B70  662D F25F 2144 EEC1 373D (@lukasa)

Se la vostra madrelingua non è l'inglese, perfavore provate a descrivere il
problema e il suo impatto al meglio delle vostre capacità linguistiche. Per
entrare nel dettaglio, utilizzate la vostra lingua madre e cercheremo di
tradurla al meglio usando dei servizi online.

Siete pregati anche di includere il codice che avete usato per identificare il
problema e la minima porzione di codice necessaria per riprodurlo.

Perfavore non rivelate il problema a nessun altro. Recupereremo un
identificatore CVE se necessario e vi daremo la piena paternità della scoperta
sotto qualsiasi nome o alias vorrete fornirci. Vi chiederemo un modo per
identificarvi solo quando avremo trovato una soluzione al problema e potremo
pubblicarla in una release.

Rispetteremo la vostra privacy e renderemo pubblico il vostro coinvolgimento
solo se ci darete il permesso di farlo.

Processo
--------
Le seguenti informazioni riguardano il processo che il progetto requests seguirà
in risposta alla rivelazione di una vulnerabilità. Se state per comunicare una
vulnerabilità, questa parte della documentazione vi spiega come reagiremo alla
vostra segnalazione.


Timeline
~~~~~~~~

Quando riportate un problema, uno dei membri del progetto vi risponderà entro
*al massimo* due giorni. Nella maggior parte dei casi le risposte saranno più
rapide, di solito entro le 12 ore. Questa prima risposta servirà almeno da
conferma della ricezione del vostro report.

Se riusciremo a riprodurre velocemente il problema, la risposta iniziale 
conterrà anche una conferma dell'esistenza della issue. Se non ci riusciremo,
probabilmente chiederemo più informazioni per ripodurre lo scenario.

Il nostro obiettivo è di rilasciare una fix per ogni vulnerabilità entro due
settimane dalla notifica della sua esistenza. Questo può potenzialmente
portare ad una release ad intermi che disabilita la funzionalità mentre una
soluzione definitiva è in sviluppo, ma generalmente ci sarà una versione
completa rilasciata il prima possibile.

Lungo il processo di risoluzione vi terremo aggiornati sul progresso della fix.
Una volta che la fix è pronta, ve lo faremo sapere. Spesso vi richiederemo di
confermare che la fix risolve il problema nel vostro ambiente di lavoro, in
special modo se non siamo pienamente convinti della sua efficacia nel nostro
ambiente di lavoro.

Giunti a tal momento, ci prepareremo per la release. Se sarà necessario,
richiederemo un identificativo CVE, assegnandovi la piena attribuzione
della scoperta. Decideremo anche una data per la release e vi faremo sapere
quando sarà. Questa data di release sarà *sempre* un giorno feriale.

Quindi, contatteremo i principali downstream packagers per informarli di una
imminente patch di sicurezza di modo che si possano organizzare. In più,
questi packagers riceveranno la patch per tempo, così che possano rilasciare
in tempo i loro packages dipendenti. Al momento, la lista delle persone che
contattiamo attivamente *prima di ciascuna public release* è:

- Ralph Bean, Red Hat (@ralphbean)
- Daniele Tricoli, Debian (@eriol)

Informeremo queste persone almeno una settimana prima della nostra release 
pianificata per assicurarci che abbiano tempo a sufficienza per prepararsi.
Se pensate di dover essere inclusi nella lista, siete pregati di informare
uno dei maintainer agli indirizzi e-mail provvisti all'inizio del documento.

Il giorno della release, pusheremo la patch sul nostro repository pubblico,
aggiornando il changelog con la descrizione del problema e attribuendovi il
merito della scoperta. Faremo in seguito una release su PyPI contenente la patch.

Infine, pubblicizzeremo la release. Ciò avverrà attraverso e-mail alle
mailing list, tweet e tutti gli altri mezzi di comunicazione disponibili al 
core team.

Menzioneremo esplicitamente quali commit contengono la fix in modo che sia
facile per i distributori e gli utenti creare patch nelle proprie versioni di
requests se l'aggiornamento alla nuova release non è per loro cosa fattibile.


Storico delle CVEs
------------------

- Risolto nella versione 2.6.0

  - `CVE 2015-2296 <http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2015-2296>`_,
    segnalato da Matthew Daley di `BugFuzz <https://bugfuzz.com/>`_.

- Risolto nella versione 2.3.0

  - `CVE 2014-1829 <http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2014-1829>`_

  - `CVE 2014-1830 <http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2014-1830>`_