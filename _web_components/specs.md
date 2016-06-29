---
layout: post
title: Specyfikacje
order: 1
---

Wspomnieliśmy już trochę czym są Web Components, czas przejść do szczegółów i spróbować wykorzystać nowe możliwości przeglądarek. W części poświęconej AngularJS stworzyliśmy dyrektywę wprowadzającą element 
`<play-button src="...">`, teraz, krok po kroku, przerobimy tę dyrektywę na komponent.

## Templates

Szablony są obojętne dla dokumentu, oznacza to, że nie zostaną pobrane zasoby z nim związane, aż do momentu użycia. Pozwalają również na zredukowanie ilości przesyłanego do klienta HTMLa, ponieważ dopiero po jego stronie zamienimy szablon na wiele elementów.

<prism-js language="markup" escape>
<template id="cat">
  <p><img src="images/CAT_NAME.png"> CAT_NAME</p>
</template>

<div id="catsContainer"></div>
</prism-js>

<source-switcher>
<prism-js language="coffeescript">
cats = ['whiskers', 'spike', 'bacon']
template = document.getElementById 'cat'
container = document.getElementById 'catsContainer'

for catName in cats
  fragment = document.importNode template.content, true
  cat = fragment.querySelector 'p'
  cat.innerHTML = cat.innerHTML.replace /CAT_NAME/g, catName
  container.appendChild fragment
</prism-js>
<prism-js language="javascript">
var cats = ['whiskers', 'spike', 'bacon'];
var template = document.getElementById('cat');
var container = document.getElementById('catsContainer')

cats.forEach(function(catName) {
  var fragment = document.importNode(template.content, true);
  var cat = fragment.querySelector('p');
  cat.innerHTML = cat.innerHTML.replace(/CAT_NAME/g, catName);
  container.appendChild(fragment);
});
</prism-js>
</source-switcher>

Czego wynikiem byłby dokument z trzema kotami i co najważniejsze `images/CAT_NAME.png` nie zostanie nigdy pobrany.

<prism-js language="markup" escape><!--
<div id="catsContainer">
  <p><img src="images/whiskers.png"> whiskers</p>
  <p><img src="images/spike.png"> spike</p>
  <p><img src="images/bacon.png"> bacon</p>
</div>
--></prism-js>


> #### Zadanie dla Ciebie: 
> 
> * W `index.html` dodaj `<template>` i umieść w nim 
>   szablon dyrektywy `playButton`
> * Zmodyfikuj dyrektywę `playButton` tak, żeby korzystała 
>   z dodanego `<template>`
> * Pytania?
{: .warning}


## HTML Imports

HTML Imports pozwalają na zamknięcie plików potrzebnych do załadowania komponentu bądź bilbioteki wewnątrz jednego pliku, który będzie zawierać ich ścieżki. Poprawia to czytelność oraz pozwala wygodnie zgrupować powiązane ze sobą linki. 

Należy pamiętać, że każda deklaracja importu zostanie pobrana do oddzielnego dokumentu w celu uniknięcia konfliktów z głównym dokumentem.

Przy wykonywaniu importów możemy również użyć atrybutu znanego z taga `<script>`, a mianowicie async, który zachowa się analogicznie jak podczas użycia w dołączaniu skryptów.

Przykładowy import, który możemy użyć w pliku index.html w celu zagregowania struktury.

<prism-js language="markup" escape><!--
<link rel="import" href="./components/component-time-ago.html" async>
--></prism-js>


Natomiast wewnątrz pliku `components/component-time-ago.html` umieścić:

<prism-js language="markup" escape><!--
<link rel="import" href="./../../bower_components/polymer/polymer.html">
<link rel="import" href="./../base.html">
<link rel="import" href="./time-ago/index.html">
--></prism-js>

Takie rozwiązanie pozwoli nam mieć pewność, że zanim stworzymy nasz komponent którego treść znajduje się wewnątrz `./time-ago/index.html`, będziemy mieć załadowaną bibliotekę Polymera oraz przykładowy komponent `base.html`, po którym będzie dziedziczyć nasz `time-ago`. 

