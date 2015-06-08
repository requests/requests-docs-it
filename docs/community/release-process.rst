Processo e regole di release
============================

.. versionadded:: v2.6.2

Dalla prima release successiva alla ``v2.6.2``, le regole che seguono verranno
usate per aiutare il core team di Requests a produrre una nuova release.

Major Release
-------------

Una major release include dei cambiamenti non retrocompatibili. Quando ad essa
viene data una versione, il suo formato è ``vX.0.0``. Ad esempio, se la release precedente
fosse la ``v10.2.7``, la successiva sarebbe la ``v11.0.0``.

I cambiamenti non retrocompatibili distruggono la compatibilità con le versioni precedenti.
Se nel progetto si modificasse l'attributo ``text`` di una ``Response`` o di un metodo,
questo sarebbe possibile solamente in una major release.

Le major release possono anche includere risoluzioni a vari bachi e aggiornamenti
a package di vendor esterni. Gli sviluppatori del core team di Requests si impegnano per
offrire una buona user experience: ciò significa che ci impegnamo anche a mantenere la
retrocompatibilità il più possibile. Le major release saranno poco frequenti e richiederanno
delle ottime giustificazioni prima di essere considerate.

Minor Release
-------------

Una minor release è priva di modifiche non retrocompatibili ma può includere risoluzioni a
vari bachi e aggiornamenti a package di vendor esterni. Ad esempio, se la release precedente
fosse la ``v10.2.7``, una minor release sarebbe versionata come ``v10.3.0``.

Le minor release saranno retrocompatibili con le releease che hanno lo stesso numero di
versione major. Altrimenti detto, tutte le versioni che iniziano con ``v10.`` dovrebbero 
essere compatibili tra di loro.

Hotfix Release
--------------

Una hotfix release include solo risoluzioni a bachi che non erano state incluse quando
è stata rilasciata la versione precedente del progetto. Se la versione precedente di Requests
fosse la ``v10.2.7``, una hotfix release sarebbe versionata come ``v10.2.8``.

Dalla versione ``v2.6.2``, le hotfix release **non** includono aggiornamenti a package di vendor esterni.

Ragionamento
------------
Nelle serie di release 2.5 e 2.6, il core team di Requests ha aggiornato le dipendenze esterne,
causando a se stesso e agli utenti di bei mal di testa. Per evitare questo genere di situazioni,
stiamo costruendo un corpo di regole per stabilire concretamente le aspettative sulle release.
