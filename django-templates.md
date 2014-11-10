# Django Templates
Il sistema di templating di django funziona per estensione, anche se non si è obbligati ad usarlo in questo modo.
Quando viene richiesta una pagina, l'url viene preso e viene cercato un matching tra le varie regole definite nei file project/urls.py e app_name/urls.py mediante regular expressions.
Il primo match viene considerato buono ed allora la palla passa alla vista definita insieme alla regola di matching. Unico compito di questa vista è di ritornare una risposta http.
Generalmente la vista riempie un context di variabili e lo passa ad un template per ottenere il corpo della risposta http.

E' chiaro quindi che ogni vista deve alla fin fine sputare fuori l'intero documento html. Sarebbero cazzi se ogni volta dovessimo scriverlo tutto, ecco allora che usiamo due strumenti che django ci mette a disposizione:

- l'estensione dei template
- i templatetags

## Estensione di template
Solitamente si procede in questo modo:

- creiamo un template base che rispecchi la struttura del sito. Generalmente questo template base contiene il codice della home, ma non è detto.
- tutte le parti "dinamiche" all'interno del template le racchiudiamo in tag {% block nome %}{% endblock %}
- a questo punto il template della singola vista può estendere il nostro template generale, andando a sovrascrivere solo i frammenti che gli interessa.

Infatti basta ridefinire il contenuto di  {% block name %} all'interno della vista per sovrascriverlo.
Si possono anche creare template che estendono template che estendono template che estendono template.....

Esempio.

base_site.html

    ...
    {% block content %}
    <section>
        <h1>Ciao questa è la home!</h1>
    </section>
    {% endblock %}
    {% block sidebar %}
    <section>
        <h1>Io sono una sidebar</h1>
    </section>
    {% endblock %}
    ...

my_view_template.html

    ...
    {% block content %}
    <section>
        <h1>Ciao io sono un output specifico della mia vista!</h1>
    </section>
    {% endblock %}
    ...

Sia la home che la mia vista avranno una stessa sidebar, ma il contenuto principale viene modificato.

##Il linguaggio dei template
I template sono file html, quindi vanno scritti in html. Ma.
Django ci mette a disposizione molti utili tag e filtri che ci consentono di utilizzare quasi un linguaggio di programmazione all'interno del template.
Possiamo fare cicli, definire alias, fare della matematica etc...

Inoltre abbiamo a disposizione dei filtri, per filtrare i dati. Per esempio per avere la lunghezza di una lista, per prenderne il primo elemento, per fare il cut di una stringa etc...

Qui: https://docs.djangoproject.com/en/dev/ref/templates/builtins/ si trova la lista completa dei templatetags e filtri supportati da django.

Ma non finisce qui... quasi ogni applicazione terze parti che aggiungiamo al nostro progetto metyte a disposizione altri tags e filtri per espletare le proprie funzioni, ad esempio sorl-thumbnail ci mette a disposizione un tag per generare al volo thumbnails di immagini

Ma non finisce qui... siccome a volte abbiamo bisogno di qualcosa di molto particolare anche noi possiamo scrivere i nostri tags e filtri, ciascuna applicazione che creiamo puo' averne ed anche il progetto, insomma ciascun package puo' averne.
