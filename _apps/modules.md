---
layout: post
title: Moduły
order: 4
---

Rozdzielenie aplikacji na moduły pozwala łatwiej utrzymywać porządek, czyni aplikację bardziej czytelną oraz łatwiejszą w zrozumieniu.
Nazewnictwo modułów w naszym przypadku jest silnie związane ze strukturą naszej aplikacji.

Proponujemy taki podział na moduły:

<prism-js language="bash">
acodemy-spotify                    
├── bower.json
├── gulpfile.js
├── package.json
└── src                            # moduł:
    ├── index.html
    ├── app                        
    │   ├── app.css                
    │   └── app.js                 # acodemy-app
    ├── apis
    │   └── spotify.js             # acodemy-app.apis.spotify
    ├── directives
    │   └── clearable-input
    │       └── clearable-input.js # acodemy-app.directives.clearable-input
    ├── webcomponents
    │   ├── clearable-input
    │   │   ├── clearable-input.html
    │   │   └── clearable-input.js
    │   └── play-button
    │       ├── play-button.html
    │       └── play-button.js
    ├── filters
    │   └── duration.js            # acodemy-app.filters.duration
    ├── navbar
    │   ├── navbar.css
    │   ├── navbar.html
    │   └── navbar.js              # acodemy-app.navbar
    └── routes
        └── search
        │   ├── search.html        
        │   └── search.js          # acodemy-app.routes.search
        ├── album                   
        │   └── index.html         
        └── artist                 
            └── index.html        
</prism-js>

Dzięki takiemu podziałowi możemy lepiej kontrolować zależności w naszej aplikacji. Każdy moduł powinien wymieniać wszystkie moduły, których potrzebuje do prawidłowego funkcjonowania. Moduły są singletonami, więc nie ma obawy, że któryś zostanie załadowany wielokrotnie.

<source-switcher>
<prism-js language="coffeescript">
# app/app.coffee
angular.module 'acodemy-app', [
  'acodemy-app.navbar'
  'acodemy-app.routes.search'
]

# navbar/navbar.coffee
angular.module 'acodemy-app.navbar', [
  'acodemy-app.routes.search'
  'acodemy-app.directives.clearable-input'
]

# routes/search/search.coffee
angular.module 'acodemy-app.routes.search', [
  'acodemy-app.apis.spotify'
  'acodemy-app.filters.duration'
]
.controller 'SearchRouteController', ($scope, SpotifyApi) ->
    ...
</prism-js>
<prism-js language="javascript">
// app/app.js
angular.module('acodemy-app', [
  'acodemy-app.navbar',
  'acodemy-app.routes.search'
])

// navbar/navbar.js
angular.module('acodemy-app.navbar', [
  'acodemy-app.routes.search',
  'acodemy-app.directives.clearable-input'
])

// routes/search/search.js
angular.module('acodemy-app.routes.search', [
  'acodemy-app.apis.spotify',
  'acodemy-app.filters.duration',
])
.controller('SearchRouteController', function($scope, SpotifyApi) {
    ...
});
</prism-js>
</source-switcher>

Jeśli uznamy, że ścieżka wyszukiwania nie jest już potrzebna, wystarczy tylko usunąć `'acodemy-app.routes.search'` z zależności. Wszystkie moduły, które były wykorzystywane tylko przez stronę wyszukiwania automatycznie nie będą używane.

> #### Zadanie dla Ciebie:
>
> * Dodaj prawidłowy podział na moduły.
> * Pytania?
{: .warning}

> #### ng-include
> 
> `ng-include` jest dyrektywą, za pomocą której można załączyć szablon 
> z innego pliku, np.:
> 
> <prism-js language="markup" escape>
> <div ng-include="'/routes/search/search.html'"></div>
> </prism-js>
{: .info}