> Jeżeli w obrębie jednego dokumentu wywołamy wewnątrz kilku importów 
> ten sam link, zostanie on pobrany tylko za pierwszym razem. 
> W ten sposób zapobiegniemy dublowaniu dołączania źródeł.
{: .info}

> #### Zadanie dla Ciebie:
> 
> * Przenieś stworzony `<template>`, z którego korzysta dyrektywa `playButton`,
>   do oddzielnego pliku `webcomponents/play-button/play-button.html`
> * Przenieś style wykorzystywane przez `playButton` do 
>   `webcomponents/play-button/play-button.css` i załącz style wewnątrz 
>   `webcomponents/play-button/play-button.html`
> * Przywróć działanie dyrektywy `playButton`
> * Pytania?
{: .warning}

> #### HTML imports a `document.querySelector()`
> 
> Element `<link>` nie dodaje zawartości importowanego dokumentu bezpośrednio 
> do aktualnego. Tworzy zagnieżdżony dokument, którego zawartość jest 
> niewidoczna dla funkcji przeszukujących DOM, takich jak 
> `document.querySelector()`.
> 
> Aby operować na zaimportowanym dokumencie należy wykorzystać atrybut 
> `HTMLLinkElement.import`.
> 
> <prism-js language="markup" escape><!--
> <html>
>   <head>
>     <title>Human Being</title>
>     <link rel="import" href="/imports/heart.html">
>   </head>
>   <body>
>     <p>What is a body without a heart?</p>
>   </body>
> </html>
> --></prism-js>
> 
> <source-switcher>
> <prism-js language="coffeescript">
> link = document.querySelector 'link[rel=import]'
> heart = link.import
> # Access DOM of the document in /imports/heart.html
> pulse = heart.querySelector 'div.pulse'
> </prism-js>
> <prism-js language="javascript">
> var link = document.querySelector('link[rel=import]');
> var heart = link.import;
> // Access DOM of the document in /imports/heart.html
> var pulse = heart.querySelector('div.pulse');
> </prism-js>
> </source-switcher>
{: .info}

## Custom Elements

Tworzenie custom elementu odbywa się przez wywołanie funkcji document.registerElement, posiada ona dwa argumenty:

1. String zawierający tag, który chcemy stworzyć (musi zawierać '-' w nazwie w celu kompatybilności)
2. Prototyp elementu HTML po którym nasz element będzie dziedziczył. Nie jest obowiązkowy, w przypadku jeśli go nie podamy, zostanie prototypem HTMLElement.

Tworząc prototyp możemy zadeklarować funkcje których działanie jest uzależnione od lifecycle'u elementu, możemy użyć następujących callbacków:

* `createdCallback` - odpalany w momencie stworzenia instancji komponentu
* `attachedCallback` - odpalany w momencie dołączenia komponentu do DOMu
* `detachedCallback` - odpalany w momencie odłączenia komponentu od DOMu
* `attributeChangedCallback(attrName, oldVal, newVal)` - odpalany gdy zmieni się wartość atrybutu

<source-switcher>
<prism-js language="coffeescript">
class SayHello extends HTMLElement
  attachedCallback: -> 
    console.log "hello #{this.innerHTML}!"

document.registerElement 'say-hello', SayHello
</prism-js>
<prism-js language="javascript">
function SayHello() {}
SayHello.prototype = Object.create(HTMLElement.prototype);
SayHello.prototype.attachedCallback = function() {
  console.log('Hello ' + this.innerHTML + '!');
};

document.registerElement('say-hello', SayHello);
</prism-js>
</source-switcher>


<prism-js language="markup" escape>
<say-hello>Web Components</say-hello>
</prism-js>

<source-switcher>
<prism-js language="coffeescript">
hello = document.createElement 'say-hello'
hello.innerHTML = 'Web Components'
document.body.appendChild hello
</prism-js>
<prism-js language="javascript">
var hello = document.createElement('say-hello');
hello.innerHTML = 'Web Components';
document.body.appendChild(hello);
</prism-js>
</source-switcher>

>#### Zadanie dla Ciebie
>
> * Zaimplementuj `<play-button>` za pomocą Custom Elements
> * Usuń dyrektywę `playButton`
> * Pytania?
{: .warning}

