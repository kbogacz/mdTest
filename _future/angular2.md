---
layout: post
title: Angular 2.0
order: 1
---

> #### Alpha
> 
> Angular 2 jest we wczesnej fazie alpha, jego design oraz api zmieniają
> się bardzo dynamicznie. Ten artykuł ma przede wszystkim zarysować w jakim 
> kierunku idzie ten projekt i zaprezentować kilka przykładów kodu bazując na 
> stanie z lutego 2015. W szczególności na prezentacji 
> [An Angular2 Todo App: First look at App Development in Angular2](http://youtu.be/uD6Okha_Yj0).
{: .danger}

AngularJS 1.x został zaprojektowany w 2009 roku, od tamtej pory wiele zmieniło się w świecie JavaScript i przeglądarek. 

Jesteśmy coraz bliżej stosowania `ES6` w przeglądarkach, nawet dziś można wykorzystać większość nowych konstrukcji JavaScript dzięki transpilatorom [traceur](https://github.com/google/traceur-compiler) czy [babel](https://babeljs.io/). Dzięki temu nie ma potrzeby implementować własnego systemu modułów, mamy moduły `ES6`. W AngularJS 1.x w wielu miejscach przekazuje się funkcje konstruktora: `.controller`, `.service`, `.provider`, etc. Dzięki `ES6` można po prostu wykorzystać klasy.

Po stronie przeglądarek mamy Web Components, zestaw standardów, który całkowicie zmienia sposób konstruowania aplikacji web'owych. Angular 1.x nie został zaprojektowany z myślą, że będziemy mogli tworzyć własne elementy DOM. Nie brał też pod uwagę Shadow DOM'u i implementował odpowiednik `<content>` na swój sposób, za pomocą transkluzji.

Celem Angular'a 2.0 jest odświeżenie api, aby korzystać z nowych standardów, uproszczenie wielu elementów framework'a, poprawa wydajności. Już dziś, w wersji alpha, AngularJS 2 jest 5x szybszy niż Angular 1.x.

> #### AtScript
> Aby osiągnąć swoje cele zespół odpowiedzialny za Angular postanowił stworzyć 
> własny język nieco wzbogacający `ES6` - `AtScript`. Wprowadza on:
> 
> * Typowanie zmiennych - wzorowane na 
>   [TypeScript](http://www.typescriptlang.org/)
> * Anotacje - np. `@Component`
{: .info}

W AngularJS 1.x mieliśmy `$scope`, kontrolery i dyrektywy, w Angularze 2 mamy komponent - `@Component`:

* Rejestruje nowy element DOM
* Wykorzystuje Shadow DOM do enkapsulacji
* Podpina instancję podanej klasy jako kontekst szablonu

Dzięki Shadow DOM nie potrzebny jest `$scope`, nie ma też wyciekania zmiennych jak w przypadku hierarchi `$scope`, wszystkie wartości muszą być jawnie przekazane przez pola komponentu lub usługi. Nie ma też odpowiednika `ng-controller`, co wymusza budowanie aplikacji z komponentów. Dzięki temu mamy przejrzystą strukturę DOM.

### Hello world - AngularJS 1.x

`index.html`

<prism-js language="markup" escape><!--
<html>
  <head>...</head>
  <body ng-app="helloApp">
    <div ng-include="`hello.html`"></div>
  </body>
</html>
--></prism-js>

`hello.html`

<prism-js language="markup" escape>{% raw %}<!--
<div ng-controller="HelloController">
  <input type="text" ng-model="greeting">

  <div class="greeting">
    {{ greeting }} <span red>world</span>!
  </div>
</div>
-->{% endraw %}</prism-js>

`hello.js`

<prism-js language="javascript">
angular.module('helloApp')
.service('Greeting', function() {
  this.greeting = 'hello';
})
.controller('HelloController', function($scope, Greeting) {
  $scope.greeting = Greeting.greeting;
})
.directive('red', function() {
  return {
    restrict: 'A',
    link: function($scope, $element) {
      $element.css('color', 'red');
    }
  };
});
</prism-js>

### Hello world - AngularJS 2

`index.html`

<prism-js language="markup" escape><!--
<html>
  <head>...</head>
  <body>
    <hello-app></hello-app>
  </body>
</html>
--></prism-js>

`hello.html`

<prism-js language="markup" escape>{% raw %}<!--
<input #input 
       [value]="greeting"
       (keyup)="greeting = input.value">
<div class="greeting">
  {{ greeting }} <span red>world</span>!
</div>
-->{% endraw %}</prism-js>

`hello.js`

<prism-js language="javascript" escape>
import {Component, Decorator, Template, NgElement} from 'angular2/angular2';

class GreetingService {
  greeting:string;
  constructor() {
    this.greeting = 'hello';
  }
}

@Component({
  // rejestruje nasz komponent pod tagiem 'hello-app'
  selector: 'hello-app',
  // komponent wymaga usługi GreetingService, zostanie
  // ona wstrzyknięta do konstruktora HelloCmp
  componentServices: [GreetingService]
})
@Template({
  // szablon komponentu znajduje się w 'hello-app.html'
  url: 'hello.html',
  // szablon używa dekoratora 'red', 
  // który jest zaimplementowany przez klasę RedDecorator
  directives: [RedDecorator]
})
class HelloCmp {
  greeting: string;
  constructor(service: GreetingService) {
    this.greeting = service.greeting;
  }
}

@Decorator({
  selector: '[red]'
})
class RedDecorator {
  // NgElement jest wstrzykiwalnym wrapperem na DOM element
  constructor(el: NgElement) {
    el.domElement.style.color = 'red';
  }
}
</prism-js>

