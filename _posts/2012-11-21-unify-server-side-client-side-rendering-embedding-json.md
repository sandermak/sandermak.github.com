---
layout: post
title: "Unify client-side and server-side rendering by embedding JSON" 
category : web 
tags : [web, javascript, java, json]
excerpt: "Not many applications I work on these days are solely using the traditional server-side rendering model. Neither do they employ 100% client-side rendering and templating. Usually it's a mix in which the 'old' world meets the new world, giving rise to some interesting design decisions. In this article I want to explore a solution that combines both worlds while minimizing the duplication of rendering logic by embedding JSON in the view."

---

Let's introduce a simple example to illustrate the JSON embedding trick. It's [on GitHub](https://github.com/sandermak/embedded-json-example), with several branches showing different approaches. Say we have a screen containing a product list with paging:
![Example screen](/pics/embedded-json-app.png)

After the initial request to the server, the first page of products is shown. Once the page is shown, we want the paging links to be AJAX-enabled: no full page reloads. The server just returns JSON representing the products and the client renders them in a table.

### What duplication?
We have two main goals:

- Code for rendering the table should be written once
- User-experience must be as smooth as possible (i.e. no visible flickering for AJAX updates of the initial page)

Think about how you would implement this example. Strictly speaking, you can't really prevent duplicated logic. On the one hand, you need to render the table with the products from page one server-side on the initial request. Like in this JSP/JQuery based example:

{% highlight html %}
<table>
    <thead>
        <tr>
            <th>Product</th>
            <th>Description</th>
            <th>Price</th>
        </tr>
    </thead>
    <c:forEach var="product" items="${products}">
        <tr>
            <td><c:out value="${product.name}" /></td>
            <td><em><c:out value="${product.description}" /></em></td>
            <td><c:out value="${product.price}" /> Euro</td>
        </tr>
    </c:forEach>
</table>
<!-- paging links omitted -->
{% endhighlight %}

Then you also need to construct the table again after a successful paging AJAX call:

{% highlight javascript %} 
var products = // JSON result of AJAX call
$.each(products, function(index, product) {
    productTable.append($('<tr><td>'+product.name+'</td><td><em>'
          +product.description+'</em></td><td>'+product.price+'</td></tr>'));
});
{% endhighlight %}
(only do this when you can guarantee the concatenated strings are properly escaped!)

It's not just the grunt work of building the table rows again. Notice how we need to add the em-tag around the description in both templates. We can already see how specific duplication is creeping in. And real-world examples have an order of magnitude more complexity! By the way, the point I want to make in this post is agnostic of the Javascript framework used.  You can use your favorite Javascript templating/MVC library (too many to choose from!), I just picked plain JQuery since everybody is familiar with it. Of course the same goes for the server-side library: this issue crops with Rails, Django, and any 'traditional' webframework. 

### The obvious solution
How to prevent this mess? One option is to emit an empty placeholder table on the server-side, and to trigger an AJAX call from the client-side once the page has loaded to fill it with page one products. Twitter popularized this method for a while with its web-based timeline. Even though this moves all rendering of the paginated calls to the client, the solution unfortunately violates our second goal: a smooth user-experience. The user first sees an empty table which is later filled: 

![Diagram client-side-only](/pics/client-side-rendering-only.png)

Depending on the network latency the effects range from nearly unnoticeable to very annoying. In my [example](https://github.com/sandermak/embedded-json-example/tree/client-side-only) I added a 400ms delay to illustrate the effect of the additional roundtrip. Deferring the rendering of the complete page until the Ajax call completes is even worse. And think of how this would scale with many components on the page using this approach (hint: there is a [limit](http://stackoverflow.com/questions/985431/max-parallel-http-connections-in-a-browser) to the amount of concurrent connections browsers make).

Another option is to not pass JSON to the client, but return server-side rendered (partial) HTML for just this component. The trick is then to modularize your server-side code so that you can have a single template for both the initial request and subsequent AJAX calls. Component-based frameworks like JSF favor this approach. However, the consensus seems to moving towards consuming JSON on the client, giving the added benefit of a real API rather than a specific rendering band-aid.

The lazy option is to call it a day and revert to the old-skool [server-side rendering model](https://github.com/sandermak/embedded-json-example/tree/server-side-only), where everything triggers a full page refresh. But that would be giving up, whereas there still is another option...

### Embedded JSON
While the general idea of client-side only rendering is good, the execution can be improved. What if the server makes the product data available in a format digestible by the client in the first request, but without needing to render the actual HTML? It turns out we can do that by embedding JSON in the page rendered on the server:

{% highlight html %} 
<html>
<head>
    <!-- other header tags omitted -->
    <script>
        // Where the magic happens:
        var initialProducts = ${productJSON};
        // Fetch products using Ajax call and update html.
        function getProducts(page, pageSize) {
               // snipped
        }
        // Render products from JSON
        function updateProducts(products) {
              // similar to previous JS snippet, render table rows
        }
        // On document.ready, render first products.
        $(function () {
            updateProducts(initialProducts);
        });
    </script>
</head>
<body>
   <!-- body containing an empty table and paging links etc. -->
</body>
</html>
{% endhighlight %}

(View the full source of [this page](https://github.com/sandermak/embedded-json-example/blob/master/src/main/webapp/WEB-INF/views/index.jsp) here.)

The initialProducts JS variable is initialized with a JSON literal. This JSON literal is composed on the server in the controller, and exposed with the variable productJSON to the server-side template. Once the page is on the client, _it can use the client-side rendering logic without performing an AJAX request first_. Other product pages are retrieved using AJAX calls, then using the same client-side rendering logic encapsulated in the updateProducts function. Observe the sequence diagram, with one less roundtrip to the server:

![Diagram embedded JSON](/pics/embedded-json-rendering.png)

With this trick it's possible to blend server and client-side rendering where appropriate, without any duplication. In the [complete example](https://github.com/sandermak/embedded-json-example) you will see that the pagination links are actually still rendered with a server-side forEach loop.

As an added bonus, it is trivial to revert to the initial state of the page without a roundtrip to the server (the embedded JSON never changes). Since that's not always appropriate I didn't implement it in this sample app.

### Drawbacks
Of course after I 'discovered' this trick it turned out I wasn't [the first](http://www.bennadel.com/blog/1603-jQuery-And-Script-Tags-As-Data-Containers.htm). There are some drawbacks you should keep in mind. The biggest disadvantage is that whenever your JSON contains the string '</script>', this trick [breaks](http://stackoverflow.com/questions/4176511/embedding-json-objects-in-script-tags). The browser closes the script block the moment it encounters this tag, whether it's a part of a JS string or object literal, regardless of context. Escaping on the server is in order, since cross-site scripting is otherwise [possible](http://benalpert.com/2012/08/03/preventing-xss-json.html).

Because most webframeworks either render JSON or HTML as response, not a combination, you may have to look around for a solution. In my example I use the Jackson [ObjectMapper](http://jackson.codehaus.org/1.7.9/javadoc/org/codehaus/jackson/map/ObjectMapper.html) directly from [the controller](https://github.com/sandermak/embedded-json-example/blob/master/src/main/java/net/branchandbound/web/IndexController.java#L36-L39), with some small trickery to force the result into a string. Other frameworks and languages provide similar capabilities, for example [simplejson.dumps](http://simplejson.googlecode.com/svn/tags/simplejson-2.0.1/docs/index.html) in Python.

One of the attractive properties of the [client-side only](https://github.com/sandermak/embedded-json-example/tree/client-side-only) approach is that you can do away with server-side rendered templates completely. Instead, HTML can be served from an edge node in a CDN for example. Obviously, when you want to embed dynamic JSON this won't fly. May be [Edge Side Includes](http://en.wikipedia.org/wiki/Edge_Side_Includes) can help, but it was never accepted by the W3C. 

Lastly, when you're trying to unify client and server-side rendering for accessibility issues (i.e. the site must work without JS enabled), this is not the trick for you. Even though all information is provided in a single request, you still need JS to do the rendering. It's not progressive enhancement by any definition. If that's you want, you might enjoy LinkedIn engineering's [blog post](http://engineering.linkedin.com/frontend/leaving-jsps-dust-moving-linkedin-dustjs-client-side-templates) on how they went the other way around: falling back to 'client-side' templates on the server in these cases.

Like with any technique you'll have to weigh the pros and cons for each application. In my opinion, avoiding the ubiquitous first AJAX callback while still maintaining only a single template is worth a lot. 

