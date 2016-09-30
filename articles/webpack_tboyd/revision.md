# Webpack: The Developer's Silver Bullet

Put succintly, modern web development is daunting, even for experienced developers.

On the one hand, the amount of code and clever engineering required to deliver a compelling web page is staggering. The chart below captures two statistics, the size of the average web page (measured in megabytes) and the average number of objects per page. The metrics shown span from the early days of the Web to 2014. More recent adoptions of Angular, React, and other popular frameworks have surely pushed these totals higher.

![Figure One: The increasing complexity of a web page](image2.png)

On the other hand, the diversity of web clients has exploded. Screen resolution, display size, the amount of raw computing power, and the range of browser capabilities vary wildly, from desktops at one extreme, to smart phones at the other. The next chart tracks the popularity of the top eight desktop, mobile, and tablet operating systems on the web during the past three years. Notice how the total share of page views coming from mobile has increased in such a short time.

![Figure Two: The Top 8 operating systems](image1.png)

In 2006, Javascript was a novelty. In 2009, mobile could connect telephone calls reliably, but did little else. Yet, in 2016, both technologies are utterly pervasive. If a site today provides a pleasant user experience both on a low-resolution smart phone and on a high-resolution monitor, it's an engineering miracle, constructed at the cost of thousands of developer hours.

<table class="c26">
    <tbody>
        <tr class="c21">
            <td class="c24" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">Year</span>
                </p>
            </td>
            <td class="c13" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">Complexity</span>
                </p>
            </td>
            <td class="c10" colspan="1" rowspan="1">
                <p class="c4"><span class="c12">Interactivity</span>
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
                <p class="c4"><span class="c12">Low</span>
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
                <p class="c4"><span class="c12">High</span>
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


## Progressive Enhancement

Developers are caught in a catch-22: Deliver a robust, interactive experience on fast & smart and dumb & slow devices. But there is a solution: *progressive enhancement:* Keep the initial payload light, then gradually add bells and whistles, commensurate with the capabilities of the client.

Progressive enhancement typically has been something of a hodge-podge. You can contort HTML, such as `script` tags, `async` script tags, `link` tags, and various methods of inlining to choose what to deliver to the client. There are Javascript APIs, such as dynamic script tags and third-party abstractions like `$.getScript()` to accomplish the same thing. *JSONP* and `eval` is another approach. And there are preprocessors to turn developer-friendly languages such as *CoffeeScript* and *SCSS* into browser-compliant counterparts. However, none of these are particularly elegant and none are automatic.

*Webpack* is different. It provides a unified, developer-friendly approach to smart asset packaging. Like *Ruby*, *Git*, and *Linux*, it adheres to a universal abstraction: everything is a *module*. No matter what type of asset you&rsquo;re must bundle, you can require it directly into your front-end code as if it were a Javascript module. Webpack's approach simplifies the allows for
