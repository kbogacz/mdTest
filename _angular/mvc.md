---
layout: post
title: MVC
order: 1
---

Pracując w AngularJS organizujemy projekt inaczej niż w klasycznym JavsScript.
Jednym z popularniejszych sposobów organizacji projektu jest **MVC**.

* **M**odel - odpowiada za dane w aplikacji. Mogą one być pobierane z plików JSON, zewnętrznych usług lub z bazy danych.
* **V**iew - aby zaprezentować dane z modelu potrzebny jest nam widok.
Widok w AngularJS to nic innego jak dokuemnt HTML wraz ze składnią systemu szablonów `{% raw %}{{}}{% endraw %}`
* **C**ontroller - to kod JavaScipt który łączy model i widok w całość oraz pozwala na przeprowadzanie interakcji z użytkownikiem.

## Model

Model to dane pokazywane użytkownikom w widokach, z którymi użytkownicy mogą wchodzić w interakcje.

<source-switcher>
<prism-js language="coffeescript">
album = {
  id: "3B0PgLmgaW0gJth55ApWbw"
  image: "https://i.scdn.co/image/fa8a3b68578c65fff17f59cc5e5058b4e2fc48b2"
  name: "Transistor Original Soundtrack"
}
</prism-js>
<prism-js language="javascript">
var album = {
  id: "3B0PgLmgaW0gJth55ApWbw",
  image: "https://i.scdn.co/image/fa8a3b68578c65fff17f59cc5e5058b4e2fc48b2",
  name: "Transistor Original Soundtrack"
};
</prism-js>
</source-switcher>

> Modelem może być dowolny obiekt JavaScript
{: .info}

## Widok (ang. View)

Widok to warstwa reprezentacyjna czyli to co de facto użytkownik widzi na ekranie przeglądarki.
W `AngularJS` to nic innego jak kod HTML okraszony wyrażeniami `{% raw %}{{}}{% endraw %}`.
Warto wspomnieć, że wyrażenia w `{% raw %}{{}}{% endraw %}` są kompilowane przez AngularJS i zachowują się prawie jak zwykły JavaScript
(obsługują wyrażenia arytmetyczne, logiczne, konkatenacje etc).


<prism-js language="markup" escape>{% raw %}<!--
<a href="/albums/{{ album.id }}">
  <img src="{{ album.image }}">
  {{ album.name }}
</a>
-->{% endraw %}</prism-js>


## Controller

Implementacja kontrolera  w AngularJS polega na dodaniu funkcji (konstruktora), której argumentami są nazwy wykorzystywanych usług ($scope, $http, $routeParams, etc.).
Dla naszych potrzeb niezbędna jest usługa `$scope`.
`$scope` jest obiektem łączącym Widok i Kontroler, stanowi on kontekst dla widoku (`$scope.model` można wyświetlić przez `{% raw %}{{ model }}{% endraw %}`).

> #### $rootScope
>
> Hierarchia `$scope'ów` w aplikacji jest drzewem, którego korzeniem jest
> zawsze dostępny `$rootScope`. Aby go użyć w kontrolerze, wystarczy
> dodać go do listy wymaganych usług.
{: .info}


> Przedyskutuj z mentorem czym różnią się rozwiązania w zagnieżdżeniach `$scope'ów`: `$parent`, `controlerAs`, `prototype inheritance`.
{: .info}

Dodatkowo, aby powiązać widok z naszym kontrolerem musimy posłużyć
się dyrektywą ng-controller. Określa ona, który kontroler ma odpowiadać za dany element (i jego zawartość).

<prism-js language="markup" escape>{% raw %}<!--
<div ng-controller="AlbumController">
  <a href="/albums/{{ album.id }}">
    <img src="{{ album.image }}">
    {{ album.name }}
  </a>
</div>
-->{% endraw %}</prism-js>

<source-switcher>
<prism-js language="coffeescript">
angular.module('app')
.controller 'AlbumController', ($scope) ->
  $scope.album =
    id: "3B0PgLmgaW0gJth55ApWbw"
    image: "https://i.scdn.co/image/fa8a3b68578c65fff17f59cc5e5058b4e2fc48b2"
    name: "Transistor Original Soundtrack"
