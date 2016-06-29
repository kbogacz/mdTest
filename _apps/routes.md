---
layout: post
title: Ścieżki
order: 6
---

Kiedy nasza aplikacja zaczyna rosnąć dobrym pomysłem jest podział jej zawartości. Na zwykłych stronach robimy to zazwyczaj za pomocą różnych podstron natomiast bądź co bądź AngularJS jest to framework przeznaczony do tworzenia Single Page Applications, który obsługuje podstrony za pomocą techniki, która nazywa się `routing`.

`routing` oznacza ładowanie różnych widoków / szablonów w zależności od tego na jakim URL znajduje się użytkownik.

Dodatkowym problem SPA jest to że nie za dobrze współgrają z przeglądarkowym guzikiem _wstecz_ lub dodawaniem takiej strony do ulubionych, a wynika to z tego, że jak sama nazwa wskazuje SPA jest przeznaczona i ładowana na pojedynczej stronie.

W takim razie jak przekonać AngularJS żeby udawał, że jedna strona to tak naprawdę cały rozbudowany serwis? AngularJS zapewnia nam specialny moduł `ngRoute` z usługą `$route`, która obserwuje aktualny URL naszej strony i sprawdza jak przekłada się on na stan naszej aplikacji. Dodatkowo ustawia ona odpowiednie URL'e w zależności od tego gdzie nasz użytkownik zawędruje.