> #### `document.currentScript`
> 
> W ramach HTML Imports został dodany property `document.currentScript`, 
> który przechowuje referencję do elementu `<script>`, który spowodował 
> uruchomienie danego kodu JavaScript. 
> 
> Dla przeglądarek, które nie wspierają natywnie `document.currentScript`, 
> webcomponents.js dostarcza polyfill `document._currentScript`.
> 
> Dzięki temu, w łatwy sposób możemy zlokalizować szablon naszego 
> komponentu:
> 
> <prism-js language="markup" escape>
>   <!-- hello-world.html -->
>   <template>
>     Hello <strong>World!</strong>
>   </template>
>   
>   <script src="hello-world.js"></script>
> </prism-js>
> 
> <source-switcher>
> <prism-js language="coffeescript">
>   # hello-world.coffee
>   
>   ownerDocument = document._currentScript.ownerDocument
>   template = ownerDocument.querySelector 'template'
>   
>   class HelloWorld extends HTMLElement
>     createdCallback: ->
>       clone = document.importNode template.content, true
>       this.appendChild clone
>       
>   document.registerElement 'hello-world', HelloWorld
> </prism-js>
> <prism-js language="javascript">
>   // hello-world.js
>   
>   var ownerDocument = document._currentScript.ownerDocument;
>   var template = ownerDocument.querySelector('template');
>   
>   function HelloWorld() {}
>   HelloWorld.prototype = Object.create(HTMLElement.prototype);
>   HelloWorld.prototype.createdCallback = function() {
>     clone = document.importNode(template.content, true);
>     this.appendChild(clone);
>   };
>   
>   document.registerElement('hello-world', HelloWorld);
>       
> </prism-js>
> </source-switcher>
> 
{: .info}

## Shadow DOM

Shadow DOM składa się z:

- hosta - czyli elementu na którym wołamy createShadowRoot(), jest to ostatni element dotyczący komponentu dostępny w dokumencie.
- roota - element który zwraca funkcja createShadowRoot(), jest to pierwszy element wewnątrz enkapsulacji
- boundary - "bariera" stworzona pomiędzy hostem a rootem, chroni style oraz skrypty przed wyciekaniem w obie strony
- insertion point - miejsce, które pozwala na przekazanie danych poprzez boundary, zazwyczaj atrybuty bądź treść elementu hosta, które są dostępne jako element `<content>` po stronie roota.

<prism-js language="markup" escape><!--
<template>
  <style>
    p { color: red; }
  </style>
  <p><content></content></p>
</template>

<p id="light">Light DOM</p>
<p id="shadow">Shadow DOM</p>
--></prism-js>

<source-switcher>
<prism-js language="coffeescript">
shadow = document.getElementById 'shadow'
shadowRoot = shadow.createShadowRoot()
shadowRoot.appendChild document.querySelector('template').content
</prism-js>
<prism-js language="javascript">
var shadow = document.getElementById('shadow');
var shadowRoot = shadow.createShadowRoot();
shadowRoot.appendChild(document.querySelector('template').content);
</prism-js>
</source-switcher>

Czego efektem będzie:

> Light DOM
> 
> <p style="color:red">Shadow DOM</p>

Co najważniejsze styl zdefiniowany wewnątrz shadow DOM `p { color: red; }` nie wpływa na wszystkie paragrafy w dokumencie.

> #### Zadanie dla Ciebie:
> 
> * Zmodyfikuj element `play-button`, żeby korzystał z Shadow DOM
> * Przenieś style wykorzystywane przez `play-button` do wewnątrz jego szablonu
>   modyfikując odpowiednio reguły
> * Pytania?
{: .warning}

> #### `HTMLElement.shadowRoot`
> 
> Shadow root stworzony metodą `HTMLElement.createShadowRoot()` jest 
> także dostępny pod atrybutem `HTMLElement.shadowRoot`.
{: .info}

> #### link
> 
> Element `link` jest ignorowany wewnątrz Shadow DOM, więc do załączenia styli
> z zewnętrznego pliku, trzeba użyć `@import`.
> 
> <prism-js language="markup" escape>
> <template>
>   <style>
>     @import url('/webcomponents/play-button/play-button.css');
>   </style>
>   <div class="play-button">
>     ...
>   </div>
> </template>
> </prism-js>
{: .danger}
