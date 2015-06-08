krTheme Sphinx Style
====================

Questo repository contiene gli stili sphinx che Kenneth Reitz usa per la
maggior parte dei suoi progetti. E' un lavoro derivato dai temi di Mitsuhiko
per progetti Flask o relativi a Flask.
Per usare questo stile nella vostra documentazione Sphinx, seguite questi passi:

1. copiate questa cartella chiamandola _themes nella root della vostra documentazione.
   In alternativa, potete usare i sottomoduli git per scaricare la cartella nella vostra documentazione.

2. aggiungete questo codice al vostro file conf.py: ::

    sys.path.append(os.path.abspath('_themes'))
    html_theme_path = ['_themes']
    html_theme = 'flask'

Sono a disposizione i seguenti temi:

**kr**
    il tema standard per la documentazione flask per grossi progetti

**kr_small**
    tema one-page minimale. E' concepito per piccole librerie aggiuntive.

