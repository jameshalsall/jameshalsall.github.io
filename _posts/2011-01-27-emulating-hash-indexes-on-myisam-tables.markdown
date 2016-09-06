---
layout: post
title:  "Emulating hash indexes on MyISAM tables"
date:   2011-01-27 20:30:00 +0100
categories: engineering
---

Database administration is possibly the scariest concept for me as a developer, mainly because I have neglected it for
so long. Everyone knows the basics, and even though I had a solid theoretical understanding of how databases store
information, I wanted to further my development in this area. I picked up a book by O'Reilly called
[High Performance MySQL on Amazon](http://www.amazon.co.uk/High-Performance-MySQL-Optimization-Replication/dp/0596101716/ref=sr_1_1?s=books&ie=UTF8&qid=1296162009&sr=1-1),
which seemed to be perfect and pick up right where my comfort zone ended.

Database indexes are tricky and, as with anything, there are trade-offs for each decision you make when designing your
schema. In MySQL, the Memory storage engine supports a special variety of indexes called hash indexes. These indexes
store a hash value of all the indexed columns in a table and store it for super-fast lookups. Great. But Memory tables
are only useful for limited datasets that can fit in your machine's memory, and realistically we want to be able to use
these hash indexes on our MyISAM storage tables if possible (InnoDB uses 
[adaptive-hash indexes](http://dev.mysql.com/doc/refman/5.0/en/innodb-adaptive-hash.html)).

Let's say we have a MyISAM table that stores user data as follows...

<script src="https://gist.github.com/jameshalsall/e2f322662e639ccbf69a.js"></script>

We want to lookup users from this table when they attempt to log into our application, so our query would look something
like this...

<script src="https://gist.github.com/jameshalsall/91fdd4a980090312119b.js"></script>

As we know, we only have an index on the user_id column of our database table, and so MySQL would have to do a
full-table scan in order to locate our desired row of data. As MyISAM locks at a table level, whilst the query is
scanning for our row no other database connections will be granted a read-level lock until it has finished. A nasty
bottle-neck obviously. However, we can improve the responsiveness of our query by adding an index to our username column
in our table. Whilst this will improve our lookups slightly, we can go one level better. We can emulate a hash-index for
the username field and create a much faster lookup operation by using MySQL's built-in `crc32()` function. First, we
need to add a column to store the hash-value in our table...

<script src="https://gist.github.com/jameshalsall/b1311c4357620941141c.js"></script>

Our `crc32()` function will generate an integer hash value of our username field, and we just need to create a trigger
on the table in MySQL so that this value is stored every time a new record is created or an old one is updated...

<script src="https://gist.github.com/jameshalsall/bd2ddb59728ca489c9c7.js"></script>

_We make use of the DELIMITER operator to change our delimiter to a pipe character (|) which allows us to use 
semi-colons to terminate our trigger statements. Note that this is set back to a semi-colon at the end of the trigger
creation._

Now we have our hash indexes, inserting a row into our users table will now store a `crc32()` hash of the username in
the username_hash column, the same will also happen when updating a record in our table too.

## Why a Hash Index?

There was nothing particularly wrong with adding an index on our username attribute, but our integer hash index is far
better from a performance point of view for a couple of reasons:


* Our hash index is far smaller in size, hence saving on storage and disk I/O operations on large datasets
* MyISAM uses packed indexes for strings, which makes lookups far slower on a `VARCHAR` datatype than on an `INT`

We can now use our `username_hash` table attribute to find our desired row of data like this...

<script src="https://gist.github.com/jameshalsall/a0d3f3fa5d7bccd6090c.js"></script>

Because of the way that MySQL's query optimizer works, it will notice that there is a highly selective index on the 
`username_hash` column, and fetch rows much faster. That should reduce the amount of time our table stay's locked up for
reads.
