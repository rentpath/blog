# Webpack: A Tool for the Age

A modern web page is a complex and sizeable tangle of content and technology. Page size, as measured in kilobytes, has more than doubled in the recent past, while the number of page objects has steadily increased. Figure One shows both trends between 1995, the early days of the web, and 2014. 

![Figure One: Complexity of a web page](image2.png)

If you&rsquo;re a web developer, these metrics likely come as no surprise. Indeed, you may argue that &ldquo;web page&rdquo; is something of a misnomer. &ldquo;Application&rdquo; is an increasingly apt description of what&rsquo;s being delivered to so many browsers. A modern web page can include megabytes of *HTML*, *CSS*, and *JavaScript*. 



 

 as more and more features are delivered over the wire. It is not uncommon 
 

Web pages continue to supplant most forms of computing, including the desktop, having reached parity in features and interactivity. Indeed, it's something of a misnomer to call a Yet ironically, 

Webpack is indispensable for developing modern, responsive, and full-featured web pages. It places unprecedented power into the hands of developers to control asset delivery. 

Let’s begin by looking at how the industry has changed in the past 10 years.

![Mobile usage](image1.png)

**1. Mobile internet consumption as a proportion of total traffic has nearly doubled in the past 3 years.**

The chart above shows the global operating system breakdown for all internet traffic since 2013, and how the proportion of traffic coming from mobile devices has risen from 15-20% to 30-35%. The story for North America is the same, although the breakdown for each category differs. For example, iPhone traffic leads Android traffic in the United States, even though the overall proportion of mobile traffic is roughly the same as in other parts of the world.

I believe that this is mostly due to the fact that Android device manufacturers like Motorola, LG, and Samsung have forced Apple into a price war. The price of an Android phone has reduced by more than half since 2010, and iPhone prices have fallen considerably to match. 

But another thing has been happening:


**2. Client-side functionality has increased 10x in the past 10 years.**

The amount of client-side functionality and interactivity expected on laptops and desktops has grown by an order of magnitude, as shown above. So web developers are now in a catch-22, where we need to stay as lightweight as possible to minimize load times on resource-constrained devices, but we also need to include a megabyte of Javascript code to give our products those bells and whistles that users expect when surfing the web from a dual-core laptop with 6 gigabytes of RAM. 

I’m convinced that the confluence of these two trends has changed web development into something entirely different from what it was 10 or even five years ago. I’ve summarized this change in the following table:

<p class="c5 c23"><span class="c16 c17">Evolution of the web platform over time</span>
</p>
<table class="c26">
    <tbody>
        <tr class="c21">
            <td class="c24" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">Year</span>
                </p>
            </td>
            <td class="c13" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">Platform convenience</span>
                </p>
            </td>
            <td class="c10" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">App interactivity</span>
                </p>
            </td>
            <td class="c0" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">Variety of compatible clients</span>
                </p>
            </td>
        </tr>
        <tr class="c21">
            <td class="c24" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">2006</span>
                </p>
            </td>
            <td class="c13" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">High</span>
                </p>
            </td>
            <td class="c10" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">Low</span>
                </p>
            </td>
            <td class="c0" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">Large</span>
                </p>
            </td>
        </tr>
        <tr class="c21">
            <td class="c24" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">2016</span>
                </p>
            </td>
            <td class="c13" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">Low</span>
                </p>
            </td>
            <td class="c10" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">High</span>
                </p>
            </td>
            <td class="c0" colspan="1" rowspan="1">
                <p class="c4"><span class="c15 c20 c16 c25">Unimaginably huge</span>
                </p>
            </td>
        </tr>
    </tbody>
</table>

The fact that you can still use any device with a screen in your household to access the web is what more or less guarantees that the web will be around forever, but it’s no longer pleasant to work with or conceptually simple. And the DIY culture of the Javascript community has been making this problem even worse. But that’s where Webpack comes in.

### Webpack’s universal abstraction

Webpack, like Ruby, Git, and the Unix operating system, adheres to a universal abstraction. Everything in Webpack is a module. This means that no matter what type of asset you’re bundling, you can require it directly into your Javascript using whatever method you like.

CommonJS:

```javascript
require.ensure([“icon.png”, “stylesheet.css”], function(require) {
	var icon = require(“icon.png”);
	var stylesheet = require(“stylesheet.css”);
	…
});
```

AMD:

```javascript
define([“icon.png”, “stylesheet.css”], function(icon, stylesheet) {
	…
});
```

`define` and `require.ensure` are the signals Webpack uses to draw its internal dependency tree for your system of asset files. This tree can be arbitrarily complex, and produces fruits called chunks.

A chunk is a bundle of Javascript that contains a set of asset files. If you run Webpack with `--display-chunks`, you will see Webpack concatenate all of your assets into a single chunk by default. This is how most people have managed large Javascript projects in the past. But ever since mobile devices have taken over the internet, that behavior is no longer optimal. That’s where split points come in.

