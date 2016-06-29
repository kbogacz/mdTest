---
layout: post
title: Dyrektywy
order: 4
---

W uproszczonej wersji dyrektywy to znaczniki w DOM (atrybut, nazwa elementu, klasa CSS lub komentarz)
które pozwalają **kompilatorowi AngularJS** rozbudowywać wybrany element DOM o dodatkowe zachowania lub nawet
całkowicie go przekształcać.

AngularJS przychodzi w pakiecie z takimi dyrektywami jak `ngHref`, `ngSrc` lub `ngModel`. Ale co tak naprawde oznacza że
Angular **kompiluje** dany element? Pod maską oznacza to jedynie tyle co dowiązanie do wybranego elementu `event listeners`
tak aby stał się interaktywny.

## Dopasowywanie dyrektyw

Zanim zaczniemy pisać nowe dyrektywy warto wspomnieć w jaki sposób Angular określa, którą dyrektywę gdzie zaaplikować.

Posłużymy się przykładem z `<input>` i `ngModel`:

<prism-js language="markup" escape>
<input ng-model="foo">
<input data-ng:model="foo">
</prism-js>

Oba przypadki zadziałają i są poprawne ze względu na to że kompilator będzie się starał **znormalizować**
wszystkie atrybuty, nazwy klas etc do formy `camelCase` tak aby w tym przypadku otrzymać: `ngModel`.

Sam algorytm normalizacji jest dość prosty:

* Pozbądź się `x-` i `data-` z początku elementu / atrybutu
* Zamień zapis `:`, `-`, lub `_` na camelCase.
* Jeżeli nazwa dyrektywy pasuje - zaaplikuj ją

> Warto używać konsekwentnie jednej notacji.
> Jeżeli pojawiaja się problemy z walidacją HTML warto zastosować prefiks 
> `data-`.
{: .info}

## Własne dyrektywy

Jak wszystko w AngularJS należy nową dyrektywe zarejestrować za pomocą metody `directive` w naszym module.
Sama metoda `directive` rejestruje nazwę dyrektywy w **znormalizowanej** formie `camelCase` oraz funkcję
konstruktora (factory function) która powinna zwracać obiekt.

Załóżmy że mamy jakiś często powtarzający sie kod HTML który jest używany w wielu miejscach na stronie a
każda zmiana wymaga przejścia przez cały projekt i wyszukania jego wystąpień.

<source-switcher>{% raw %}
<prism-js language="coffeescript">
...
.controller 'DummyCtrl', ($scope) ->
  $scope.customer =
    name: 'ala'
    surname: 'kot'
    address: 'Częstochowa'

.directive 'myCustomer', () ->
  template: 'Cześć {{customer.name}}, twój adres: {{customer.address}}'
</prism-js>
<prism-js language="javascript">
...
.controller('DummyCtrl', function($scope) {
  $scope.customer = {
    name: 'ala',
    surname: 'kot',
    address: 'Częstochowa'
  };
})
.directive('myCustomer', function() {
  return {
    template: 'Cześć {{customer.name}}, twój adres: {{customer.address}}'
  };
});
</prism-js>
{% endraw %}</source-switcher>

A wywołać to możemy tak:

<prism-js language="markup" escape>
<div ng-controller="DummyCtrl">
  <div my-customer></div>
</div>
</prism-js>

Sam atrybut `template` sprawdza się przy bardzo małych i prostych szablonach jeżeli masz coś bardziej złożonego
to warto zapisać całość w oddzielnym pliku i użyć `templateUrl`.

<source-switcher>
<prism-js language="coffeescript">
...
.directive 'myCustomer', () ->
  templateUrl: 'my-customer.html'
</prism-js>
<prism-js language="javascript">
...
.directive('myCustomer', function() {
  return {
    templateUrl: 'my-customer.html'
  };
});
</prism-js>
</source-switcher>


Wszystko fajnie pięknie ale co jakbym chciał użyć naszej dyrektywy jako `<my-customer>`?
Tu z pomocą przychodzi nam opcja `restrict` która może dopasować dyrektywę po:

* `A` - nazwie atrybutu
* `E` - nazwie elementu
* `C` - nazwie klasy
* `M` - nazwie komentarza


<source-switcher>
<prism-js language="coffeescript">
...
.directive 'myCustomer', () ->
  restrict: 'E'
  templateUrl: 'my-customer.html'
</prism-js>
<prism-js language="javascript">
...
.directive('myCustomer', function() {
  return {
    restrict: 'E',
    templateUrl: 'my-customer.html'
  };
});
</prism-js>
</source-switcher>

> Wartość `restrict` może być kombinacją poszczególnych wartości, 
> czyli `AEC` też będzie działać
{: .info}

## Zakres zmiennych - scope

Aktualnie nasza dyrektywa dziedziczy / współdzieli zmienne z kontrolerem, co nie zawsze jest pożądanym zachowaniem. Aby kontrolować to w jaki sposób i które zmienne są przekazywane do dyrektywy możemy się posłużyć opcją `scope`. Przyjmuje ona 2 typy wartości:

- `true|false` - tworzy, lub nie, nowy scope dla dyrektywy dziedziczący wartości z miejsca, w którym dyrektywa została użyta
- `Object` - tworzy izolowany scope, który nie dziedziczy wartości, przekazany obiekt jest mapą pól i powiązanych nazw atrybutów, z których pobierane są wartości.

Tworząc izolowany scope zwiększamy możliwość użycia dyrektywy w wielu miejscach, ponieważ nie jest ona zależna od kontekstu w którym występuje.

