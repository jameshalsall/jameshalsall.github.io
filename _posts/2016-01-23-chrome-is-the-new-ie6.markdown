---
layout: post
title:  "Chrome is the new IE6"
date:   2016-01-23 04:30:00 +0100
categories: opinion
---

Google Chrome. The saviour of the browser. The best rendering engine in all of the land, or at least that's how it felt 
back in 2012. The former best browser on the planet is slowly becoming a replacement for Microsoft's behemoth that was 
IE6. Whilst this seems very harsh, let me explain.

IE6 was cursed because of its insistence on abiding by its own rules. A complete ignorance to the standards adhered by 
the rest of the web (achieving an earth shatteringly bad score on the 
[Acid test](http://www.tomshardware.com/news/internet-explorer-ie9-acid-test,12412.html)) it became a law unto itself.
With most corporate environments adopting a Windows based operating system, and hence Internet Explorer, it quickly 
became the rule that if you were developing an application intended for use on the web, that it was basically built for 
IE6. This in itself is not a huge problem, but when you take IE6's insanely bad Acid score and the fact that Microsoft 
enjoyed implementing their own version of a W3C spec, you end up in an ecosystem dominated by a single corporation's 
view of the said ecosystem.

Well, what has this got to do with Google Chrome? A hell of a lot actually. Let's rewind a few years back when Chrome 
was hot on the scene and everyone was still rocking Internet Explorer 8. Yes, back then I too was using a Microsoft 
based machine and trying my best to avoid IE. When Chrome dropped it was like a breath of fresh air. They took the web 
seriously. They respected the upcoming HTML5 spec, and even the parts of it that were still in draft were supported in 
some kind of fashion. As the world slowly adopted Chrome and it became the defacto web browser I felt saved from the 
nightmare that had been IE6 (and its successors IE7/8) and re-born into a world where modern web standards stood a 
fighting chance.

In the early days Chrome held the torch for what every other browser should do. Follow the modern web standards. 
Ditch old vendor specific standards (such as the ones created by IE6 that had so many vendors locked into developing 
apps specifically for that browser). Offer a fresh new interface.  Basically do what most other browsers were not doing
at that time by offering something refreshing. Awesome.

Let's fast forward to 2015. Last year. For me, the year that all UX went to total shit. Chrome was no exception to this.
Whoever was designing Chrome's UI needs a punch in the head. Let's take a look at an example below:

<img src="http://i.imgur.com/InlO74b.png" alt="chrome ui" title="Chrome's awful UI" />

On initial glance it looks like there is only a single call to action. This is the "Customize Fonts..." button. 
Makes sense. Hold on, what about the "Manage autofill settings" hyperlink. Yes, that is also a call to action. 
So is the "Manage Passwords" link below it. So what makes these two different from the fonts button? Absolutely nothing.
This is ridiculous from a company the size of Google. Not only is this extremely confusing, it makes absolutely no 
sense in terms of the decisions around what deserves a button and what gets the hyperlink treatment.

Let's get into standards though. I mean, that's what IE6 was so famously bad for right? Well Chrome has now started its 
own death march down this path. Back in 2013 Chrome split away from the WebKit rendering engine (which it used in its 
first 5 years) to create its own Blink rendering engine. Hold on, why? What was wrong with WebKit? The rendering engine 
used by Safari was obviously no good for Google, and I believe it's because Chrome want to create a monopoly on the web
by isolating other rendering engines and introducing their own quirks to adopted standards.

So, adopted standards and Google's insistence on bringing their own "interpretation" to such standards is something that
massively infuriates me. Very recently this came to light when I was working on Arsenal.com's login and registration 
form, where they requested that I disable the autocomplete functionality on the login form due to the sensitive nature 
of said form. This makes sense, you don't want someone entering their details on a public machine and it remembering 
them so someone else can harvest your email address. So I set about setting the
[autocomplete attribute](https://developer.mozilla.org/en/docs/Web/HTML/Element/Input#attr-autocomplete) to "off" on the
form so that it wouldn't offer to remember a user's details. Worked great in Safari and Firefox. Even Internet Explorer 
10 worked great. Chrome on the other hand proved to be a complete different story.

Chrome's rendering engine and DOM refused to accept that the autocomplete attribute had any bearing on how a form should
behave, even though its an accepted part of the HTML5 spec. In fact, let's take a look at what the HTML5 spec says about
this attribute when it has a value of "off":

> The "off" keyword indicates either that the control's input data is particularly sensitive (for example the activation 
> code for a nuclear weapon); or that it is a value that will never be reused (for example a one-time-key for a bank 
> login) and the user will therefore have to explicitly enter the data each time, instead of being able to rely on the 
> UA to prefill the value for them; or that the document provides its own autocomplete mechanism and does not want the
> user agent to provide autocompletion values.

Okay, so pretty evident that `autocomplete="off"` on a form means that _it should not autocomplete any email addresses 
or any other information whatsoever_. Like I said, all other browsers worked as expected bar Chrome, which continued to 
offer autocomplete suggestions on the email input. I was completely baffled (and angered) by this so I started to do 
some research into why Chrome behaved this way. Google apparently believe that the user should have greater control over
form behaviour than a website vendor. They believe that a user should have the option to continue to autocomplete their
email address regardless of whether a website vendor <em>knows</em> that a particular input contains sensitive data and
should not be autocompleted by the browser. This is insane, it is direct disregard for the adopted HTML5 spec.

At this point I started getting flash backs of the years gone by where IE6 was the standout (for the wrong reasons)
browser because it didn't follow the HTML spec and made up its own rules. Well guess what? We've already seen the
beginning of this with Google Chrome, and it's only going to get worse as time goes on. With 
[Chrome being the most popular browser](http://www.sitepoint.com/browser-trends-april-2015-statcounter-vs-netmarketshare/)
right now we can only expect Google to flex its muscles more and more. If you have a monopoly on the market then you can
start to enforce your own view of said market.

In 5 years we will have another IE6 on our hands in the way of Chrome.

Someone help us.