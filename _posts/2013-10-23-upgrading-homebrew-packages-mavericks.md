---
layout: post
title:  "Upgrading Homebrew packages on OSX Mavericks"
date:   2013-10-23 12:30:00 +0100
categories: engineering
slug: upgrading-homebrew-packages-on-osx-mavericks
---

After upgrading to OSX Mavericks today I discovered my PHP 5.5 install (via homebrew) to be broken, which is pretty much
par for the course after upgrading OSX. After a short moment of anger I started to work on fixing the issue, which I
have documented below for everyone else's benefit:


1. After installing OSX Mavericks upgrade, install the latest XCode from the AppStore (5.0.1 as of the time of writing)
2. Open XCode and accept the end user license agreement (or run `xcodebuild -license` from your terminal)
3. Install the latest command line tools from XCode by running `xcode-select --install`
4. Update brew to the latest version with `brew update`
5. Check your system health with `brew doctor` and fix any missing links, it should not report any issues with XCode
6. Upgrade all of your brew packages with `brew upgrade`

During the last step, if you receive any error messages like...

> <packge-name> dependency <dependency-name> was built with the following C++ standard library: libstdc++

...then you need to re-compile that dependency with brew by running
    
    brew uninstall <dependency-name> && brew install <dependency-name>
    
Keep running `brew upgrade` until you have re-compiled all of your dependencies. Personally I only came 
across 5 or 6 dependencies that needed to be re-compiled.

## Apache2

Upgrading your OSX install will mean you lose your `http.conf` that you customise to use the homebrew PHP install (or 
anything else that is an Apache module installed via homebrew). You will need to change the  `LoadModule` calls used in
the `http.conf` to point at your freshly upgraded homebrew packages.