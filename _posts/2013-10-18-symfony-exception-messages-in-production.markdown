---
layout: post
title:  "Displaying exception messages in a Symfony production environment"
date:   2013-10-18 21:30:00 +0100
categories: engineering
slug: displaying-exception-messages-in-symfony-production-environments
---

When we throw an exception in our Symfony applications, we can be quite lazy and have Symfony catch it for us and
display a nice error message to our user. This is great, most of the time we don't want to show detailed exception
messages to the user. We certainly wouldn't expose any sensitive information like database names for example. Sometimes
however, we want to use our exception messages as a way to communicate back to our user that something went wrong.

The Symfony standard distribution will display its `TwigBundle/Resources/views/Exception/error.html.twig` template
content to the user when an exception is thrown and not caught explicitly. This is great, apart from the fact that we
may want some exceptions to have their message displayed in the error template to explain to the user what went wrong.

Symfony handles exceptions using the `kernel.exception` event, which means we too can hook into this in our container
configuration and intercept the exception handling with our own logic. Let's start by creating our 500 error template
that will be used to display the exception message to the user. This will reside in
`app/Resources/TwigBundle/views/Exception/error500.html.twig`, and will only be used for exceptions and error responses
(500 error codes)...

<script src="https://gist.github.com/jameshalsall/042266654e4952a5d737.js"></script>

This is just a simple example of what you can do with your template. If you are wondering what that `status_text`
template variable is, we set that in our exception handler which will look something like below...

<script src="https://gist.github.com/jameshalsall/017ee68b95fb0e880304.js"></script>

The aim of any exception handler in Symfony is to provide a `Response` object (or throw another more relevant exception)
which can be returned for the request. In our handler we return a `Response` instance (created via the templating
engine) with a single template variable `status_text` containing our exception message. Symfony will then return our 
response to the client that made the request.

To finish off we need to hook our exception handler into our service container with some configuration. If you are using
XML for your configuration it would look something like this...

<script src="https://gist.github.com/jameshalsall/a37a19dcbe336976f843.js"></script>

Our `<tag />` XML tag tells Symfony that this is an event listener, and that it wants to subscribe to any
`kernel.exception` events fired during the life cycle of the application. Providing your service container configuration
is loaded, you should see your custom exception template after a container recompile (`app/console cache:warmup`).
Obviously, this will only be used in a non-debug environment because Symfony will display the much more verbose
exception view in debug mode.

### Being Careful Exposing Exception Messages

Although this is a quick way of getting exception messages back to your user, it would be wise to take this one step 
further and use a custom `Exception` class that indicates that it is okay to display the exception message to the user.
