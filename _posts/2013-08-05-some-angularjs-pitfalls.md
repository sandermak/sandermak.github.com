---
layout: post
title: "Some AngularJS pitfalls" 
category : web 
tags : [javascript, angularjs, web]
excerpt: "In the past few months I've been heavily using AngularJS. It's a marvelous framework for modern web applications. But as with every new toy, there are some rough edges that you have to be aware of. In this post I'll round up some gotcha's I encountered. Assuming you already know your way around Angular, these may be helpful."
---

### The flickering UI

Angular's automatic data-binding feature is awesome. However, it does have a flipside: before Angular is initialized, your page may show unparsed expressions to the user. When the DOM is ready and Angular calculates and replaces the values, this results in an ugly flickering effect:

![Unparsed Angular expression](/pics/angular_flicker.png)

The above situation is a direct result of rendering this example from the [Angular tutorial](http://docs.angularjs.org/tutorial/step_02):

{% highlight html %}
<body ng-controller="PhoneListCtrl">
  <ul>
    <li ng-repeat="phone in phones">
      {{ "{{ phone.name " }}}}
      <p>{{ "{{ phone.snippet " }}}}</p>
    </li>
  </ul>
</body>
{% endhighlight %}

If you have a single page application, this problem only occurs on the first page-load. Fortunately, it's easily prevented: don't use inline ```{{ "{{ " }}}}``` expressions. Instead, use the ng-bind directive:

{% highlight html %}
<body ng-controller="PhoneListCtrl">
  <ul>
    <li ng-repeat="phone in phones">
      <span ng-bind="phone.name"></span>
      <p ng-bind="phone.snippet">Optional: visually pleasing placeholder</p>
    </li>
  </ul>
</body>
{% endhighlight %}

You need a tag to apply this directive, so I added a ```<span>``` around the phone name. What happens is that initially, the value inside the tag is shown (but you can choose to leave it empty.) Then, when Angular initializes and replaces the innards of the bound tag with the result of the expression. Note that you don't need to add the curly braces inside the [ng-bind](http://docs.angularjs.org/api/ng.directive:ngBind) property. Much cleaner! If you need a compound expression, use [ng-bind-template](http://docs.angularjs.org/api/ng.directive:ngBindTemplate) instead. With this variant you do need the curly braces though, in order to differentiate between string literals and expressions.

