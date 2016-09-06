---
layout: post
title:  "Running PHPUnit with HHVM on Mac OSX"
date:   2014-02-08 12:00:00 +0100
categories: engineering
---

If you are a PHP developer who is still yet to hear about HHVM (HipHop Virtual Machine) then you must have been living
under a rock for the last 2 years. HHVM is Facebook's grand contribution back to the PHP community, 
[providing up to 40x faster throughput](http://www.hhvm.com/blog/1817/fastercgi-with-hhvm) on a standard WordPress site.

Whilst this is great for the PHP community, we are still waiting on 100% parity between the HHVM and the Zend Engine 
(which is the default PHP engine). In the meantime, enterprise frameworks such as Symfony and Doctrine's ORM are out in
the cold and cannot take advantage of HHVM. However, we can make use of HHVM for some of our day-to-day toolsets, and 
one of my most commonly used tools is PHPUnit.

# Installing HHVM

Installing HHVM on Mac OSX is made easy by doing so via [homebrew](http://brew.sh/). We can install homebrew by 
following the instructions at the bottom of their homepage.

Next, we need to tap into some new brew repositories in order to install our HHVM. Run the following commands to do so:

<script src="https://gist.github.com/jameshalsall/5847eaa51fc9febf454e.js"></script>

Now our homebrew knows where to install HHVM from, we can tell it to do so:

<script src="https://gist.github.com/jameshalsall/ca8a386c3efab76d822a.js"></script>

HHVM can take a while to compile and install, so just be patient. It took about 45 minutes on my MacBook and will vary 
depending on your hardware.

We can verify the install by running the following command...

<script src="https://gist.github.com/jameshalsall/58ab7f492d9e44d2cc92.js"></script>

...and if all has gone smoothly we will see some output with the HHVM version number we currently have installed.

Now we can run our PHPUnit using HHVM (your path to PHPUnit might differ)...

<script src="https://gist.github.com/jameshalsall/bb27f57816d0124c9ed9.js"></script>

We can go one step further and alias our PHPUnit (or whatever other tool you want to run) by adding the following to 
our `~/.bashrc`...

<script src="https://gist.github.com/jameshalsall/497979ad8f24f948ae6b.js"></script>

...which will facilitate a new command `phpunit-hhvm` allowing us to leave our original PHPUnit command running via the
standard PHP Zend Engine.

Easy money.
