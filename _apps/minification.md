---
layout: post
title: Minifikacja
order: 5
---

Większość bibliotek w JavaScipt jest serwowana w postaci zminifikowanej co generalnie oznacza, iż kod został skompresowany do jednej linii, a nazwy zmiennych skrócone tak aby plik js miał jak najmniejszy rozmiar. Weźmy jako przykład uproszczony `SearchRouteController`:

<source-switcher>
<prism-js language="coffeescript">
.controller 'SearchRouteController', ($scope, $location, SpotifyApi) ->
  $scope.search = $location.search().q or ''

  SpotifyApi.search($scope.search, ['album', 'artist'])
  .then (response) ->
    $scope.searchResults = response.data
</prism-js>
<prism-js language="javascript">
.controller('SearchRouteController', function($scope, $location, SpotifyApi) {
  $scope.search = $location.search().q || '';

  SpotifyApi.search($scope.search, ['album', 'artist'])
  .then(function(response) {
    $scope.searchResults = response.data;
  });
})
</prism-js>
</source-switcher>

Po minifikacji przez `uglifyjs`, będzie on wyglądał tak:

<prism-js language="javascript">
.controller("SearchRouteController",function(a,b,c){a.search=b.search().q||"",c.search(a.search,["album","artist"]).then(function(b){a.searchResults=b.data})});
</prism-js>

Jak widać na powyższym przykładzie kod został skompresowany do jednej linii a nazwy zmiennych skrócone (`a` - `$scope`, `b` - `$location` itd.).
Problem w tym że AngularJS na podstawie nazw argumentów fukcji określa jakie dokładnie zależnośći mają być zainicjalizowane i przekazane, w momencie kiedy te nazwy będą zminifikowane kod przestanie działać. Aby temu zapobiec można zapisać kontroler w bezpieczny sposób:

<source-switcher>
<prism-js language="coffeescript">
.controller 'SearchRouteController', [
  '$scope', '$location', 'SpotifyApi',
  ($scope, $location, SpotifyApi) ->
    $scope.search = $location.search().q or ''
  
    SpotifyApi.search($scope.search, ['album', 'artist'])
    .then (response) ->
      $scope.searchResults = response.data
]
</prism-js>
<prism-js language="javascript">
.controller('SearchRouteController', [
  '$scope', '$location', 'SpotifyApi',
  function($scope, $location, SpotifyApi) {
    $scope.search = $location.search().q || '';
  
    SpotifyApi.search($scope.search, ['album', 'artist'])
    .then(function(response) {
      $scope.searchResults = response.data;
    });
  }
])
</prism-js>
</source-switcher>

> Nasza konfiguracja gulpa **automatycznie** konwertuje kod do 
> bezpiecznej postaci za pomocą 
> [ngAnnotate](https://github.com/olov/ng-annotate)
{: .info}

> #### ng-strict-di
> 
> Aby zasymulować działanie aplikacji po minifikacji, bez obfuskowania kodu,
> można dodać atrybut `ng-strict-di` do elementu z dyrektywą `ng-app`. Wtedy
> Angular nie będzie brał pod uwagę nazw argumentów funkcji i będzie wymagał
> podania nazw usług bezpośrednio, np. przez notację listową.
{: .info}
