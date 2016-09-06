---
layout: post
title:  "Why soft deletes are evil and what to do instead"
date:   2016-01-23 12:30:00 +0100
categories: engineering
---

For a long time I've always thought that soft deletes are the "right way" of deleting records from your database. Like
most things within software development, you end up realising that things are not always so black and white.

Whilst soft deletes provide a "safe" (read: easily reversible) way of deleting records, I have come to realise that they
can create far more problems than they solve.

## Letting the database do its job

Our database is the best tool for managing relationships between our data, and when we use soft deletes we are
preventing the database from doing its job. We take the responsibility for cascading deletes away from the database
layer and put it in the application layer, which <em>is not</em> designed to take care of this for us (some ORMs will
provide ways of cascading delete operations in memory - but these merely act as a proxy to trigger lifecycle callbacks
on entities).

Consider the following contrived example, where we have many `User` objects which each belong to a single 
`Group` object. Each `Group` has many `User` instances associated with it in the database. Consider these in the
following PHP model...

<script src="https://gist.github.com/jameshalsall/69b4643552abf59c9856.js"></script>

<script src="https://gist.github.com/jameshalsall/816c3b51d07a376fb83b.js"></script>

This is straightforward enough, so let's think about deleting a `Group` object:

<script src="https://gist.github.com/jameshalsall/e6cbe257b1c2e836b7db.js"></script>

This can go one of two ways at this point depending on whether we are using soft deletes, or hard deletes:

 * If we're using hard deletes, awesome - we have just removed our group and all of the users associated with it. We 
   have no redundant data, but we also cannot recover any of the deleted users that we may have wanted to keep.
 * If we're using soft deletes, we have a couple of issues. We haven't deleted any of the users associated with the
   group we deleted, which could be what we wanted but is an unintended benefit of the situation. When we soft delete
   the group we also expect all of the associated users to be soft deleted. The problem here is that our database has
   no native support for soft deletes, so we have to make our application do all the work.

## What can we do instead?

Let's take a look at our `GroupManager::delete()` method again, but this time let's take into account the necessity to also delete the `User` objects that are associated with it.

NB. You would never implement the deletion like this, because all of your users would have to be stored in memory. However, it serves to illustrate the point.

<script src="https://gist.github.com/jameshalsall/08117c0a361e5149c812.js"></script>

That works, but it isn't ideal. We now have our `GroupManager` not only managing the persistence of `Group` objects but also
managing `User` objects too. This breaks SRP and we can do a lot better. How about if we fired an event when the group is deleted?

<script src="https://gist.github.com/jameshalsall/760835a12500605cb242.js"></script>

This let's the rest of our application know that we're deleting a group and gives any listeners a chance to clean up
related data. We would probably have a dedicated listener for deleting any related user objects, so it achieves what we
set out to solve: cascading soft deletes.

It's easy to see already that our domain logic will become quite "polluted" with this kind of logic, and we want to
reduce the amount of code we write, not increase it. Not only that, what if someone removes that listener or forgets to
add one somewhere else for new relationships? Things get messy when you have data floating around your application which
should have been deleted but never was because we forgot to write the code to do it for us. It's nasty.

## Creating an Audit Log

As I mentioned earlier in the article, as soon as we start using soft deletes we are preventing the database from doing
its job. We configure our database to manage relationships between data using foreign keys and cascading operations. It
is far better to have the database perform hard deletes on your data. It is the best tool for doing so.
However, what about the benefits of a soft delete that we lose with a hard delete? The main two are accountability for
action and ease of recovery of our data. This is still achievable with hard deletes, but we just have to go about
it in a different way.

I'm now very much in favour of the idea of audit logs for deletions. A basic audit log can simply store objects (if
using an ORM) or records in a serialized format so that we have a copy of them for future use. Let's consider a simple
example using our `GroupManager`. We will still fire an event in our `GroupManager::delete()` method but this time it
will be more generic to allow a listener to create a copy of it in the audit log...

<script src="https://gist.github.com/jameshalsall/b2036e8d7fe927b5caf7.js"></script>

And our listener will look something like this...

<script src="https://gist.github.com/jameshalsall/c2c9a162492ef0396740.js"></script>

Whilst this isn't a perfectly SOLID example, it demonstrates what we can do. Our audit manager will persist the new
`AuditEntry` containing a serialized copy of the data removed, the name of the original object class (so we can restore
it later if we desired), the user who performed the operation and the time at which the operation was carried out.

Our domain layer is now no longer concerned with hard or soft deletes, we are simply creating an audit trail of delete
operations (which is good practice anyway) without complicating our business logic. Best of all, our database is the 
tool responsible for deleting our data.