</prism-js>
<prism-js language="javascript">
angular.module('app')
.controller('AlbumController', function($scope) {
  $scope.album = {
    id: "3B0PgLmgaW0gJth55ApWbw",
    image: "https://i.scdn.co/image/fa8a3b68578c65fff17f59cc5e5058b4e2fc48b2",
    name: "Transistor Original Soundtrack"
  };
});
</prism-js>
</source-switcher>

> #### Nazewnictwo
>
> Zazwyczaj do nazwy kontrolera (funkcji) dodaje się suffix`Controller`
> lub `Ctrl`
{: .danger}

## Aplikacja

Wreszcie, żeby angular skompilował nasze widoki i podpiął kontrolery trzeba stworzyć moduł aplikacji `app` i wskazać element, który jest korzeniem naszej aplikacji.

<prism-js language="markup" escape><!--
<body ng-app="app">
  ...
</body>
--></prism-js>

<source-switcher>
<prism-js language="coffeescript">
angular.module 'app', []
</prism-js>
<prism-js language="javascript">
angular.module('app', []);
</prism-js>
</source-switcher>

> #### Moduły
>
> `angular.module()` jest funkcją typu getter/setter:
>
> `angular.module(moduleName, dependencies)`
> rejestruje moduł `moduleName`, który wymaga modułów
> wymienionych w liście `dependencies`
>
> `angular.module(moduleName)`
> zwraca wcześniej zarejestrowany moduł `moduleName`
>
> Upewnij się, że pobierasz moduł po jego zarejestrowaniu!
{: .danger}


> #### Zadanie dla Ciebie:
>
> * Wypróbuj `AlbumController` modyfikując `index.html` tak, aby wyświetlić
>   kafelek albumu
> * Zaimplementuj `TrackController` który będzie odpowiedzialny
>   za wyświetlenie pojedynczego wiersza tabeli utworów
> * Powiąż kontroler z widokiem i wyświetl model w tabeli
> * Popraw błędy typu `GET http://localhost:8888/%7B%7B%20album.image%20%7D%7D 404 (Not Found)`
> * Pytania?
{: .warning}

> #### Struktura
> 
> Spróbuj umieścić `AlbumController` oraz `TrackController` odpowiednio w plikach `src/routes/album/album.js` i `src/routes/album/track.js`.
> Ułatwi Ci to potem kolejne zmiany w aplikacji.
{: .structure}

> #### ng-style
>
> `ng-style` to dyrektywa, która pozwala powiązać model ze stylami
> danego elementu.
>
> <source-switcher>
> <prism-js language="coffeescript">
> .controller 'Controller', ($scope) ->
>   $scope.textColor = 'red'
> </prism-js>
> <prism-js language="javascript">
> .controller('Controller', function($scope) {
>   $scope.textColor = 'red';
> })
> </prism-js>
> </source-switcher>
>
> <prism-js language="markup" escape>{% raw %}
> <span ng-style="{color: textColor}">I'm {{ textColor }}!</span>
> {% endraw %}</prism-js>
{: .info}

## ng-model <small>two-way binding</small>

Aby obsłużyć dane pochodzące od użytkownika, możemy wykorzystać two-way bindings przez dyrektywę `ng-model`.

<prism-js language="markup" escape>
<div ng-controller="GreetingController">
  <input type="text" ng-model="name">
  <p>Hello {{ name }}!</p>
</div>
</prism-js>

<source-switcher>
<prism-js language="coffeescript">
angular.module('app')
.controller 'GreetingController', ($scope) ->
  $scope.name = 'Stefan'  # wartość początkowa
</prism-js>
<prism-js language="javascript">
angular.module('app')
.controller('GreetingController', function($scope) {
  $scope.name = 'Stefan'  // wartość początkowa
});
</prism-js>
</source-switcher>

> #### ng-click
>
> `ng-click` to dyrektywa która pozwala nam powiązać kliknięcie
> przez użytkownika z wywołaniem funkcji lub wyrażenia.
>
> <source-switcher>
> <prism-js language="coffeescript">
> .controller 'Controller', ($scope) ->
>   $scope.x = 0
>   $scope.y = 'a'
>   $scope.clickMe = ->
>     alert 'Hi!'
> </prism-js>
> <prism-js language="javascript">
> .controller('Controller', function($scope) {
>   $scope.x = 0;
>   $scope.y = 'a';
>   $scope.clickMe = function() {
>     alert('Hi!');
>   };
> })
> </prism-js>
> </source-switcher>
>
> <prism-js language="markup" escape>
> <button ng-click="x = 1; y = 'b'">Click me to set variables!</button>
> <button ng-click="clickMe()">Click me to call function!</button>
> </prism-js>
{: .info}

