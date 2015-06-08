Filosofia di sviluppo
=====================

Requests è una libreria open ma con una propria visione, creata da uno sviluppatore open ma con una propria visione.


Stile di gestione del progetto
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`Kenneth Reitz <http://kennethreitz.org>`_ è il BDFL. Ha l'ultima parola su ogni decisione relativa al progetto Requests. Kenneth è responsabile della direzione e della forma che la libreria prende. Oltre a prendere decisioni sulla base dei suoi meriti tecnici, è responsabile delle decisioni sulla filosofia di sviluppo di Requests. Solo Kenneth può mergiare codice in Requests.

`Ian Cordasco <http://www.coglib.com/~icordasc/>`_ e `Cory Benfield <https://lukasa.co.uk/about/>`_ sono i collaboratori fondamentali. Sono responsabili della valutazione della prorità dei bug report, della revisione delle pull request e di mantenere Kenneth aggiornato con gli sviluppi sulla libreria. La gestione giornaliera del progetto è fatta da loro. Sono responsabili di giudicare se la richiesta di una nuova feature ha la
possibilità di essere accettata da Kenneth. Non hanno l'autorità di cambiare il codice o di mergiare modifiche, anche se possono modificare la
documentazione. La loro opinione non è quella definitiva.

Valori
~~~~~~

- La semplicità è sempre meglio della funzionalità.
- Date ascolto a tutti, e poi ignorateli.
- L'API è l'unica cosa che conta. Tutto il resto è secondario.
- Realizzate il caso d'uso che accontenta il 90% degli utenti. Ignorate i piagnucoloni.

Semantic Versioning
~~~~~~~~~~~~~~~~~~~

Per molti anni la community dell'open source è stata flagellata dalla distonia sui numeri di versione.
Il significato dei numeri di versione varia così tanto da progetto a progetto da farne in pratica perdere il significato.

Requests adotta il `Semantic Versioning <http://semver.org>`_. Questo manifesto cerca di porre fine alla follia del versioning 
attraverso un piccolo insieme di linee guida che voi ed i vostri colleghi potete usare nei vostri prossimi progetti.

Libreria Standard?
~~~~~~~~~~~~~~~~~~

Requests non rientra in alcun piano *attivo* per l'inclusione nella libreria standard Python. Questa decisione è stata discussa
al tempo con Guido ed alcuni sviluppatori del core team.

Sostanzialmente, la libreria standard Python sarebbe la morte della libreria requests. Sarebbe opportuno includere un modulo nella
libreria standard solo quando uno sviluppo attivo non è più necessario.

Requests ha da poco raggiunto la versione v1.0.0. Questa grande milestone ha segnato uno step significativo nella giusta direzione.

Pacchetti per le distribuzioni Linux
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Sono state create distribuzioni di Requests per molti repository Linux, tra cui: Ubuntu, Debian, RHEL e Arch.

Queste distribuzioni a volte sono fork divergenti, oppure non sono mantenute aggiornate con il codice e i bugfix più recenti. PyPI (e i suoi
mirror) e GitHub sono i canali ufficiali di distribuzione; le alternative non sono supportate dal progetto Requests.
