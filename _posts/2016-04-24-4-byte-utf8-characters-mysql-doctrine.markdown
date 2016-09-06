---
layout: post
title:  "4 byte UTF-8 characters with Doctrine and MySQL"
date:   2016-04-24 16:57:39 +0100
categories: engineering
---

I was recently working on a personal project where I needed to store UTF-8 characters such as emojis in a MySQL TEXT 
column type. I was surprised to find that MySQL's implementation of the UTF-8 character set isn't actually true UTF-8.
It is only capable of storing UTF-8 characters that
[fall within the 3 byte range](http://dev.mysql.com/doc/refman/5.7/en/charset-unicode-utf8.html), so that rules out my
emoji characters that I needed to store.

In version 5.5. MySQL introduced a new character set called 
[utf8mb4](https://dev.mysql.com/doc/refman/5.5/en/charset-unicode-utf8mb4.html) that supports 4 bytes per character, 
allowing the storage of emoji and other supplementary characters.

The obvious first step is to make sure your character set is set correctly on your table configuration in Doctrine:

<script src="https://gist.github.com/jameshalsall/cf4a96d2464260c88656feb2f9b0f007.js"></script>

I had to drop my schema and re-create it to have the new character set take effect. If this isn't an option for you then
you can apply the character set changes manually via SQL.

At this point I expected things to be resolved, but MySQL was still giving me problems with the following error:

> SQLSTATE[HY000]: General error: 1366 Incorrect string value: '\xF0\x9F\x99\x8C\xF0\x9F...' for column 'caption' at row 1

I verified the character set change had been applied on my table, which it had. After looking at my DBAL configuration
for Doctrine I realised that the charset option had to be specified as `UTF8MB4`. Lo and behold, I was now able to save
my 4 byte UTF-8 characters to the database.