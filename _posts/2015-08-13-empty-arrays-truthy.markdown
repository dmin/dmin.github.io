---
layout: post
title:  "Empty arrays are truthy!"
date:   2015-08-13 23:34:27
categories: values booleans expression
---
A quick reminder (if only to myself) that only the following expressions in JavaScript evaluate as falsy when used where a boolean is expected (Thank you, [JavaScript: The Definitive Guide](http://www.amazon.com/gp/product/0596805527/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0596805527&linkCode=as2&tag=blog0ae8-20&linkId=YGOTNSSIMJW3W6FX)).

{% highlight javascript %}
undefined
null
0
-0
NaN
"" //(empty string)
false
{% endhighlight %}

Everything else in JavaScript evaluates to a truthy value when used where a  boolean is expected. Take special note that empty arrays and empty objects are not included on that list. I've been burned before!

{% highlight javascript %}
[] // truthy
{} // truthy
new Boolean(false) // truthy - if this expression was ever used in real code I'd love to know why!
Boolean(false) // trick question - its falsy!
{% endhighlight %}
