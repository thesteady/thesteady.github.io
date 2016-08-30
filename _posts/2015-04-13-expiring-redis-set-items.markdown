---
layout: post
title:  "How to Create and Expire Set Items in Redis"
summary: "Redis can be a great way to keep in-memory lists. However, what happens when you need to be able to quickly search that list and be able to expire individual items after a given amount of time? None of the redis classes allow you to do this out of the box, but it can be done with sets. I'll show you how."
date:   2015-04-13 18:52:35 -0600
categories: tech_blog
---

_This post originally appeared on the [Quick Left blog](https://quickleft.com/blog/how-to-create-an…t-items-in-redis/) under a slightly different title._

#### [Featured in Redis Labs](https://redislabs.com/redis-watch-archive/41)

Redis can be a great way to keep in-memory lists. However, what happens when you need to be able to quickly search that list **and** be able to expire individual items after a given amount of time? None of the redis classes allow you to do this out of the box, but it _can_ be done. I'll show you how.

## The Problem:

Recently, I built an application that processed user activity data from a sepearate application. I needed to know if I had already processed an activity (each had a unique ID), but I might _receive_ the same activity multiple times. Because I would be processing a high volume, I wanted to remove activities after 60 days.

I found two different strategies briefly explained in [this Google Groups discussion](https://groups.google.com/forum/#!topic/redis-db/rXXMCLNkNSs), but no specifics as to how I might go about implementing them. Below you'll find what I consider to be the pros and cons of two different options, as well as a walkthrough of how to implement each of them in Node.js using [node_redis](https://github.com/mranney/node_redis).

Not familiar with redis data types? Check out the [intro to data types on the redis website](http://redis.io/topics/data-types-intro). Here's a quick overview of sets and sorted sets, which you'll want to understand before we solve our problem:

Redis [sets](http://redis.io/commands#set) are "unordered collections of strings", where items:

*   are unique (redis will silently refuse to add duplicates)
*   are not sorted or ordered in any way

Redis [sorted sets](http://redis.io/commands#sorted_set) allow you to create an _ordered_ collection of strings, where items:

*   as in sets, are unique
*   have a score (which does _not_ have to be unique)
*   are ranked (indexed)
*   cannot have a time-to-live (TTL) set on them. You _can_, however, set a TTL on the key for the entire sorted set.

## An Overview of Two Solutions

How do we solve the problem of expiring items in a redis list? Let's explore how you might implement two solutions.

### Option 1: Sorted Set

Querying sorted sets can be really fast because redis essentially creates a copy of the list as a set, so that when we query the sorted set, it has _already_ been sorted for us. According to the redis documentation:

> "Sorted sets are implemented via a dual-ported data structure containing both a skip list and a hash table, so every time we add an element Redis performs an O(log(N)) operation. That's good, but when we ask for sorted elements Redis does not have to do any work at all, it's already all sorted"

As mentioned above, you can't set a TTL on an individual item in a redis sorted set, which means you can't automatically expire individual items. You can query sorted sets by their `scores`, which adds an extra dimension that we can take advantage of in our solution. For each item that we add to the sorted set, we'll set the score to be a UNIX timestamp that represents the item's "expiration date". We then need to create a background/cron job to query the sorted set and remove those items that have expired. This process should query the sorted set based on score.

Adding an item to the sorted set might look something like this:

    var Q = require('q');
    var moment = require('moment');

    // redisClient is your node-redis client. In our case, it returns
    // a promise that resolves when the redis connection is ready.
    var redisClient = require('../redisClient');

    addActivity: function(itemId) {
      var self = this;
      return redisClient.then(function(client) {
        var ttlDate = this._flooredDate(timestamp).add(60, 'days').unix();
        return Q.npost(client, 'zadd', ['activities', ttlDate, itemId]);
      });
    },

    _flooredDate: function(timestamp) {
      return moment(timestamp).utc().startOf('day');
    }

How does the background job delete our "expired" items? Essentially, the background job would grab every item with a score older( smaller) than or equal to the current date. We can do this with the [ZREMRANGEBYSCORE method](http://redis.io/commands/zremrangebyscore) that redis provides. Here's how I might do it:

    expireItems: function() {
      var unixNow = moment().unix();

      return redisClient.then(function(client) {
        return Q.ninvoke(client, 'zremrangebyscore', 0, unixNow)
      });
    }

### Option 2: Multiple sets with TTL set

This solution uses normal sets. You can think of a set as a list of values. While you can't set TTLs on individual items, you _can_ set a TTL on the **key** for the whole set. For each timeblock you're working with (I'll use a day in this example), you'll want to create a new set, and set its TTL.

How do you do this? When you are processing a new item to add to your list, you'll want to generate a UNIX timestamp for the day "bucket" you're going to store it in. This could be generated from the current date or from a date associated with the data you're processing. If you haven't created this bucket, "adding" an item with this set name will create the new set. If the set already exists, your new item will be added. My strategy for setting the TTL on this bucket was to calculate the number of seconds from the _beginning_ of this bucket (the end would work as well), and set the TTL dynamically in this way.

    addActivity: function(itemId, timestamp) {
      var setName = activity + "-" + this._flooredDate(timestamp).unix();
      var ttl = this._flooredDate(timestamp).add(60, 'days').unix();

      return redisHelper.sAdd(setName, itemId)
        .then(function() {
          return redisHelper.expire(setName, ttl);
        });
    },

    _flooredDate: function(timestamp) {
      return moment(timestamp).utc().startOf('day');
    }
    .............................
    //redis helper methods that we abstracted for easier re-use:
    var redisHelper = {
      sAdd: function(setName, itemId) {
        return redisClient.then(function(client) {
          return Q.ninvoke(client, 'sadd', setName, itemId);
        });
      },

      expire: function(key, timeToLive) {
        return redisClient.then(function(client) {
          return Q.ninvoke(client, 'expire', key, timeToLive);
        });
      }
    };

When we set the `expire` on the set, redis automatically takes care of the cleanup for us.

### Pros and Cons

These two strategies each have their advantages and disadvantages. Depending on your particular situation, some things may matter more than others. As with any implementation choice, you'll want to think about what your decision means to your current and future system. In our decision-making process, these are the pros and cons that I identified.

The biggest issue I see with the sorted set strategy (_Option #1_) is that it requires external tooling to remove old items. You'll incur the added cost of building and maintaining a background job to clear out the expired items. This process will require additional queries to the redis database. While these can be "cheap" and usually quick, it's still something to consider. The biggest advantage that I see with this approach is that there's only one set to work with. This makes it easier to add, delete, and query items. Another potential advantage is that its also more flexible if the date associated with your data might be changing (more on this, below). If your system **needs** this ability, Option #2 won't work. (N.B. It _could_ work if you knew how the date was changing and were willing to query multiple sets).

An advantage of Option #2 is that there's no extra tooling required to remove expired items. Setting the TTL on the set key will auto-magically remove the expired data for you! When querying your sets, the data you'll be searching over could be much smaller. In my case, I needed to check every incoming foreign key to see if I'd already processed it. Since I knew the date associated with it, I was querying over a much smaller list. While this is probably a minor bonus (since Redis queries are typically very fast), I think it's worth noting. A final caveat with Option #2: beware timezones. Because you need add each item to a set associated with a particular time block, be careful to convert everything to the same timezone (e.g., UTC).

## Summary

Here's a quick summary of what we've just discussed:

#### Option 1: Sorted Set

*   Easy to add, delete, and query items
*   Fast ( `O(log(N))` )
*   Everything in one place means you can find things regardless of associated timestamps
*   One sorted set could has potential to become very large, depending on how much data you're processing per timeblock
*   You **must** create a cron job to delete expired items using their score

#### Option 2: Multi-set

*   If your records might have changing, associated timestamps, this option probably won't work for you
*   This limitiation could make it tricky to adjust down the road if you _do_ want to account for editing
*   Searches over a much smaller set, so theoretically faster with less bloat
*   Auto-deletion occurs through redis TTL, so no external tooling needed

## What Did I Choose?

For my project, I was already creating quite a few background and recurring processes, so creating and managing yet another one was not appealing. I also knew that my items' dates would NOT change, so I didn't need to worry about that. This made Option #2, using multiple sorted sets, the right choice for me.