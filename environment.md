# Environment

## Tool principali di sviluppo

La tecnologia utilizzata include i seguienti attori principali:

- python 2.7
- virtualenv
- django
- uwsgi
- south
- fabric
- git

### virtualenv
Serve ad isolare una installazione di pacchetty python relativi ad un progetto, in modo da renderlo facilmente trasportabile da un sistema all'altro e molto più facilmente controllabile

Un nuovo vitualenv si crea e attiva in questo modo:

    $ virtualenv --no-site-packages venv
    $ source venv/bin/activate

Dopo l'attivazione tutti i pacchetti verranno installati o disinstallati all'interno dell'installazione locale al visrtualenv

### south
Serve a gestire le migrazioni del database. In realtà penso che le versioni ultime di django lo integrino, ma il prgetto è stato iniziato con questa dipendenza che comunque funziona allo stesso modo. Praticamente ogni tabella per la quale si vogliano supportare migrazioni deve essere inizializzata con south. Successive cambiamenti al db devono essere "ufficializzati" con il comando

    $ python manage.py schemamigration app_name --auto

e la migrazione si applica con il comando

    $ python manage.py migrate app_name

Per iniziare è sufficiente conoscere le basi: https://south.readthedocs.org/en/latest/tutorial/part1.html

### fabric
Permette l'automazione del deploy. In pratica esegue comandi bash su una sessione ssh sul server di produzione. E' lui che mette tutto assieme, aggiorna il repository, lo clona nella directory servita dal web server, aggiusta i settaggi di produzione, effettua le migrazioni e riavvia i servizi.

### git
Tool per il versioning del codice. Un 'bare' repository sul server tiene il codice allo stato dell'arte. Tutti i progetti locali di sviluppo sono ad essi collegati in entrata uscita. Il repository in remoto per convenzione deve essere referenziato come 'production', 'origin' punterà quando il progetto sarà stabile al repository condiviso open source che starà su github.

## Setup environment
Le procedure per tirare su l'ambiente di sviluppo sono tutte contenute in questi 5 post:

- http://www.abidibo.net/blog/2012/04/30/deploy-django-applications-nginx-uwsgi-virtualenv-south-git-and-fabric-part-1/
- http://www.abidibo.net/blog/2012/05/23/deploy-django-applications-nginx-uwsgi-virtualenv-south-git-and-fabric-part-2/
- http://www.abidibo.net/blog/2012/06/04/deploy-django-applications-nginx-uwsgi-virtualenv-south-git-and-fabric-part-3/
- http://www.abidibo.net/blog/2012/06/20/deploy-django-applications-nginx-uwsgi-virtualenv-south-git-and-fabric-part-4/
- http://www.abidibo.net/blog/2012/06/29/deploy-django-applications-nginx-uwsgi-virtualenv-south-git-and-fabric-part-5/

### In poche e rozze parole
Lo sviluppo avviene in locale, utilizzando il server di sviluppo di django che facilità il compito di servire file statici e dinamici. La struttura delle cartelle rispecchia la struttura django style. Ogni qual volta sia necessario modificare il database devono essere create le migrazioni attraverso south.

Quando il codice è sufficientemente maturo e testato per passare in produzione si deve in genere:

- fare un collectstatic per collezionare i file statici all'interno di una directory
- pushare le modifiche in produzione
- lanciare il deploy e pregare

Tutto questo si traduce in 

    $ python manage.py collectstatic
    $ git commit -a -m "descrizione cambiamenti"
    $ git push production master
    $ fab production deploy

## Abilitare un nuovo progetto di sviluppo
Per iniziare a sviluppare da un nuovo pc si devono seguire i passi descritti qui: http://www.abidibo.net/blog/2013/01/23/deploy-django-application-set-another-machine/
