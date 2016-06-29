---
layout: post
title: Filtry
order: 2
---

AngularJS pozwala nam oraganizować dane tak aby pokazywały się tylko wtedy kiedy spełniają określone kryteria.
Robi to poprzez coś o nazwie Filters (pol. Filtry). Można ich użyć aby modyfikować, ograniczać lub formatować dane pochodzące z naszych modeli.

Filtry mogą być stosowane w szablonach:
<prism-js language="markup" escape>{% raw %}
{{ zmienna | nazwa_filtru:argument:...:argumentN }}
{{ zmienna | uppercase }}
{% endraw %}</prism-js>

bądź bezpośrednio w kodzie przy użyciu usługi `$filter`:

<source-switcher>
<prism-js language="coffeescript">
.controller 'Controller', ($filter) ->
  myFilter = $filter 'nazwa_filtru'
  myFilter zmienna, argument, argumentN

# przykład:
.controller 'Controller', ($scope, $filter) ->
  uppercaseFilter = $filter 'uppercase'
  $scope.text = uppercaseFilter 'Ala ma kota'
</prism-js>
<prism-js language="javascript">
.controller('Controller', function($filter) {
  var myFilter = $filter('nazwa_filtru');
  myFilter(zmienna, argument, argumentN);
})

// przykład:
.controller('Controller', function($scope, $filter) {
  var uppercaseFilter = $filter('uppercase');
  $scope.text = uppercaseFilter('Ala ma kota');
})
</prism-js>
</source-switcher>

> To dobry moment aby przedyskutować z mentorem w jakiej sytuacji 
> używa się poszczególnych rozwiązań.
{: .info}

> #### Wbudowane filtry
> {% raw %}
> `{{ string | lowercase }}` - zamienia tekst na małe litery  
> `{{ string | uppercase }}` - zamienia tekst na duże litery  
> `{{ number | number:fractionSize }}` - formatuje liczbę  
> `{{ number | currency:symbol }}` - formatuje liczbę jako walutę  
> `{{ value | date:format }}` - formatuje datę za pomocą wzorca, 
>   np. `yyyy-MM-> dd`  
> `{{ value | json }}` - serializuje wartość do formatu JSON  
> `{{ list | limitTo:n }}` - zwraca pierwszych `n` elementów listy  
> `{{ list | filter:expression:comparator }}` - filtruje listę zwracając 
>   elementy spełniające podane wyrażenie  
> `{{ list | orderBy:expression:reverse }}` - sortuje tablicę wg podanego 
>   wyrażenia  
> {% endraw %}
{: .info}

> #### Zadanie dla Ciebie:
> 
> * Za pomocą filtra ogranicz liczbę wyświetlanych elementów do `5`
> * Wyświetl wszystkie tytuły utworów drukowanymi literami
> * Pytania?
{: .warning}

## Sortowanie i filtrowanie list

Zamiast filtrować/sortować dane bezpośrednio na poziomie modelu możemy na podstawie danych z formularza przetwarzać je po stronie widoku za pomocą filtrów.
Do tego celu wykorzystajmy pole wyszukiwania oraz filtry `limitTo`, `filter` i `orderBy`.

> #### Zadanie dla Ciebie:
> 
> * Zmniejsz liczbę widocznych rezultatów do pięciu.
> * Zaimplementuj filtrowanie wyświetlanych albumów, artystów i utworów
>   tak, aby widoczne były tylko te, które w nazwie mają wyszukiwaną frazę
> * Dodaj sortowanie wg nazwy albumu, artysty, utworu
> * Pytania?
{: .warning}

## Własne filtry

Budowanie własnych filtrów jest dość proste, rejestrujemy je poprzez metodę modułu `.filter(name, filterFactory)`.
Pierwszy argument metody `filter` to nazwa naszego filtra, drugi to funkcja.
Ta funkcja musi zwracać inną funkcję, która jest tak naprawdę zawartością naszego filtra.
Zwracana funkcja musi przyjmować jak pierwszy parametr `input` czyli dane przekazane do filtra oraz może przyjmować dodatkowe parametry konfiguracyjne.

<source-switcher>
<prism-js language="coffeescript">
angular.module('app')
.filter 'name', ->
  (input, arg1, arg2) ->
    'modified input'
</prism-js>
<prism-js language="javascript">
angular.module('app')
.filter('name', function() {
  return function(input, arg1, arg2) {
    return 'modified input';
  };
});
</prism-js>
</source-switcher>

> #### Zadanie dla Ciebie:
> 
> * Zaimplementuj filtr `duration`, który zamieni czas trwania utworów w ms 
>   na tekst w formacie `m:ss`,  
>   np. `188226 -> '3:08'`
> * Pytania?
{: .warning}

> #### Struktura
> 
> Kod najlepiej jest umieścić w pliku `src/filters/duration.js`. 
{: .structure}

## Efekt

![Filters Efekt]({{ site.baseurl }}angular/img/filters_result.png)