<source-switcher>
<prism-js language="coffeescript">
...
.directive 'myCustomer', () ->
  restrict: 'E'
  scope:
    customerInfo: '=info'
  templateUrl: 'my-customer.html'
</prism-js>
<prism-js language="javascript">
...
.directive('myCustomer', function() {
  return {
    restrict: 'E',
    scope: {
      customerInfo: '=info'
    },
    templateUrl: 'my-customer.html'
  };
});
</prism-js>
</source-switcher>

Całość przekłada się na HTML'a tak:

<prism-js language="markup" escape>
<div ng-controller="DummyCtrl">
  <my-customer info="customer"></my-customer>
  <hr>
  <my-customer info="secondCustomer"></my-customer>
</div>
</prism-js>

Tu na sekunde powiedziałbym stop i zerknął dokładniej na definicje `scope`.
Możemy przekazywać zmienne do `scope` na kilka sposobów a definijujemy je przez prefiks:

* `@` - powiązanie tekstowe (wartość zostanie skompilowana a jej wynik przekazany do zmiennej)
* `&` - powiązanie w jedną stronę (getter)
* `=` - powiązanie w dwie strony (getter, setter)

Dodatkowo warto zauważyć że możemy mapować nazwy zmiennych czyli:

* `customerInfo: '=info'` - atrybut `info` zostanie przekazany do szablonu jako `customerInfo`
* `customerInfo: '='` - atrybut `customer-info` zostanie przekazany do szablonu jako `customerInfo`


> Warto przedyskutować z mentorem róznicę pomiędzy `ng-show/ng-hide` a `ng-if/ng-switch`
{: .info}

## Manipulowanie DOM'em

Aktualnie nasze dyrektywy służyły nam jako wrapper na szablon czas nadać im trochę życia.
Możemy się posłużyć opcją `link` która pozwoli nam podlinkować dodatkową fukcjonalność do
wybranego elementu.


> Jeżeli nasza dyrektywa nie posiada własności `compile`, wtedy zostanie użyta własność `link`. `link` jest aktywowany po sklonowaniu szablonu, najczęściej umieszczamy tutaj część logiczną.
{: .info}

Wartością opcji `link` powinna być funkcja która przyjmuje trzy argumenty:

* `scope` - instancja scope powiązana z aktualnym elementem i jego atrybutami
* `element` - element DOM'a do którego dyrektywa została przypisana - `angular.element`
* `attrs` - wszystkie atrybuty danego elementu

<source-switcher>
<prism-js language="coffeescript">
...
.directive 'dummyTime', ($interval) ->
  restrict: 'E'
  templateUrl: 'time.html'
  link: (scope, element, attrs) ->
    timeoutId = $interval () ->
      element.text new Date().toString()
    , 1000

    element.on '$destroy', () ->
      $interval.cancel timeoutId
</prism-js>
<prism-js language="javascript">
...
.directive('dummyTime', function($interval) {
  return {
    restrict: 'E',
    templateUrl: 'time.html',
    link: function(scope, element, attrs) {
      var timeoutId = $interval(function() {
        element.text(new Date().toString());
      }, 1000);
      
      element.on('$destroy', function() {
        $interval.cancel(timeoutId);
      });
    }
  };
});
</prism-js>
</source-switcher>

> Dyrektywy powinny sprzątać po sobie, wiec dobrą praktyką jest używanie
> `element.on('$destroy', ...)` lub `scope.$on('$destroy', ...)`.
{: .info}

O dyrektywach pisać można całe książki ale my na tym poprzestaniemy.

> #### Zadanie dla Ciebie:
> 
> * Napisz dyrektywę `playButton` - 
>   `<play-button src="http://..."></play-button>`, która wyrenderuje 
>   przycisk do odtworzenia próbki utworu za pomocą `<audio>`
> * Podmień statyczne przyciski "play" na `<play-button>`
> * Dodaj zapewnienie, że tylko jeden `<play-button>` odtwarza muzykę
>   jednocześnie
> * Pytania?
{: .warning}

> #### Struktura
> 
> Dyrektywę umieść w pliku `src/directives/play-button/play-button.js`. 
{: .structure}

> #### $sceDelegateProvider
> 
> Angular nie pozwoli ustawić atrybutu src, jeśli url nie będzie spełniał 
> same-origin-policy. Aby móc odtworzyć muzykę ze Spotify, trzeba dodać 
> `https://p.scdn.co/mp3-preview/**` do whitelisty Angulara. 
> Służy do tego getter/setter:
> 
> `$sceDelegateProvider.resourceUrlWhitelist([whitelist])`
> 
> #### Przykład
> 
> <source-switcher>
> <prism-js language="coffeescript">
> angular
> .module 'app', []
> .config ($sceDelegateProvider) ->
>   whitelist = $sceDelegateProvider.resourceUrlWhitelist()
>   whitelist.push('https://p.scdn.co/mp3-preview/**')
>   $sceDelegateProvider.resourceUrlWhitelist(whitelist)
> </prism-js>
> <prism-js language="javascript">
> angular
> .module('app', [])
> .config(function ($sceDelegateProvider) {
>   var whitelist = $sceDelegateProvider.resourceUrlWhitelist();
>   whitelist.push('https://p.scdn.co/mp3-preview/**');
>   $sceDelegateProvider.resourceUrlWhitelist(whitelist);
> });
> </prism-js>
> </source-switcher>
{: .danger}

