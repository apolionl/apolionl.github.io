---
layout: post
title:  "Welcome to my blog!"
date:   2020-06-29 14:34:25
categories: featured
tags: featured
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
---

If you are reading this post, probably, you are the PI that I have chosen for undergoing my next step in *i.e*  pursuing a postdoctoral in your research group. It could be that you are one of my dear friend that is verifying how I am doing. Anyway, _**welcome**_.

In this place you will find time to time tips on how to knowingly use `Latex`, `Julia`, `Gnuplot`, and many other programs for efficiently producing nice-looking plots. Other times, I will solely discuss fundamental concepts in quantum chemistry and how to properly address certain problems in specific software.

Let´s begin. In [gnuplot][gnuplot], it is always worthy to carefully set your own default settings, so when you plot something you obtain what you really desire, by that I naturally imply colours and some secondary settings that might improve tremendously the presentation (visualisation) of your data. First of all, create a settings file in your `$HOME` directory, the file is called `.gnuplot`, in that file include:

{% highlight zsh %}
defaultFont="Helvetica,12"
set term x11 enhanced font defaultFont
set encoding iso_8859_1
{% endhighlight %}

This sets the legible font and intentionally allows the effective use of special characters, subscripts, superscripts, Greek letters, and so on. Additionally, one can instantly set the colour scheme to employ in plotting. With this proper regard, I typically implement a colourblind safe set. In this example, there are six colours that I typically use, for more specific information, please visit the gnuplot official website or google colourblind safe palette.


{% highlight zsh %}
set	style	line	1	lc	rgb	'#a6cee3'	lw	2
set	style	line	2	lc	rgb	'#1f78b4'	lw	2
set	style	line	3	lc	rgb	'#b2df8a'	lw	2
set	style	line	4	lc	rgb	'#33a02c'	lw	2
set	style	line	5	lc	rgb	'#fb9a99'	lw	2
set	style	line	6	lc	rgb	'#e31a1c'	lw	2
set     style   increment user
{% endhighlight %}

Here a silly example. 

![The plot](/assets/article_images/2014-08-29-welcome-to-jekyll/pic-gnuplot.png "Sin(x)")

Hope this helps you. 

[gnuplot]:      http://www.gnuplot.info/
