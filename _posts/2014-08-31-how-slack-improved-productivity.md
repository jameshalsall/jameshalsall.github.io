---
layout: post
title:  "How Slack improved our productivity"
date:   2014-08-31 12:30:00 +0100
categories: productivity
slug: how-slack-improved-our-productivity
---

As programmers we generally hate disruption. We get halfway through writing a test to have someone interrupt our flow
and set [us back by 15 minutes](http://www.cc.gatech.edu/~vector/papers/sqj.pdf). Imagine that 4 or 5 times a day (if 
you are lucky), and we're losing about 5 hours of productive coding time every week.

That's not to say that interruptions are unnecessary. As programmers we all understand that coding only makes up one
part of our job. Meetings, knowledge sharing, retrospectives, design, debugging etc. are all important aspects as well.

Knowledge sharing is probably the most common type of interruption at Rippleffect. We work within project teams and
frequently have to share our knowledge with other team members. This is where they can become quite costly, certainly in
terms of programmer productivity.

## Interruptions

Imagine a question such as "Do you know if anyone has implemented a way of re-indexing the site search yet?". Now, this
can go one of three ways...

1. The askee (person being asked) may not know the answer to the question, or may not have been on the project long
   enough to know for sure. So someone has just been interrupted and has lost 10-15 minutes of coding time without the
   asker getting an answer.
2. The askee knows the answer and informs the asker exactly how to re-index the site search.
3. The askee knows the answer and informs them that there is no way to re-index the site search currently.

Two of the above scenarios lead to a desirable outcome. Whenever the askee knows the answer to the question we can say
that the interruption was worth it, from a monetary point of view. If the question was never asked then someone may well
have duplicated a way of re-indexing the site search, wasting hours of company time.

The thing worth noting above is that all three of those scenarios do not allow the askee to make a decision as to
whether to stop what they are doing and respond. We all have good times to break our flow throughout the day, usually
when we've just committed and pushed a component or unit test etc. It's at these times we want the questions to be
asked, but there's no way that the asker could actually know when people around them have just finished a piece of work.

## Enter Slack
[Slack](https://slack.com/) is a great channel / group based chat service, much like that of IRC but with a lot more 
polish. We introduced Slack fully about 3 months ago and have found that not only does it bring the various  project
teams closer together, it also creates a forum for suggesting best practices, questioning current practices and raising
overall department awareness of current goings on.

There are two tools within Slack that let you achieve the above:

1. Private groups
2. Channels

### Private Groups

We use private groups to bring project teams together to talk about project specific issues. These are more suitable
than channels which allow anyone in your organisation to join, as we like to keep project chat very clean. It's here
that you ask your project specific questions (more on that below).

### Channels

Channels are public within your organisation (restricted by your email domain) so they are more suited to topic based
chat. We have channels for things like...

* PHP
* JavaScript
* .NET
* Continuous Integration
* Design Patterns

...and so on. This allows the chat to be much more informal, but still on topic. If I don't want to discuss anything 
related to Continuous Integration then I can remove myself from the channel.

## How does Slack help productivity?

Earlier we talked about how vocal questions can impact the askee's productivity involuntarily. In an agile project team
this can become quite an issue, especially if you are acting as both the product owner and the lead programmer on the
team. Slack makes questioning like this more of a voluntary interaction for the askee, so they can choose when to reply
rather than being forcefully interrupted.

Let's take the example earlier with the site re-indexing. If Joe asks his question on the project group in Slack, not
only is he not forcefully interrupting the askee but he is also offering his question up to everyone else in the project
group. This means the most qualified person is usually the one who responds. At the very least, Joe is guaranteed to get
the best possible answer from all of the people available on the project that day.

### What if it's blocking development?

At Rippleffect we still interact a lot face to face, there is a lot of mentoring that goes on in the studio and we are
all committed to creating the best solution for the problem. We endeavour not to let our tools get in the way of that
and Slack is no exception. If the question is stopping a developer from continuing at all then we always advocate
speaking directly with the project lead. Most blocking issues are resolved as part of daily standups, so it's very rare
that this is the case.

## Summary
Since introducing Slack, our programmers have been able to focus on their job at hand and optionally interact rather 
than being forced to. Our staff now discuss things that would never have been discussed before, our knowledge sharing is
far better and we are always challenging each other. There are plenty of alternatives to Slack, such as 
[HipChat](https://www.hipchat.com/) which is owned by Atlassian, but they all do much the same.
