---
layout: post
title:  "How to iterate huge query results in Magento 2"
date:   2018-11-12 18:57:39 +0100
categories: engineering
---
Working with Magento 2 we often use collections to fetch and work with data from our database. For the most part these
work perfectly fine, but when it comes to fetching and iterating millions of records in a single process it becomes extremely 
difficult because memory usage becomes a problem. From my experience the overhead of using a collection is too high when
dealing with result sets of this magnitude, so we need to look for an alternative solution. In this article I'm going to
talk about how to handle huge result sets in Magento without crippling your PHP process.

## Querying and loading huge results

Magento 2 ships with a helper (`Magento\Framework\Model\ResourceModel\Iterator`) that enables us to iterate huge 
database result sets by applying callbacks to each row in the result. This is perfect when you have millions of records
that you need to process without loading them all into memory. Let's look at this in a real example: 


<script src="https://gist.github.com/jameshalsall/ef7c2c820cdc1dfea37c35b3b9da345c.js"></script>

A few things are happening here, so let's look at them:

1. We are injecting an `IteratorFactory` which is an auto-generated factory from Magento's dependency injection container
2. We are creating a simple `SELECT` query to fetch data from the huge table, intentionally omitting the `OFFSET`/`LIMIT`
(more on this below)
3. We are creating an iterator and walking through each result in the query by passing a single callback function 
(notice that we can provide many callbacks here if we wanted to)

The intention here is to only ever load a single row into memory at any time (unless you assign i tot a variable outside 
the scope of the callback function passed to `$iterator->walk()`). If we achieve this then it does not matter how many
rows we need to process, it should not increase our memory consumption.

### Why not just use `OFFSET`/`LIMIT`?

The first thing that might stand out here is that we are not fetching batches of results using MySQL's `OFFSET` and 
`LIMIT` expressions. This is because [this approach is incredibly wasteful](https://explainextended.com/2009/10/23/mysql-order-by-limit-performance-late-row-lookups/) 
at the database level. If we issed a query such as `SELECT name FROM users OFFSET 10000 LIMIT 1000` we are basically 
telling the database to fetch 11000 records and throw the first 10000 away. By avoiding this approach and only executing
a single query in the database we are being more efficient with our computing resources.

## PHP's MySQL memory management

Our solution above *looks* perfectly OK. We execute a single query and only ever load one row into memory. However, 
there is a problem with the way PHP handles MySQL query results **by default**. When we execute that single query  PHP 
tells MySQL: "Hey, give me this entire query result and I will store it in my PHP memory and you can forget about it", 
and it does this even though we haven't asked for anything to be loaded into memory other than a single row. Even 
though PHP does this for very good reasons (to allow us to do result counts in memory etc.), it is completely the 
opposite of what we want when dealing with result sets of this magnitude. 

> "Queries are using the buffered mode by default. This means that query results are immediately transferred from the 
> MySQL Server to PHP and then are kept in the memory of the PHP process."
> <cite><http://php.net/manual/en/mysqlinfo.concepts.buffering.php></cite>

This concept is called query buffering, and luckily there is a way to turn if off in situations like this. If we turn 
off query buffering we end up with an unbuffered query result which is held on our database server and  returned to us 
row-by-row or however we want to fetch it in our PHP process. 

> "Unbuffered MySQL queries execute the query and then return a resource while the data is still waiting on the MySQL 
> server for being fetched. This uses less memory on the PHP-side, but can increase the load on the server." 
> <cite><http://php.net/manual/en/mysqlinfo.concepts.buffering.php></cite>

This is exactly what we want when dealing with a huge result set: a small, predictable memory footprint in our PHP 
process (which is usually running inside a resource restricted boundary like a docker container). So how do we get
unbuffered query results in Magento? Let's take a look...

## Creating an unbuffered query connection

If we have a look at the [official PHP documentation on query buffering](http://php.net/manual/en/mysqlinfo.concepts.buffering.php)
we can see that there is an attribute that can be set on the `PDO` object after connecting:

<script src="https://gist.github.com/jameshalsall/d75869bea0275c3d8f9320d8d4150e3c.js"></script>

In Magento we don't readily have access to the underlying `PDO` object so we must use an alternative approach. Instead 
we must create a new instance of `Magento\Framework\DB\Adapter\AdapterInterface` (which is the interface that Magento DB
connections implement) that has this attribute set on it. The easiest way to do this is to fetch the current connection
from the object manager and then re-create it so that it returns unbuffered queries when used. Let's have a look at an 
example of this factory:

<script src="https://gist.github.com/jameshalsall/fc999eb50cdb69b1e3bb94ff2e364ff1.js"></script>

There are a few things happening here, so let's break it down:

1. We inject `ResourceConnection` into our factory that gives us access to existing connections
2. In our `factory()` method we take a connection name and fetch the actual connection (if no name is provided then the 
default connection is returned)
3. We modify the configuration data for the connection to set `use_buffered_query` to `false`
4. We return a new instance of the same connection type, with this new configuration
5. When the new connection is used it will return unbuffered query results

So now, if we want to fetch an unbuffered query result in our example from the start of this article, it would look 
something like this:

<script src="https://gist.github.com/jameshalsall/ce35a951be9fa54af06322c749da7f2d.js"></script>

Notice how we are now no longer using `ResourceConnection` in our result iterator, instead we use our new factory that
fetches unbuffered query results for us. One important thing to note here is that when fetching unbuffered query results
no other queries can be issued over the same connection until the entire result has been fetched. This means that if we
wanted to issue, for example, an `UPDATE` or `INSERT` query for each row in our result then we would need to fetch a 
normal connection from `ResourceConnection` and issue these queries over that instead.