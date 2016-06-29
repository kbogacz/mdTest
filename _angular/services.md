---
layout: post
title: Serwisy
order: 3
---

Sformułowanie usługa ma wiele znaczeń w załeźności od kontekstu użycia.
Można mieć na myśli usługę HTTP lub na przykład zwykły obiekt, który przechowuje w sobie jakąś logike biznesową.

W świecie AngularJS usługa to zwykły obiekt, który ma za zadanie wykonać jakąś pracę. Niekoniecznie musi to robić
poprzez internet ale może być użyty do wykonania takich operacji np: `AJAX`.

## $http

Nasza aplikacja nadal odbiega od bardziej realistycznych implementacji, nikt we współczesnym świecie nie zapisuje tak dużych danych na sztywno w kodzie JavaScript. Znacznie lepszym rozwiązaniem byłoby pobranie danych bezpośrednio z [API Spotify](https://developer.spotify.com/web-api/search-item/).

W tradycyjnym JavaScipt można użyć zapytania `XHR` aby pobrać dokument lub odwołać się do zewnętrznej usługi, najprawdopodobniej znasz tę technikę pod kryptonimem `AJAX`. W AngularJS do takich zadań służą Services (pol. Usługi).
Usługa to nic innego jak mały kawałek kodu, który rozwiązuje powtarzający się problem lub zadanie.

Nas interesuje dokładnie jedna usługa, która nazywa się `$http`. Jej głównym zadaniem (jak sama nazwa wskazuje) jest obsługa komunikacji HTTP z serwerem, generalnie jest bardzo prosta i przyjemna w użyciu.

Aby skorzystać z usługi `$http` dodajemy ją jako kolejny argument do naszego kontrolera oraz przy okazji warto wyczyścić tę ogromną listę danych.

> #### Interface $http
> 
> `$http(config)` - wysyła zapytanie XHR zgodnie z podanymi parametrami, 
>                   między innymi:
> 
> - `method` - metoda HTTP, np. 'GET', 'POST'
> - `url` - adres na jaki ma zostać wysłane zapytanie
> - `params` - mapa parametrów GET, które zostaną dodane do adresu
> - `data` - dane, które mają być wysłane z zapytaniem np. typu POST
> - `headers` - mapa nagłówków, które zostaną umieszczone w zapytaniu
> 
> **Funkcje pomocnicze**:
> 
> `$http.delete(url, [config])`  
> `$http.get(url, [config])`  
> `$http.head(url, [config])`  
> `$http.patch(url, data, [config])`  
> `$http.post(url, data, [config])`  
> `$http.put(url, data, [config])`  
{: .info}

> #### API Spotify - wyszukiwanie
> 
> `GET https://api.spotify.com/v1/search`
> 
> **Parametry**
> 
> `q` - szukana fraza  
> `type` - lista zasobów, które mają być przeszukane, oddzielonych przecinkami, np.: `album,artist,track`  
> `limit` - liczba rezultatów na jednej stronie  
> `offset` - indeks pierwszego rezultatu, w połączeniu z limitem używany do paginacji  
> 
> **Przykład**
> 
> <prism-js language="javascript">
> GET https://api.spotify.com/v1/search?q=transistor&type=album&limit=1
> 
> {
>   "albums" : {
>     "href" : "https://api.spotify.com/v1/search?query=transistor&offset=0&limit=1&type=album",
>     "items" : [ {
>       "id" : "3B0PgLmgaW0gJth55ApWbw",
>       "images" : [ {
>         "height" : 640,
>         "url" : "https://i.scdn.co/image/6776280c479cbd4a09c363e1208e4aa40cb79e93",
>         "width" : 640
>       }, ... ],
>       "name" : "Transistor Original Soundtrack",
>       "type" : "album",
>       ...
>     } ],
>     "limit" : 1,
>     "next" : "https://api.spotify.com/v1/search?query=transistor&offset=1&limit=1&type=album",
>     "offset" : 0,
>     "previous" : null,
>     "total" : 156
>   }
> }
> </prism-js>
{: .info}

> #### Zadanie dla Ciebie:
> 
> * Zaimplementuj w `MainController` pobieranie wyników wyszukiwania 
>   przez usługę `$http`
> * Pytania?
{: .warning}

> #### Przeciążanie API
> 
> Aby uniknąć przeciążania API Spotify, dodaj dyrektywę 
> `ng-model-options` do pola wyszukiwania.  
> Za pomocą `ng-model-options` można opóźnić zapis do modelu, 
> tak aby nie robić zapytań za każdym wprowadzonym/usuniętym znakiem.
{: .danger}

## $location

`$location` jest serwisem parsującym url z adresu przeglądarki, umożliwiającym jego zmianę oraz zarządzającym historią otwartych podstron aplikacji.

> #### Interface $location <small>ważniejsze metody</small>
> 
> **Getters**  
> `$location.absUrl()` - pełny adres, np. 
> `http://example.com/some/page?ala=ma&kota`  
> `$location.host()` - nazwa host'a strony, np. `example.com`   
> 
> **Getters/Setters**  
> `$location.url([url])` - względny adres, np. `/some/page?ala=ma&kota`  
> `$location.path([path])` - aktualna ścieżka, np. `/some/page`  
> `$location.search()` - zwraca mapę aktualnych parametrów, np. `{ala: 'ma', kota: true}`  
> `$location.search(key, value)` - ustawia podany parametr
> 
> **Inne**  
> `$location.replace()` - jeśli zmienisz url za pomocą jednej z powyższych 
> metod, `$location` doda nowy adres do historii przeglądania dla każdej 
> zmiany. Wywołanie `.replace()` rozwiązuje ten problem przez zagregowanie 
> wszystkich aktualnych zmian i dodanie jednego nowego punktu w historii.   
{: .info}

> #### Zadania dodatkowe:
> 
> * Dodaj aktualnie wyszukiwaną frazę do adresu w przeglądarce
> * Dodaj czytanie wyszukiwanej frazy z adresu, żeby odświeżenie strony
>   nie czyściło wyszukiwania
{: .warning}

## Własne usługi

Dlaczego warto używać własnych usług?

* Jesteśmy w stanie podzielić kod na mniejsze logiczne fragmenty
* Taki kod może być użyty wielokrotnie w naszej aplikacji
* Single Responsibility Principle (SRP)
* Dependency injection - usługi można wstrzykiwać do naszych kontrolerów lub innych usług tak jak np: `$http` lub `$route`
* Łatwiejsze testowanie - mniejszy kod łatwiej się testuje

Dróg do stworzenia nowej usługi mamy kilka:

* `constant/value` - zapisuje wartość
* `factory` - obiektem zapisanym w pamięci jest to co usługa zwróci
* `service` - obiektem zapisanym w pamięci będzie `this`
* `provider` - to tzw usługa konfigurowalna, której możemy przekazać dodatkowe ustawienia na poziomie metody `config` naszego modułu 

### Constant i Value

`constant` i `value` są wykorzystywane do przechowywania stałych. Różnią się tym, że wartości zapisanych za pomocą `constant` można użyć w czasie konfiguracji `module.config(...)`. Pozwala to na np. dodanie nagłówka z tokenem autoryzującym zapytania do API przez usługę `$http`.

<source-switcher>
<prism-js language="coffeescript">
.constant 'constant', 'some constant'
.value 'value', 'some value'
.controller (constant, value) ->
  ...

# dobrze
.config (constant) ->
  ...

# źle
.config (value) ->
  ...
</prism-js>
<prism-js language="javascript">
.constant('constant', 'some constant')
.value('value', 'some value')
.controller('Controller', function(constant, value) {
  ...
})

// dobrze
.config(function(constant) {
  ...
})

// źle
.config(function(value) {
  ...
})
</prism-js>
</source-switcher>


### Factory

`factory` ma za zadanie stworzyć wartość/obiekt, który następnie będzie dostępny pod podaną nazwą. Wykorzystywana jest najczęściej, kiedy udostępniana wartość musi być dynamicznie obliczona lub korzysta z innych usług.

<source-switcher>
<prism-js language="coffeescript">
# przechowuje wartość, odpowiednik .value
.factory 'valueFactory', ->
  'dummy value'

# przechowuje prywatne zmienne
.factory 'msgFactory', () ->
  messages = []

  addMessage: (msg) -> messages.push msg
  getMessages: () -> messages
  delMessage: (index) -> messages.splice index, 1

.controller 'Controller', ($scope, msgFactory) ->
  $scope.notify = (msg) ->
    msgFactory.addMessage msg

# załączanie innych usług
.factory 'mixFactory', ($http, msgFactory, valueFactory) ->
...
</prism-js>
<prism-js language="javascript">
// przechowuje wartość, odpowiednik .value
.factory('valueFactory', function() {
  return 'dummy value';
})

// przechowuje prywatne zmienne
.factory('msgFactory', function() {
  var messages = [];

  return {
    addMessage: function(msg) { messages.push(msg); },
    getMessages: function() { return messages; },
    delMessage: function(index) { messages.splice(index, 1); }
  };
})

.controller('Controller', function($scope, msgFactory) {
  $scope.notify = function(msg) {
    msgFactory.addMessage(msg);
  };
})

// załączanie innych usług
.factory('mixFactory', function($http, msgFactory, valueFactory) {
...
</prism-js>
</source-switcher>

### Service

Jeżeli jesteś zwolennikiem bardziej obiektowego programowania to `service` jest dla Ciebie. W tym przypadku przekazujemy konstruktor obiektu, który zostanie wywołany w momencie pierwszego użycia serwisu.

<source-switcher>
<prism-js language="coffeescript">
.service 'dummyService', () ->
  privateVariable  = 1
  privateMethod = () -> 1 + 1

  @publicVariable = 'ala'
  @publicMethod = () -> 2 + 2

  return

.controller 'Controller', ($scope, dummyService) ->
  $scope.public = dummyService.publicVariable
  $scope.four = dummyService.publicMethod()
</prism-js>
<prism-js language="javascript">
.service('dummyService', function() {
  var privateVariable = 1;
  var privateMethod = function() { return 1 + 1; };

  this.publicVariable = 'ala';
  this.publicMethod = function() { return 2 + 2; };
})

.controller('Controller', function($scope, dummyService) {
  $scope.public = dummyService.publicVariable;
  $scope.four = dummyService.publicMethod();
})

</prism-js>
</source-switcher>

### Provider

`provider` jest najbardziej uniwersalnym sposobem rejestrowania usługi, który dodatkowo umożliwia konfigurowanie naszej usługi podczas inicjalizacji aplikacji.
Podobnie jak w przypadku `service`, przekazujemy konstruktor obiektu, który musi mieć metodę `$get` odpowiedzialną za stworzenie usługi, dużo łatwiej będzie to zrozumieć na przykładzie:

<source-switcher>
<prism-js language="coffeescript">
.provider 'appApi', () ->
  provider = this
  provider.defaults =
    baseUrl: '/api'
    token: ''

  provider.$get = ($http) ->
    defaults: provider.defaults
    get: (path) -> 
      $http.get "#{@defaults.baseUrl}/#{path}",
        headers:
          Authorization: "Token #{@defaults.token}"

  return
</prism-js>
<prism-js language="javascript">
.provider('appApi', function() {
  var provider = this;
  provider.defaults = {
    baseUrl: '/api',
    token: ''
  };

  provider.$get = function($http) {
    return {
      defaults: provider.defaults,
      get: function(path) {
        return $http.get(this.defaults.baseUrl + '/' + path, {
          headers: {
            Authorization: 'Token ' + this.defaults.token}'
          }
        });
      }
    };
  };
})
</prism-js>
</source-switcher>

Aby skonfigurować naszą usługę wystarczy:

<source-switcher>
<prism-js language="coffeescript">
.config (appApiProvider) ->
  appApiProvider.defaults.token = 'xxxxxxxx'
</prism-js>
<prism-js language="javascript">
.config(function(appApiProvider) {
  appApiProvider.defaults.token = 'xxxxxxxx';
})
</prism-js>
</source-switcher>

A wywołanie wygląda klasycznie:

<source-switcher>
<prism-js language="coffeescript">
.controller 'Controller', ($scope, appApi) ->
  appApi.get('users').then (response) ->
    $scope.users = reponse.data
</prism-js>
<prism-js language="javascript">
.controller('Controller', function($scope, appApi) {
  appApi.get('users').then(function(response) {
    $scope.users = reponse.data;
  });
}
</prism-js>
</source-switcher>

> Wszystkie usługi to obiekty typu `singleton`
{: .info}

> #### Zadanie dla Ciebie:
> 
> * Napisz usługę `SpotifyApi`, która będzie służyła do komunikacji z bazą
>   muzyki Spotify
> * Zaimplementuj metodę `SpotifyApi.search(query, types)` do przeszukiwania
>   Spotify
> * Podmień kod wyszukujący dane w `MainController` wykorzystując usługę 
>   `SpotifyApi`
> * Pytania?
{: .warning}

> #### Struktura
> 
> Umieść usługę `SpotifyApi` w pliku `src/apis/spotify.js`. 
{: .structure}

> #### Zadanie dodatkowe:
> 
> * Przeprowadź refaktoryzację usługi `SpotifyApi` na `provider` 
>   tak aby można było skonfigurować domyślne wartości dla: 
>   adresu api i limitu wyników na stronie
{: .warning}

## Efekt

![Serwisy efekt]({{ site.baseurl }}angular/img/services_result.png)