> #### ng-class
>
> `ng-class` to dyrektywa która pozwala nam powiązać klasy elementu
> z modelem. Przyjmuje mapę nazw klas i wyrażeń, które muszą być spełnione,
> żeby dana klasa była dodana do elementu.
>
> <prism-js language="markup" escape>
> <div ng-class="{hidden: isHidden}"></div>
> </prism-js>
{: .info}

> #### Zadanie dla Ciebie:
>
> * Zaimplementuj `NavbarController`, odpowiedzialny za pasek nawigacyjny
>   i pole wyszukiwania
> * Przetestuj działanie two-way data binding na polu wyszukiwarki
>   wyświetlając obok logo strony wyszukiwaną frazę
> * Dodaj czyszczenie pola wyszukiwania za pomocą przycisku
> * Usuń klasę minimized z elementu `<nav>` kiedy pole wyszukiwania jest puste
> * Pytania?
{: .warning}

> #### Struktura
>
> Kontroller `NavbarController` postaraj się umieścić w apliku `src/navbar/navbar.js`
{: .structure}

## Przykładowe dane

Do tej pory nasza aplikacja za wiele nie robiła, a nasz model był zwykłym pojedynczym obiektem.
Wypadałoby zacząć implementację bardziej realistycznej aplikacji z większą ilością danych.

Przygotowaliśmy dla Ciebie plik [search.json]({{ site.baseurl }}assets/search.json), który zawiera pobrane ze [Spotify](https://developer.spotify.com/web-api/search-item/) wyniki wyszukiwania słowa "transistor", które posłużą nam
jako wypełniacz do, jeszcze aktualnie, sztywnego szablonu :)

Aby dołączyć dane z pliku `search.json` do naszej aplikacji narazie po prostu wkleimy jego zawartość do naszego kontrolera (`MainController`):

Tak jest to mało _pro_ rozwiązanie ale zaimplementowanie tego lepiej wymagałoby znajomości usług, które poznamy później.

> #### Zadanie dla Ciebie:
>
> * Stwórz `MainController` i dodaj go do body
> * Pobierz plik [search.json]({{ site.baseurl }}assets/search.json)
> * Wklej jego zawartość do `MainController` tak aby był dostępny w `$scope`,
>   np. pod zmienną `searchResults`
{: .warning}

> #### Struktura
> 
> Na tą chwilę kontroller `MainController` możesz umieścić w pliku `src/app/app.js`. 
{: .structure}

## ng-repeat

Aby wyświetlić listę elementów ze zmiennej, np. `searchResults`, będziemy musieli posłużyć się dyrektywą `ng-repeat`. `ng-repeat` powieli nam element DOM, do którego została przypisana tyle razy ile jest elementów w tablicy.

<prism-js language="markup" escape><!--
<a ng-repeat="artist in searchResults.artists.items"
   class="tile thumb-m"
   title="{{ artist.name }}"
   href="artist/index.html">
</a>
--></prism-js>

Zapis `ng-repeat="artist in searchResults.artists.items"` oznacza tyle co utwórz zmienną o nazwie `artist`, która pobiera dane ze zmiennej
`searchResults.artists.items`.
Dyrektywa `ng-repeat` dosłownie przejdzie po każdym elemencie tablicy
`searchResults.artists.items` i na chwilę wpisze go do zmiennej `artist` przy okazji kopiując element DOM do którego została przypisana.
Zmienna `artist` zostanie udostępniona i będzie można jej użyć do wypełnienia szablonu danymi.

> #### Zadanie dla Ciebie:
>
> * Zaimplementuj `ng-repeat` z użyciem danych z `search.json`
> * Wypełnij szablon danymi o znalezionych artystach, albumach i utworach
>   (`{% raw %}{{}}{% endraw %}`)
> * O ile występują jakieś błędy w konsoli spróbuj je naprawić
> * Pytania?
{: .warning}

## Efekt

![MVC Efekt]({{ site.baseurl }}angular/img/mvc_result.png)
