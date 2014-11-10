# Struttura folder/files progetto
Intanto facciamo una prima distinzione tra tipi di file:

- file statici: sono file che non cambiano (ah ah), quindi tutte le immagini, i css ed i javascript. Questi file non hanno bisogno di passare attraverso uwsgi e quindi possono essere serviti direttamente dal webserver
- file di codice (i file .py che contengono il codice)
- media: i files uploadati dall'applicativo

I file statici devono essere serviti direttamente dal web server, ma di questo in locale non dobbiamo preoccuparci, fa django per noi.
C'è pero' da sapere che questi file devono sempre stare dentro una dir "static" all'interno di un package. In questo modo quando li serviamo, e li serviamo utilizzando un url che per convenzione (ma si puo' campbiare) inizia con `/static`, django sa dove cercarli. Inoltre prima di committare e deployare in produzione lanciamo il comando

    $ python manage.py collectstatic

che non fa altro che ciclare su tutte le folder static e copiare il contenuto dentro ad una directory che noi indichiamo. In produzione i file statici saranno serviti proprio da questa directory.

I media file anche hanno bisogno di un minimo di configurazione, hanno un loro url, `/media` e vengono uploadati all'interno di una folder che configuriamo sempre nel fuile settings.py di cui parlero' dopo.

Quando creiamo un progetto django ci crea automaticamente alcuni files e dirs. Per convenzione diciamo che la ROOT del nostro progetto è la directory all'interno della quale si trova il file manage.py che contiene tutti i comandi che utilizziamo da shell in django. Per convenzione diciamo anche che il nostro progetto si chiami torinometeo.

Allora dentro la directory root avremo una directory di nome torinometeo. vediamo cosa c'è dentro.

## La directory torinometeo
Dentro django crea per noi una serie di file importanti, tra i quali:

- settings.py
- urls.py

Settings è il file di configurazione. Contiene costanti utilizzate da tutto l'applicativo, informazioni per accedere al db, configurazione di singoli pacchetti installati e l'elenco di applicazioni installate etc...
Nella ROOT poi teniamo un file settings_production.py. Quando facciamo una modifica nel settings.py dobbiamo rifletterla anche su quello di produzione a manina. Questo perché il settings.py viene letto nello sviluppo in locale, ma quando deployamo sul server ci sono dei comandi nel fabfile di fabric che copiano il settings_production.py al posto del settings.py (principalmente perché i dati di accesso al db sono diversi ed anche alcune costanti per il debug, ma non solo...)

Urls è il primo file che viene preso in considerazione nel momento in cui si deve rispovere l'url. Non è pero' cosa buona inserire tutti i matching degli url di tutte le app del nostro porgetto dentro questo file. Django ama l'incapsulamento e ci tiene a tenere le applicazioni come mondi separati per il riuso del codice. Quindi spesso questo file urls.py non fa altro che includere altru urls.py (di tutte le applicazioni installate) quando l'url inizia per una certa stringa.
Esempio:

    # Wire up our API using automatic URL routing.
    # Additionally, we include login URLs for the browseable API.
    urlpatterns = patterns('',
        #url(r'^$', TemplateView.as_view(template_name='base_site.html'), name='home'),
        url(r'^$', include('frontend.urls')),
        url(r'^ckeditor/', include('ckeditor.urls')),
        url(r'^admin/', include(admin.site.urls)),
        url(r'^djangular/', include('djangular.urls')),

        url(r'^blog/', include('blog.urls')),
        url(r'^realtime/', include('realtime.urls')),
        url(r'^webcam/', include('webcam.urls')),
    )

Noterete poi che dentro a questa directory compare una directory "static". Ecco qui dentro ci stanno tutti i file statici condivisi da tutte le applicazioni e che quindi non avrebbe senso includere in una o tutte le singole applicazioni. Dentro a static c'è un'ulteriore directory con il nome del progetto e poi dentro finalmente le dir dei file statici. Questo perché nelle ultime verisoni django ha deciso così, anche per le singole applicazioni avremo `app_name/static/app_name/css/example.css`

## Cos'è la directory static dentro la ROOT directory?
E' la cartella che ho configurato come contenitore dei file statici una volta che sono collezionati dal comando visto sopra. Quella che contiene tutti i millemila file statici di tutte le applicazioni e che viene usata per servire i file direttamente dal web server.

## Esempio di struttura di una app

Quando creiamo un'app

    $ python manage.py startapp blog

django ci crea già un po' di file, altri li aggiungiamo poi noi se è il caso.

## models.py

Questo file contiene la descrizione dei modelli. Cioè dice quali oggetti tratta la nostra applicazione, quali campi hanno questi oggetti e quali relazioni intercorrono tra oggetti diversi.
Insomma definisce classi di tipo model del pattern MVC, dove i campi vengono definiti per bene uno ad uno. Questo consente a django di autogenerare il db e cosa non da poco tutta l'interfaccia amministrativa per gestire i dati degli oggetti.

## views.py

Questo file contiene la definizione ed implementazione delle viste dell'applicazione, cioè le funzioni o classi che possono essere richiamate quando il matching di un url pattern da esito positivo.
Le viste un tempo venivano definite come funzioni, ora è preferibile definirle come classi. Questo perché django ci regala una miriade di mixin (ingredienti) da poter mischiare per aggiungere funzionalità alla nostra vista. Ad esempio possiamo dire che questa vista ha le funzionalità di una scheda dettaglio ed un form di modifica, mettiamo insiemen i 2 mixin ed abbiamo la pappa pronta.

## Altri file che di solito creiamo dentro ad una app
