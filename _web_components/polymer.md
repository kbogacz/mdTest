---
layout: post
title: Polymer
order: 2
---

Każdy komponent stworzony przy użyciu Polymera jest zadeklarowany przy użyciu taga `<dom-module></dom-module>`, posiada on co najmniej atrybut `id` zawierający jego nazwę (nazewnictwo obowiązują te same restrykcje co przy tworzeniu zwykłego komponentu). 

Dodatkowo musimy zarejestrować stworzony element. Możemy to zrobić przy pomocy prostego `<script>Polymer({is: 'our-id'})</script>`.

Tak wygląda prosty, przykładowy webcomponent stworzony przy pomocy Polymera:

<prism-js language="markup" escape><!--
<dom-module id="say-hello">
  <template>
    Hello <content></content> !
  </template>

  <script>
    Polymer({
      is: 'say-hello',
      attached: function () {
        console.log('Hello ' + this.textContent + '!');
      }    
    });
  </script>
</dom-module>
--></prism-js>

Jeżeli chcielibyśmy użyć go w kodzie, wystarczy zrobić to tak samo, jak przy zwykłym Web Componencie:

<prism-js language="markup" escape><!--
<say-hello>Web Components</say-hello>
--></prism-js>

W efekcie otrzymamy dokument wyświetlający: `Hello Web Components !`

> Aby wykorzystać treść HTML podaną przy użyciu Web Componentu używamy elementu `<content></content>` (wewnątrz template'a).
{: .info}


Polymer dostarcza nam kilka rzeczy:

1. Zapewnia polyfill Shadow DOM w przeglądarkach, które go nie wspierają
2. Dodaje sugar syntax, ułatwia pisanie komponentów
3. Rozszerza możliwości które mamy w webcomponentach, np:


### Dynamiczne renderowanie szablonów (np. atrybut `repeat` bądź `if`)

<prism-js language="markup" escape><!--
<dom-module id="cat-list">
  <template>
  {% raw %}
    <template is="dom-repeat" items="{{cats}}">
      <p><img src="images/{{item}}.png"> {{item}}</p>
    </template>
  {% endraw %}
  </template>
  <script>
    Polymer({
      is: 'cat-list',
      ready: function () {
        this.cats = ['whiskers', 'spike', 'bacon'];
      },
    });
  </script>
</dom-module>
<cat-list></cat-list>
--></prism-js>


> Zwróć uwagę na zagnieżdżone użycie elementu `template` z atrybutem `is="dom-repeat"`.
{: .info}


### Event mapping + Value Watching

<prism-js language="markup" escape><!--
<dom-module id="two-way">
  <template>
    {% raw %}<input type="text" value="{{_value::input}}">
    <span>Hello {{_value}}</span>
    <button on-click="clear">Clear</button>{% endraw %}
  </template>
  <script>
    Polymer({
      is: 'two-way',
      properties: {
        _value: {
          type: String,
          value: '',
          observer: 'onValueChanged',
        },
      },
      onValueChanged: function (val) {
        console.log('value change: ', val);
      },
      clear: function () {
        this._value = null;
      }
    });
  </script>
</dom-module>
<two-way></two-way>
--></prism-js>

> Wewnątrz opcji prototypu możemy zadeklarować eventy, zwróć uwagę jak przekazywany jest trigger funkcji `clear`.
> Aby zaktualizować wartości naszych properties w rezultacie jakiejś akcji używamy zapisu
`_value::input`. Ten konkretny mówi, że do zmiennej `_value` zostanie przypisana nowa wartość 
w reakcji na event `input`.
{: .info}

> Kiedy chcemy używać i obserwować prywatne property, jak w tym przypadku, dobrą praktyką jest 
poprzedanie ich nazw znakiem podkreślenia (`_value`).
{: .info}


### Więcej lifecycle methods

Polymer dodaje 2 dodatkowe metody do lifecycle methods, oraz używa własnych nazw na poznane wcześniej metody webcomponentowe:

* `created` - aktywowany przy stworzeniy instancji (natywnie: `createdCallback`)
* `ready` - aktywowany gdy element jest w pełni przygotowany, tzn posiada Shadow DOM, podpięte eventListenery itp.
* `attached` - aktywowany gdy dodamy instację do dokumentu (natywnie: `attachedCallback`)
* `detached` - aktywowany gdy usuniemy element z dokumentu (natywnie: `detachedCallback`)
* `attributeChanged` - atrybut elementu zmienił się, w polymerze mamy dostęp do `Changed watchers` (przykład użycia możesz znaleźć nieco wyżej, podczas Event mappingu - funkcja `onValueChanged`) i jest to zalecana, jeśli możliwa do użycia droga. (natywnie: `attributeChangedCallback`)


### Wygodny node finding

Do wszystkich elementów HTML zawartych wewnątrz szablonu możemy odwołać się przy pomocy `this.$`, gdzie po kropce możemy podać id elementu który chcemy znaleźć. Możemy również sprawdzać wartości elementu.


<prism-js language="markup" escape><!--
<dom-module id="node-finding">
  <template>
    <div class="big green" id="wrapper">
      <span>Acodemy is awesome!</span>
    </div>
  </template>
  <script>
  Polymer({
    is: 'node-finding',
    ready: function() {
      console.log(this.$.wrapper.className);
      console.log(this.$.wrapper.querySelector('span').innerHTML);
    }
  });
  </script>
</dom-module>
<node-finding></node-finding>
--></prism-js>

> Dostęp przy pomocy `this.$` otrzymamy tylko do elementów które istnieją podczas inicjalizacji komponentu.
{: .info}

Są to przykładowe własności, które otrzymujemy wraz z Polymerem, kompletną dokumentację API można [znaleźć pod tym linkiem](https://www.polymer-project.org/1.0/docs/start/quick-tour.html).

> #### Zadanie dla Ciebie
>
> * Zaimplementuj komponent `play-button` za pomocą `dom-module`
> * Pytania?
{: .warning}