Your split points are the points in your code where Webpack encounters a define or require.ensure. These are places where Webpack knows it can separate a piece of code into separate chunks. The genius of Webpack is that split points don’t necessarily follow file boundaries, but are entirely up to the developer; one file can have a single split point at the top, or it can have several. The result is greater flexibility in loading your assets. You can have sections of your code block the rendering of the page, while other sections load concurrently in the background. Again, this is crucial in 2016 when you have to support a range of devices with unpredictable connectivity.

### Using complexity to simplify something even more complex

When I first heard about Webpack processing SCSS into CSS, then transforming it into JSON to be required into Javascript and inlined into HTML, my mind recoiled in horror. Why would anyone do things this way? Why not just use link tags like a person of sanity?

The answer is that Webpack assumes some complexities, like CSS preprocessors, CJS/AMD/ES6 module syntax, and asynchronous loading logic, for one simple reason: to automatically chunk interdependent assets together, no matter what they are. Once you understand this, you accept the complexity as a necessary evil in order to achieve this extremely ambitious goal.

For example, say your site’s assets are arranged in this dependency graph:

[[image3.png]]

In short, you have a component, shown only after a user action, that is dependent on one of two possible libraries chosen through feature detection. This highly synchronous process is made up of a chain of practically unpredictable events, which I’ve marked with dotted lines. You don’t know when the user is going to click the button, you don’t know when the JS and CSS libraries will finish loading, and you don’t know how long the setup code will take on the user’s device. The only thing you can really guarantee is the loading of the HTML and the feature detection code. But you want each of these processes to:

1. Be in the right order.
2. Be as concurrent as possible.
3. Happen only once. 

In the past, you might solve this problem with dynamic loading techniques like passing callbacks, dynamically creating script tags, keeping track of state through booleans scattered throughout your code, and so on. You might end up with a sloppy state machine scattered across about 5 files that is still slow and vulnerable to race conditions.

Here’s an optimal solution using Webpack:

```javascript
define([
    'jquery',
    'feature-detect'
 ], function(
    $,
    featureDetect
 ) {
    var clickedComponent;
 
    if(featureDetect()) {
        clickedComponent = function() {
           require(['setup-component-1'], function(setupComponent) {
               setupComponent();
           });
       };
       require(['setup-component-1'], function(setupComponent) {});
    } else {
       clickedComponent = function() {
           require(['setup-component-2'], function(setupComponent) {
               setupComponent();
           });
       };
       require(['setup-component-2'], function(setupComponent) {});
   }

   $("button.display-component").click(clickedComponent);
});
```

Looks like regular Javascript written in the AMD style, right? It is, but Webpack has repurposed `require()` to define a split point, or a break in the synchronous flow of the code where assets can be split into separate chunks.

But this doesn’t mean you have to write in AMD or limited to libraries written in AMD. Webpack also supports CommonJS-style modules, as well as modules that interact with globally scoped objects like `window`. Webpack even supports modules that use AMD or CommonJS incorrectly. You can mix and match all of these types of libraries in one code base, while writing your app code the way you prefer.

### Set your publicPath

There is one slight caveat with this technique, and that comes into play when you are using a CDN to deliver your assets. 

When using Webpack’s dynamic require as shown above, Webpack will automatically insert the necessary code into your app bundle to fetch and evaluate the deferred sections of code. This means Webpack needs to know the URL from which to pull this code, or else it will attempt to pull it by default from the relative root path. [The way to specify this is via the publicPath.](https://github.com/webpack/docs/wiki/configuration#outputpublicpath)

This only becomes a concern once you use a dynamic require, so not every Webpack configuration will have the publicPath specified. Whenever you use this technique, you should be sure to check to make sure the publicPath is properly set.

### Conclusion

I don’t know enough about Webpack yet to guarantee that it won’t be made obsolete by some other tool within the next couple of years. But, I do know enough to say that mastering a tool like Webpack will pay off many times in the long run if you are heavily invested in developing for the web platform. Even if Webpack stays in the margins of the Javascript community, its innovative principles will guide the design of its own future usurpers.

Like Rails in 2009, Webpack is a mature technology outside the mainstream that has nonetheless attracted a community of highly productive people and created its own niche. I’m excited to learn Webpack and look forward to see what comes of it.

References:

* http://www.yottaa.com/company/blog/application-optimization/a-brief-history-of-web-page-size/
* https://medium.com/@rajaraodv/webpack-the-confusing-parts-58712f8fcad9#.xm9oh8m87
* https://webpack.github.io/docs/code-splitting.html
* https://github.com/webpack/docs/wiki/shimming-modules

<script data-codementor="tylerboyd" data-style="badge" data-theme="light" src="https://cdn.codementor.io/assets/badge.js"></script>