Same zmiany stanu naszej aplikacji są obsługiwane poprzez [hasztag](http://pl.wikipedia.org/wiki/Hasztag).


## Konfiguracja

Pierwszą rzeczą, którą musimy zrobić to dodać moduł `ngRoute` do zależności naszej aplikacji. 

Moglibyśmy dodać zależność bezpośrednio do modułu `acodemy-app` i tam skonfigurować wszystkie ścieżki, które obsługuje nasza aplikacja. Jednak lepszym rozwiązaniem będzie przeniesienie tych informacji do modułów poszczególnych ścieżek. Dzięki temu, aby usunąć jakąś ścieżkę wystarczy usunąć odpowiedni moduł z zależności aplikacji.

Zacznijmy od ścieżki `/search`. Do modułu `acodemy-app.routes.search` dodajmy 
`ngRoute`.

<source-switcher>
<prism-js language="coffeescript">
angular.module 'acodemy-app.routes.search', [
  'ngRoute'

  'acodemy-app.apis.spotify'
  'acodemy-app.filters.duration'
]
...
</prism-js>
<prism-js language="javascript">
angular.module('acodemy-app.routes.search', [
  'ngRoute',

  'acodemy-app.apis.spotify',
  'acodemy-app.filters.duration'
])
...
</prism-js>
</source-switcher>

Następnie musimy zarejestrować ścieżkę `/search`. Przed "startem" aplikacji, zanim wszystkie usługi zostaną stworzone, odbywa się konfiguracja usług. Możemy zgłosić potrzebę skonfigurowania usługi `$route` wywołaniem metody `module.config()`:

<source-switcher>
<prism-js language="coffeescript">
angular.module 'acodemy-app.routes.search', [
  'ngRoute'
  ...
]
.config ($routeProvider) ->
  ...
</prism-js>
<prism-js language="javascript">
angular.module('acodemy-app.routes.search', [
  'ngRoute',
  ...
])
.config(function($routeProvider) {
  ...
})
</prism-js>
</source-switcher>

`$routeProvider` powazala nam skonfigurować wszystkie ścieżki wraz z ich szablonami oraz kontrolerami. Sam `$routeProvider` posiada dwie metody, w których określamy:

* `when` - co jeśli url pasuje do ścieżki
* `otherwise` - co się dzieje w każdym innym przypadku

<source-switcher>
<prism-js language="coffeescript">
...
.config ($routeProvider) ->
  $routeProvider
    .when '/search',
      templateUrl: 'routes/search/search.html'
      controller: 'SearchRouteController'
      reloadOnSearch: false
</prism-js>
<prism-js language="javascript">
...
.config(function($routeProvider) {
  $routeProvider
    .when('/search', {
      templateUrl: 'routes/search/search.html',
      controller: 'SearchRouteController',
      reloadOnSearch: false
    });
})
</prism-js>
</source-switcher>

> #### ng-view
> 
> Gdy używamy routingu, template który powinien zostać zaserwowany dla podanej ścieżki zostanie włożone wewnątrz taga posiadającego dyrektywę `ng-view`
> 
{: .info}

> #### reloadOnSearch
> 
> Jeśli ustawimy `reloadOnSearch` na `false`, zmiany w parametrach
> URL'a (np. `?q=search`) nie będą powodować przeładowania widoku. 
> Zostanie jednak wysłane zdarzenie `$routeUpdate`, żeby poinformować 
> kontroler o zmianie URL'a.
{: .info}

> #### Zadanie dla Ciebie:
> 
> * Dodaj ścieżkę `/search` i zaktualizuj `SearchRouteController`
> * Spraw, aby domyślną ścieżką było `/search`
> * Pytania?
{: .warning }


## Widoki z parametrami

Nasza aplikacja z powodzeniem obsługuje już różne podstrony, choć mamy ją tylko jedną. Dużo ciekawiej by było gdyby w naszych ścieżkach można było przekazywać dodatkowe parametry i dodać szczyptę dynamizmu do naszej strony. Tylko jak?  

Metoda `when` w swoim pierwszym argumencie przyjmuje zmienną `path`, która może zawierać:

* nazwane grupy zaczynające się od **dwukropka** np: `:name`, które zostaną dopasowane do końca tekstu lub slasha
* nazwane grupy zaczynające się od **dwukropka** a kończące się **gwiazdką** np: `:name*` gdzie wszystkie znaki zostaną dopasowane
* nazwane grupy zaczynające się od **dwukropka** a kończące się **znakiem zapytania** np: `:name?` gedzie dana grupa jest opcjonalna

Dla ścieżki `/hi/:name` wywołanie `/hi/ala` pobierze wartość `ala` do atrybutu 
`name`. Jak to wygląda w praktyce?

<source-switcher>
<prism-js language="coffeescript">
...
$routeProvider
  .when '/some/url/:param',
    templateUrl: 'views/my_custom_view.html'
    controller: 'ParamCtrl'
  .when '/some/url/:param*/edit',
    templateUrl: 'views/my_custom_view.html'
    controller: 'WildcardParamCtrl'
  .when '/some/url/:param?',
    templateUrl: 'views/my_custom_view.html'
    controller: 'OptionalParamCtrl'
...
</prism-js>
<prism-js language="javascript">
...
$routeProvider
  .when('/some/url/:param', {
    templateUrl: 'views/my_custom_view.html',
    controller: 'ParamCtrl'
  })
  .when('/some/url/:param*/edit', {
    templateUrl: 'views/my_custom_view.html',
    controller: 'WildcardParamCtrl'
  })
  .when('/some/url/:param?', {
    templateUrl: 'views/my_custom_view.html',
    controller: 'OptionalParamCtrl'
  });
...
</prism-js>
</source-switcher>

## Kontroler <small>jak to odczytać?</small>

Aby przechwycić parametry z URL'a posłużymy się usługą `$routeParams`.

<source-switcher>
<prism-js language="coffeescript">
# routes/album/album.js
angular.module 'acodemy-app.routes.album', [
  'ngRoute'
  ...
]
.config ($routeProvider) ->
  $routeProvider
    .when '/album/:id', 
      template: 'routes/album/album.html',
      controller: 'AlbumRouteController'

.controller('AlbumRouteController', (
  $scope, $routeParams, SpotifyApi
) ->
  console.log($routeParams.id);
</prism-js>
<prism-js language="javascript">
// routes/album/album.js
angular.module('acodemy-app.routes.album', [
  'ngRoute',
  ...
])
.config(function($routeProvider) {
  $routeProvider
    .when('/album/:id', {
      template: 'routes/album/album.html',
      controller: 'AlbumRouteController'
    });
})
.controller('AlbumRouteController', function(
  $scope, $routeParams, SpotifyApi
) {
  console.log($routeParams.id);
});
</prism-js>
</source-switcher>


## Widok

Aby nasz użytkownik miał możliwość przejścia na naszą stronę z parametrem, trzeba dopisać w szablonie po prostu:

<prism-js language="markup" escape>{% raw %}
<a ng-href="#/album/{{ variable }}">Click me!</a>
{% endraw %}</prism-js>

> #### ng-href
> Warto sprawdzić czym się różni `ng-href` od `href`
{: .info}

> #### Zadanie dla Ciebie:
> 
> * Skonfiguruj ścieżkę `/album/:id` wyświetlającą szczegóły o danym albumie
>   na podstawie szablonu `routes/album/index.html`
> * Zaktualizuj linki, tak aby korzystały z powyższych ścieżek
> * Dodaj przejście do strony wyszukiwania kiedy wpiszę się coś w polu
>   wyszukiwania
> * Dodaj przejście do pustej strony wyszukiwania gdy kliknie się na 
>   logo aplikacji
> * Pytania?
{: .warning}

> #### Zadanie dodatkowe:
> 
> * Skonfiguruj ścieżkę `/artist/:id` wyświetlającą szczegóły o danym artyście
>   na podstawie szablonu `routes/artist/index.html`
{: .warning}

> #### API Spotify - albums, artists
> 
> `GET https://api.spotify.com/v1/albums/`  
> `GET https://api.spotify.com/v1/artists/`  
> 
> **Parametry**
> 
> `ids` - lista identyfikatorów albumów, oddzielonych przecinkami
> 
> **Przykład**
> 
> <prism-js language="javascript">
> GET https://api.spotify.com/v1/albums?ids=3B0PgLmgaW0gJth55ApWbw
> 
> {
>   "albums" : [ {
>     "id" : "3B0PgLmgaW0gJth55ApWbw",
>     "name" : "Transistor Original Soundtrack",
>     "release_date" : "2014-05-20",
>     ...
>     "images" : [ {
>       "width" : 640
>       "height" : 640,
>         "url" : "https://i.scdn.co/ime/6776280c479cbd4a09c363e1208e4aa40cb79e93",
>     }, ...],
>     "artists" : [ {
>       "id" : "0ZMWrgLff357yxLyEU77a1",
>       "name" : "Darren Korb",
>       ...
>     }, ...],
>     "tracks" : {
>       "items" : [ {
>         "artists" : [ {
>           "id" : "0ZMWrgLff357yxLyEU77a1",
>           "name" : "Darren Korb",
>           ...
>         }, ...],
>         "duration_ms" : 201324,
>         "id" : "4zmT3KiW5UVfzGSIkYbs0y",
>         "name" : "Old Friends",
>         "preview_url" : "https://p.scdn.co/mp3-priew/27d0aa224616b39d6469bc0e0bf27388e3cca973",
>         ...
>       }, ...],
>       "limit" : 50,
>       "next" : null,
>       "offset" : 0,
>       "previous" : null,
>       "total" : 23
>     }
>   }, ...]
> }
> </prism-js>
{: .info}

> #### API Spotify - dodatkowe informacje o artyście
> 
> Albumy danego artysty  
> `GET https://api.spotify.com/v1/artists/{id}/albums`
> 
> Najpopularniejsze utwory artysty  
> `GET https://api.spotify.com/v1/artists/{id}/top-tracks`
> 
> Podobni artyści  
> `GET https://api.spotify.com/v1/artists/{id}/related-artists`
{: .info}

> #### $q
> 
> Aby zagregować kilka zapytań, tak żeby obsłużyć je wszyskie jednym 
> callbackiem, można posłużyć się modułem `$q`. Posiada on metodę
> `$q.all(promises)`, która zwraca `promise`, który zostanie zrealizowany
> gdy wszystkie podane `promises` zostaną spełnione.
{: .info}

## Efekt

W naszej strukturce powinno pojawić się kilka plików:

<prism-js language="bash">
acodemy-spotify
├── bower.json
├── gulpfile.js
├── package.json
└── src
    ├── index.html
    ├── app
    │   ├── app.css
    │   └── app.js
    ├── apis
    │   └── spotify.js
    ├── directives
    │   └── clearable-input
    │       └── clearable-input.js
    ├── webcomponents
    │   ├── clearable-input
    │   │   ├── clearable-input.html
    │   │   └── clearable-input.js
    │   └── play-button
    │       ├── play-button.html
    │       └── play-button.js
    ├── filters
    │   └── duration.js
    ├── navbar
    │   ├── navbar.css
    │   ├── navbar.html
    │   └── navbar.js
    └── routes
        ├── album
        │   ├── album.html
        │   └── album.js
        ├── artist
        │   ├── artist.html
        │   └── artist.js
        └── search
            ├── search.html
            └── search.js
</prism-js>

Natomiast wizualnie szczegóły albumu/artysty powinny pezentować się tak:

![Album efekt]({{ site.baseurl }}apps/img/routes_album_result.png)

![Album efekt]({{ site.baseurl }}apps/img/routes_artist_result.png)

## $route

Mamy już podzieloną aplikację na trzy widoki. Warto w tym momencie wspomnieć o usłudze, która nazywa się `$route`. `$route` sam w sobie daje nam dostęp do tego co już skonfigurowaliśmy za pomocą `$routeProvider` oraz do wszystkich aktualnych parametrów powiązanych z aktualnie aktywnym URL'em.

Jeżeli dodamy jakikolwiek parametr (`myCustomVariable`) do konfiguracji `$routeProvider` w `app.coffee`/`app.js`:

<source-switcher>
<prism-js language="coffeescript">
...
$routeProvider
  .when '/album/:id',
    templateUrl: 'routes/album/album.html'
    controller: 'AlbumRouteController'
    myCustomVariable: 'myCustomValue'
...
</prism-js>
<prism-js language="javascript">
...
$routeProvider
  .when('/album/:id', {
    templateUrl: 'routes/album/album.html',
    controller: 'AlbumRouteController',
    myCustomVariable: 'myCustomValue'
  });
...
</prism-js>
</source-switcher>

Mamy możliwość pobrania go w kontrolerze za pomocą usługi `$route`:

<source-switcher>
<prism-js language="coffeescript">
.controller 'AlbumRouteController', ($scope, $route) ->
  console.log $route.current.myCustomVariable
  ...
</prism-js>
<prism-js language="javascript">
.controller('AlbumRouteController', function($scope, $route) {
  console.log($route.current.myCustomVariable);
  ...
});
</prism-js>
</source-switcher>

Dodatkowo możemy pobrać parametry z URL'a poprzez `current.params`, np: 
`http://localhost:9000/#/?myCustomVariable=myCustomValue`

<source-switcher>
<prism-js language="coffeescript">
.controller 'AlbumRouteController', ($scope, $route) ->
  console.log $route.current.params.myCustomVariable
  ...
</prism-js>
<prism-js language="javascript">
.controller('AlbumRouteController', function($scope, $route) {
  console.log($route.current.params.myCustomVariable);
  ...
})
</prism-js>
</source-switcher>

Usługa `$route` umożliwia nam też dostanie się do naszych _magicznych_ zmiennych takich jak `id` więc jeżeli odwiedzimy
`http://localhost:8888/#/album/3B0PgLmgaW0gJth55ApWbw`

<source-switcher>
<prism-js language="coffeescript">
.controller 'AlbumRouteController', ($scope, $route) ->
  console.log $route.current.params.id
  ...
</prism-js>
<prism-js language="javascript">
.controller('AlbumRouteController', function($scope, $route) {
  console.log($route.current.params.id);
  ...
});
</prism-js>
</source-switcher>

Kolejnym sposobem na dotarcie do naszych zmiennych jest `$route.current.pathParams`

<source-switcher>
<prism-js language="coffeescript">
.controller 'AlbumRouteController', ($scope, $route) ->
  console.log $route.current.pathParams.id
  ...
</prism-js>
<prism-js language="javascript">
.controller('AlbumRouteController', function($scope, $route) {
  console.log($route.current.pathParams.id);
  ...
});
</prism-js>
</source-switcher>

> #### params vs pathParams
> 
> Zmienna `params` przechowuje wszystkie zmienne z URL'a, te ze ścieżki 
> jak `id` oraz te z query jak `?myCustomVariable`. 
> `pathParams` przechowuje **jedynie** zmienne takie jak `id`.
{: .info}

Ostatnią przydatną metodą w usłudze `$route` jest `$route.reload()`. W sytuacji kiedy chcemy odświeżyć pojedynczy widok, bez ładowania
ponownie całej aplikacji wystarczy wywołać w kontrolerze `$route.reload()` a wszystko w danym widoku powróci do stanu początkowego.

## HTML5

Jak już pewnie zauważyliście w URL'ach towarzyszy nam `#`, można się tego drania pozbyć poprzez drobne zmiany w konfiguracji naszej aplikacji za pomocą 
`$locationProvidera`.

<source-switcher>
<prism-js language="coffeescript">
angular.module 'acodemy-app', [...]
.config ($locationProvider) ->
  $locationProvider.html5Mode true
  ...
</prism-js>
<prism-js language="javascript">
angular.module('acodemy-app', [...])
.config(function($locationProvider) {
  $locationProvider.html5Mode(true);
  ...
});
</prism-js>
</source-switcher>

Dodatkowo jeżeli chcemy aby wszysko działało poprawnie należy poprawić linki:

<prism-js language="markup" escape>{% raw %}
<a ng-href="/album/{{ album.id }}">
  {{ album.name }}
</a>
{% endraw %}</prism-js>

## Czy wszystko działa poprawnie?

Używając `HTML5 Mode` trzeba być świadomym że AngularJS wie jak obsługiwać nasze ścieżki / URL'e ale nasz serwer już nie. Nasz serwer HTTP założy, że skoro wywołujemy adres `/questions/123456` to fizycznie gdzieś powinien istnieć plik lub folder o takiej nazwie.

> #### Zadanie dla Ciebie:
> 
> * Wypróbuj `html5mode`
> * Przywróć aplikację do stanu sprzed `html5Mode`
{: .warning}


> Przedyskutuj z mentorem różnice między `$apply`, `$digest`, `$evalAsync`. Zapytaj o różnicę w działaniu pomiędzy `addEventListener'em`, a `ng-click`
{: .info}

## resolve

Już całkiem dobrze znamy usługę `$routeProvider` ale warto jeszcze wspomnieć o kilku jej właściwościach.

Gdy pobieramy dane z zewnętrznych usług mają one tendencję do zajmowania sporej ilości czasu. Kiedy AngularJS czeka na te dane nasz szablon może być widoczny pusty. Aby to zasymulować możemy użyć usługi `$timeout` w naszym kontrolerze:

<source-switcher>
<prism-js language="coffeescript">
.controller 'AlbumRouteController', (
  $scope, $routeParams, $timeout, SpotifyApi
) ->
  $timeout ->
    SpotifyApi.getAlbum $routeParams.id
    .then (album) ->
      ...
  , 3000
</prism-js>
<prism-js language="javascript">
.controller('AlbumRouteController', function(
  $scope, $routeParams, $timeout, SpotifyApi
) {
  $timeout(function {
    SpotifyApi.getAlbum($routeParams.id)
    .then(function(album) {
      ...
    });
  }, 3000);
})
</prism-js>
</source-switcher>

Możemy temu zapobiec wymuszając na AngularJS wstrzymanie ładowania się **widoku** do momentu pobrania danych.

<source-switcher>
<prism-js language="coffeescript">
$routeProvider
  .when '/album/:id',
    templateUrl: 'routes/album/album.html'
    controller: 'AlbumRouteController'
    resolve: 
      album: ($routeParams, SpotifyApi) ->
        SpotifyApi.getAlbum $routeParams.id

# W kontrolerze możemy to odczytać tak:

.controller 'AlbumRouteController', ($scope, album) ->
  $scope.album = album
</prism-js>
<prism-js language="javascript">
$routeProvider
  .when('/album/:id', {
    templateUrl: 'routes/album/album.html',
    controller: 'AlbumRouteController',
    resolve: {
      album: function($routeParams, SpotifyApi) {
        return SpotifyApi.getAlbum($routeParams.id);
      }
    }
  });

// W kontrolerze możemy to odczytać tak:

.controller('AlbumRouteController', function($scope, album) {
  $scope.album = album;
});
</prism-js>
</source-switcher>

Teraz AngularJS poczeka z wyświetleniem widoku na załadowanie się danych.
