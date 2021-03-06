---
layout: post
title:  "Empty arrays are truthy!"
date:   2015-08-13 23:34:27
categories: values booleans expression
---
A quick reminder (if only to myself) that only the following expressions in JavaScript evaluate as falsy when used where a boolean is expected (Thank you, [JavaScript: The Definitive Guide](http://amzn.to/1La9TSK)).

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
