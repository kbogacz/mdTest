---
layout: post
title: AngularJS
order: 3
---

W poprzednich rozdziałach podmieniliśmy dyrektywę `playButton` na Custom Element `play-button` i, mimo że używamy Angular'owych binding'ów do przekazywania attrybutu `src`, wszystko działa jak powinno.

Wynika to z faktu, że AngularJS traktuje nasze custom elementy tak jak każdy inny element DOM. Dopóki nasz komponent prawidłowo reaguje na zmiany atrybutów wszystko będzie w porządku.

Istnieją jednak sytuacje kiedy trzeba pomóc Angularowi zrozumieć jak funkcjonuje komponent. Wcześniej już wspominaliśmy o dyrektywie `ng-model`, teraz wyjaśnimy co tak na prawdę dzieje się pod spodem, aby zapewnić Angularowy two-way binding.

## Własny `<input>`

Spójrzmy na nasze pole wyszukiwania, jest to idealny kandydat na stworzenie komponentu `<clearable-input>`, który poza samym polem tekstowym, będzie także posiadał przycisk do wyczyszczenia zawartości.

<prism-js language="markup" escape>{% raw %}<!--
<dom-module id="clearable-input">
  <template>
    <style>
      @import url('/bower/font-awesome/css/font-awesome.css');
      ...
    </style>

    <input id="input" 
           type="text" 
           placeholder="{{ placeholder }}">
    <button on-click="clear"
            class="{{ {visible: !!value} | tokenList }}">
      <i class="fa fa-times"></i>
    </button>

  </template>
  <script src="clearable-input.js"></script>
</dom-module>
-->{% endraw %}</prism-js>

> #### Ciekawostka
> 
> Polymer 1.0 ze względów na problemy z implementacją Shadow DOM wstecz oraz z wydajnością
> zastępuje Shadow DOM nowym pojęciem:
> [Shady DOM](https://www.polymer-project.org/1.0/articles/shadydom.html).
> 
{: .info}

> #### Zadanie dla Ciebie:
> 
> * Zaimplementuj komponent `<clearable-input>` i dodaj go zamiast pola
>   wyszukiwania
> * Dodaj emitowanie zdarzenia `input`, kiedy pole jest czyszczone
> * Czy `ng-model` działa?
> * Pytania?
{: .warning}

## ng-model

Jak widać `ng-model` przestał współpracować.

AngularJS dostarcza wiele dyrektyw niezbędnych do pracy z DOM'em, wśród nich jest zestaw dyrektyw `input`, po jednej dla każdego typu. Reagują one na zdarzenia emitowane przez element `<input>` i informują `ng-model` o zmianach wartości.

Angular nie wie jakie zdarzenia emituje nasz `<clearable-input>` i w jakim
polu przechowuje wpisaną wartość. Musimy dodać dyrektywę, która opiszę Angularowi sposób interakcji z komponentem.

Omówiliśmy już opcje dyrektyw `restrict`, `template`, `scope` oraz `link`, teraz czas omówić `controller` i `require`. 

### controller

Za pomocą `controller` możemy przekazać kontroler (lub jego nazwę), który będzie odpowiedzialny za zachowanie dyrektywy. Działa dokładnie tak samo jak parametr `controller` przy definiowaniu ścieżek. Sam kontroler otrzymuje jednak dodatkowe usługi:

* `$element` - element DOM, na którym operuje dyrektywa
* `$attrs` - attrybuty powyższego elementu

<source-switcher>
<prism-js language="coffeescript">
.directive 'greeting', ->
  restrict: 'A'
  controller: ($scope, $element, $attrs) ->
    @greet = (name) ->
      console.log "#{$attrs.greeting} #{name}"
    return
</prism-js>
<prism-js language="javascript">
.directive('greeting', function() {
  return {
    restrict: 'A'
    controller: function($scope, $element, $attrs) {
      this.greet = function(name) {
        console.log($attrs.greeting + ' ' + name);
      };
    }
  };
})
</prism-js>
</source-switcher>

<prism-js language="markup" escape>
<button greeting="Hello"></button>
</prism-js>

### require

Za pomocą `require` możemy pobrać kontroler innej dyrektywy:

* `'myDir'` - znajdź kontroler dyrektywy `myDir` w tym samym elemencie
* `'^myDir'` - znajdź kontroler w tym elemencie lub jego rodzicach
* `'^^myDir'` - znajdź kontroler w rodzicach aktulnego elementu

Dodatkowo można prefiksować wszystkie z tych opcji `?`, wtedy kontroler nie będzie wymagany - Angular nie rzuci błędem jeśli nie znajdzie wymienionego kontrolera.

Znaleziony kontroler zostanie przekazany naszej dyrektywie jako 4ty argument funkcji `link`. Do `require` można przekazać też listę takich specyfikacji, wtedy otrzymamy listę kontrolerów.

<source-switcher>
<prism-js language="coffeescript">
.directive 'greetOnClick', ->
  restrict: 'A'
  require: 'greeting'
  link: ($scope, $element, $attrs, greetingController) ->
    $element.on 'click', ->
      greetingController.greet $element.text()
</prism-js>
<prism-js language="javascript">
.directive('greetOnClick', function() {
  restrict: 'A',
  require: 'greeting',
  link: function($scope, $element, $attrs, greetingController) {
    $element.on('click', function() {
      greetingController.greet($element.text());
    });
  }
})
</prism-js>
</source-switcher>

<prism-js language="markup" escape><!--
<button greeting="Hello" greet-on-click>require</button>
--></prism-js>

### NgModelController

Dyrektywa `ng-model` definiuje swój kontroler `NgModelController`, a więc możemy go pobrać za pomocą `require`. Jest to niezbędne jeśli chcemy implementować własne elementy otrzymujące dane od użytkownika.

Schemat działania `NgModelController` (uproszczony):

* Kiedy użytkownik wpisze coś do pola `<input>`
  1. dyrektywa `input` przechwytuje event i wywołuje 
     `modelCtrl.$setViewValue(value)`
  2. `value` jest przetwarzana przez `modelCtrl.$parsers` i 
     `modelCtrl.$validators`, a efekt jest zapisywany jako 
     `modelCtrl.$modelValue`
  3. uruchamiane są wszystkie listenery z `modelCtrl.$viewChangeListeners`, np.
     wyrażenia podane w `ng-change="..."`

* Kiedy zmienia się model
  1. model przetwarzany jest przez `modelCtrl.$formatters`, efekt jest 
     zapisywany jako `modelCtrl.$viewValue`
  2. wywoływana jest metoda `modelCtrl.$render` zdefiniowana przez dyrektywę
     `input`

Tak więc, kiedy dodajemy własny rodzaj input'a, potrzebujemy:

* Przechwycić zdarzenia, które wskazują na zmianę wartości i wywołać 
  `modelCtrl.$setViewValue(...)`
* Zdefiniować `modelCtrl.$render()`, tak aby `NgModelController` 
  wiedział jak wyświetlić reprezentację modelu w naszym inpucie

> #### Zadanie dla Ciebie:
> 
> * Dodaj dyrektywę `clearableInput` wykorzystującą `ngModel`,
>   aby przywrócić two-way binding
> * Pytania?
{: .warning}

