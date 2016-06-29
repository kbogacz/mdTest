---
layout: post
title: Struktura projektu
order: 0
---

Zanim zaczniemy warto chwilę się zastanowić nad ogolną ideą struktury naszej aplikacji. Dobrze ustalona na początku 
pozwoli nam uniknąć czasochłonnych zmian. Dla lepszego poglądu jak taka struktura powinna 
wyglądać, warto przeanalizować poniższy przykład.

> Jeżeli interesowałeś się już wcześniej aplikacjami opartymi o framework angular, być może spotkałeś
> się z różnymi podejściami do tego, jakiej struktury powinniśmy używać. My wybraliśmy taką gdyż dobrze 
> sprawdza się w naszym projekcie, co nie oznacza że jest jedyna możliwość. 
{: .info}

<prism-js language="bash">
root-folder
├── bower.json
├── gulpfile.js
├── package.json
└── src
    ├── index.html
    ├── apis
    │   ├── api-1.js
    │   └── api-2.js
    ├── app
    │   ├── app.css
    │   └── app.js
    ├── common-component
    │   ├── common-component.css
    │   ├── common-component.html
    │   └── common-component.js
    ├── directives
    │   ├── directive-1
    │   │   └── directive-1.js
    │   └── directive-2
    │       ├── directive-2.css
    │       ├── directive-2.html
    │       └── directive-2.js
    ├── filters
    │   ├── active.js
    │   └── order-by.js
    └── routes
        ├── details
        │   ├── details.html
        │   └── details.js
        ├── root-page
        │   └── root-page.html
        └── search
            ├── search.html
            └── search.js
</prism-js>
Krótkie objaśnienie:

* `apis` - tutaj będziemy trzymać wszelkie pliki implementujące obsługę API. W naszej aplikacji będzie to np. plik *spotify.js*, w którym 
będziemy obsługiwać API pobierania danych ze Spotify.
* `app` - zawiera główne pliki aplikacji - zarówno style jak plik *javascript*, w którym deklarujemy nasz główny moduł
 oraz konfigurujemy naszą aplikację.
* `common-component` - jest to przykładowy komponent, którego będziemy używać w całej naszej aplikacji ( tutaj będzie to np. navbar ). 
Nie jest on bezpośrednio związany z poszczególnym routingiem, chcemy również żeby był reużywalny w wielu miejscach.
* `directives` - tutaj będziemy trzymać wszystkie dyrektywy, każda w oddzielnym katalogu. Tak jak to zostało przedstawione w przykładzie, 
w katalogu dyrektywy może znajdować się tylko plik *javascript*, lub również pliki stylów i *html* (więcej o dyrektywach dowiesz się później).
* `filters` - filtry, każdy w oddzielnym pliku.
* `routes` - katalog ten podzielimy na pod-katalogi, każdy dla poszczególnego routingu. Dla przykładu **root-page** będzie zawierało w 
środku plik *html* strony głównej, katalog **details** plik *html* i plik *javascript* z kontrolerem.


> #### Zadanie dla Ciebie:
> 
> * Przy implementowaniu kolejnych funkcjonalności aplikacji staraj się trzymać ww. zasad.
> * Pytania?
{: .warning}

