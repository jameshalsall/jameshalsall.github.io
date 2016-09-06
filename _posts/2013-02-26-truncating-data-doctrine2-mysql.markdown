---
layout: post
title:  "Truncating tables via Doctrine 2.x in MySQL"
date:   2013-02-26 14:30:00 +0100
categories: engineering
---

I recently stumbled across a question on
[stackoverflow.com](http://stackoverflow.com/questions/8526534/how-to-truncate-a-table-using-doctrine/15024353) where
somebody was having problems with foreign key constraints and truncating data in MySQL. An accepted answer stated that
while it was possible to truncate the data, it isn't possible if there are foreign keys present (as is the case with 
Doctrine 2 entities).

Actually, it is possible to truncate your data even with foreign key constraints. It can be dangerous if you're not 
careful about what you delete, but the following code can be useful in Doctrine 2.x if you have a need for emptying 
tables programmatically:

<script src="https://gist.github.com/jameshalsall/4b230c0022c59e623f43.js"></script>

The raw query before and after the truncate statement tells MySQL to forget about foreign key constraints, so it just
blindly empties your table(s).

Hopefully this will save people some time.