Another option is to completely hide elements, or even your whole application, until Angular is ready. Angular provides [ng-cloak](http://docs.angularjs.org/api/ng.directive:ngCloak) to this end. It works by injecting a CSS rule very early in the initialization phase into the page. Alternatively you can include this CSS hiding rule in your own stylesheet so it loads early. Angular will the remove the cloak style when it is ready to roll, unveiling your application (or designated elements) at once and fully rendered.

### jQuery and Angular

Angular doesn't depend on jQuery. In fact, the Angular source contains an embedded lightweight alternative: [jqLite](http://docs.angularjs.org/api/angular.element). Still, when Angular detects the presence of a jQuery version in your page, it uses that full jQuery implementation in lieu of jqLite. One direct way in which this manifests itself is with Angular's element abstraction. For example, in a directive you get access to the element that the directive applies to:

{% highlight javascript %}
angular.module('jqdependency', [])
  .directive('failswithoutjquery', function() {
    return {
      restrict : 'A',
      link : function(scope, element, attrs) {
               element.hide(4000)
             }
    }
});
{% endhighlight %}
(see this example in action in [this plunkr](http://plunker.co/edit/aqeDikqd6O2QaqH3eaIq?p=preview))
 
but is this ```element``` a jqLite or jQuery element? It depends. The manual states:
>  All element references in Angular are always wrapped with jQuery or jqLite; they are never raw DOM references.

So it will be a jqLite element only if jQuery was not detected by Angular. The ```hide()``` method only exists for jQuery elements, meaning this example only works when Angular uses jQuery. If you (accidentally) switch the order of AngularJS and jQuery script tags in your page, this code breaks! Shuffling script tags around may not happen very often, but it bit me when I started modularizing our codebase. Especially when you start using a module loader (like [RequireJS](http://requirejs.org)), this can rear it's ugly head. For RequireJS, I solved it by explicitly declaring that Angular does in fact depend on JQuery in the [shim config](http://requirejs.org/docs/api.html#config-shim).

Another takeaway might be that you shouldn't call jQuery specific methods through Angular's element wrapper. Instead you can use ```$(element).hide(4000)``` to make it abundantly clear that you do, in fact, depend on jQuery. In that case, changing the script tag order won't break the directive.

### Minification
Special care should be taken for Angular applications that need to be minified. Otherwise error messages like ```'Unknown provider: aProvider <- a'``` will hound you. Like with so many things, this information is hidden somewhere in the [official documentation](http://docs.angularjs.org/tutorial/step_05). In short, Angular depends on function parameter names to perform dependency injection. The minifier,  blissfully unaware of the special semantics of formal parameter names in Angular, mangles them to the shortest possible identifier. Solution? Use the minify-friendly method of method injection. From this:

{% highlight javascript %}
module.service('myservice', function($http, $q) { 
   // This breaks when minified
});
{% endhighlight %}

to this:

{% highlight javascript %}
module.service('myservice', [ '$http', '$q', function($http, $q) { 
   // Using the array syntax to declare dependencies works with minification!
}]);
{% endhighlight %}

The array syntax is accepted wherever a to-be-injected function is accepted. My advise is to use this syntax from the get-go. It will prevent a lot of pain later on if you decice to minify your JavaScript. There also seems to be a [automatic rewriter](https://github.com/btford/ngmin), although I don't know how well it works.

One last bit of advice: when you need to rewrite your functions using the array syntax, apply it at every location where Angular injects dependencies. This includes directives, but also controllers on directives. A spot that is easily missed (speaking from experience):

{% highlight javascript %}
// the directive itself needs array injection syntax:
module.directive('directive-with-controller', ['myservice', function(myservice) {
    return {
      controller: ['$timeout', function($timeout) {
        //  but this controller needs array injection syntax, too!  
      }],
      link : function(scope, element, attrs, ctrl) {
                
      }
    }
}]);
{% endhighlight %}

Note that the link function doesn't need the array syntax, since it is not actually an injection target. It is a fixed-arity function called directly by Angular. Injections from the directive level can be used inside the link function though.

### Directives are never 'done'
One irritating issue with directives is that you don't get a hook for when the directive is 'done'. When integrating a jQuery plugin inside a directive, such a hook would be very helpful. Let's say you want to display a table with dynamic (ng-repeat) data as a jQuery datatable. You need to call ```$('.mytable').dataTable()``` at some point when all data is actually materialized in an HTML table (and preferably before it is inserted into the DOM), but you can't.

Why is that? Angular's data-binding is powered by a continuous digest-cycle which can lead to updates and re-rendering of directives. As such, there is never really a moment that the framework is 'at-rest' when applying directives. One (admittedly ugly) workaround is to schedule the jQuery dataTable call outside the current digest-cycle by using timeout:

{% highlight javascript %}
angular.module('table',[]).directive('mytable', ['$timeout', function($timeout) {
    return {
      restrict : 'E',
      template: '<table class="mytable">' +
                   '<thead><tr><th>counting</th></tr></thead>' +
                   '<tr ng-repeat="data in datas"><td>{{ data }}</td></tr>' +
                '</table>',
      link : function(scope, element, attrs, ctrl) {
         scope.datas = ["one", "two", "three"]
         // Doesn't work, shows an empty table:
         // $('.mytable', element).dataTable()   
         // But this does:
         $timeout(function() { 
           $('.mytable', element).dataTable(); 
         }, 0)
      }
    }
}]);
{% endhighlight %}
(see this example in action in [this plunkr](http://plnkr.co/edit/ir7U3h1C26NTUpPgyeqG?p=preview))

In our codebase we've even encountered a situation where a doubly-nested ```$timeout``` was necessary. Another crazy work around is to add a ```<script>``` tag in the template which does a callback to an Angular function using ```scope.$apply()```. Suffice to say, it's less than ideal. Due to Angular's nature this is [unlikely](https://github.com/angular/angular.js/issues/734) to change.

All this being said, Angular is still my favorite client-side JS framework. Did you encounter other pitfalls while using Angular? Or did you solve similar problems in a different way? Leave a comment! 
