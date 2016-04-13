## Redis Notes
###### Jen Diamond

##### [Redis is a data structure server. It is open-source, networked, in-memory, and stores keys with optional durability. The name Redis means REmote DIctionary Server.](https://en.wikipedia.org/wiki/Redis)

#### Data types
**Redis maps keys to types of values.**  

A key difference between Redis and other structured storage systems is that  
**Redis supports not only strings, but also abstract data types:**
+ **Lists of strings**
+ **Sets of strings**
Collections of non-repeating unsorted elements
+ **Sorted sets of strings**
Collections of non-repeating elements ordered by a floating-point number called score
+ **Hash tables**
Where keys and values are strings
+ **HyperLogLogs**
Used for approximated set cardinality size estimation.

===

The type of a value determines what commands are available for the value itself. 

Redis supports high-level, atomic, server-side operations like 
+ intersection
+ union
+ difference between sets
+ sorting of lists, sets and sorted sets

---

#### Persistence
**Redis typically holds the whole dataset in memory.**  

##### Persistence is reached in two different ways:   
###### Snapshotting
A semi-persistent durability mode where the dataset is asynchronously transferred from 
memory to disk from time to time, written in RDB dump format.  

===

###### AOF
An append-only file that is written as operations modifying the dataset in memory are processed.  
Redis is able to rewrite the append-only file in the background in order to avoid an indefinite 
growth of the journal.

By default, Redis syncs data to the disk at least every 2 seconds,  
with more or less robust options available if needed. 

**In the case of a complete system failure on default settings, only a few seconds of data would be lost.**

---

+ http://squarism.com/2013/05/04/using-redis-as-a-database/
+ http://railscasts.com/episodes/193-tableless-model?autoplay=true
+ http://jimneath.org/2011/03/24/using-redis-with-ruby-on-rails.html
+ http://jimneath.org/2011/03/24/using-redis-with-ruby-on-rails.html#example_uses_in_rails
+ http://billpatrianakos.me/blog/2013/10/14/api-sessions-with-redis-in-rails/

---

#### [Up and Running with NoSQL Databases](http://www.lynda.com/Cassandra-tutorials/Exploring-NoSQL-possibilities/111598/117569-4.html)

When you should use NoSQL database instead of an SQL-based relational one. Let's take look at some examples of instances where NoSQL databases give you an advantage. Many NoSQL databases can be used to easily create web applications with customizable fields. You don't have to worry about schema changes and you can still index the fields.

You might have a relational database that you want to keep traffic away from. You can use NoSQL as a caching layer and write code to pull data from the relational database and store it in NoSQL, then you can use NoSQL for your API.

some NoSQL databases allow you to serve full web applications. Your HTML, stylesheets, and JavaScript can be served directly from NoSQL, then you can use the permissions in NoSQL to control who can read and write data. NoSQL databases can be used to build new applications from scratch as well as augment the capabilities of a relational database. Even if you're working with an existing application, you can start using NoSQL databases now to take advantage of their features.

Redis is a key-value store has built-in support for data structures like lists, sets, and hashes, and can persist data to disk. As such, it is quite useful as both a cache store and as a full-fledged, NoSQL data store.

Redis is not a plain key-value store, actually it is a data structures server, supporting different kind of values. What this means is that, while in traditional key-value stores you associated string keys to string values, in Redis the value is not limited to a simple string, but can also hold more complex data structures

http://www.sitepoint.com/introduction-to-using-redis-with-rails/

---

### Cache vs. Data

http://android.stackexchange.com/questions/108926/what-is-the-difference-between-application-cache-and-data

+ **Cache** is temporary files. One example might be thumbnails for contacts in a social media app. These can be cleared without any major effect — the app can just download them again when it needs to — and if space is low the Android OS may remove cache files itself.

+ **Data** is the "important" information. This might include credentials and settings that you generally want the app to remember.

---

### Install Redis
http://tutorials.jumpstartlab.com/topics/performance/installing_redis.html

---

### Redis commands

+ $ `sudo service redis-server stop`

+ $ `redis-server config/initializers/redis.rb`

+ $ `sudo service redis-server status`

+ $ `sudo service redis-server start`

+ $ `redis-cli`

+ $ `sudo service redis-server stop`

---

### Testing out your redis server

http://redis.io/topics/data-types-intro

> set mykey somevalue  
OK  
> get mykey  
"somevalue"

---

http://www.tutorialspoint.com/redis/redis_data_types.htm

redis 127.0.0.1:6379> SET name "tutorialspoint"  
OK  
redis 127.0.0.1:6379> GET name  
"tutorialspoint"  

In the above example **`SET`** and **`GET`** are redis commands,   *name* is the *key* used in redis   
and *tutorialspoint* is the *string value* that is stored in redis.

**NOTE:** A String value can be at max 512 Megabytes in length.

#### Hashes
A Redis hash is a collection of key value pairs. Redis Hashes are maps between string fields and string values, so they are used to represent objects

**Example**
redis 127.0.0.1:6379> HMSET user:1 username tutorialspoint   password tutorialspoint points 200  
OK  
redis 127.0.0.1:6379> HGETALL user:1

1) "username"  
2) "tutorialspoint"  
3) "password"  
4) "tutorialspoint"  
5) "points"  
6) "200"

In the above example hash data type is used to store user's object whichh contais basic information of user. Here HMSET, HGETALL are commands for redis while user:1 is the key.

**Every hash can store up to 232 - 1 field-value pairs (more than 4 billion).**

#### Lists
Redis Lists are simply lists of strings, sorted by insertion order. You can add elements to a Redis List on the head or on the tail.

**Example**
redis 127.0.0.1:6379> lpush tutoriallist redis  
(integer) 1  
redis 127.0.0.1:6379> lpush tutoriallist mongodb  
(integer) 2  
redis 127.0.0.1:6379> lpush tutoriallist rabitmq  
(integer) 3  
redis 127.0.0.1:6379> lrange tutoriallist 0 10

1) "rabitmq"  
2) "mongodb"  
3) "redis"

**The max length of a list is 232 - 1 elements (4294967295, more than 4 billion of elements per list).**

#### Sets
Redis Sets are an unordered collection of Strings.  
In redis you can **add**, **remove**, and **test for existence of members** in O(1) time complexity.

Example
redis 127.0.0.1:6379> sadd tutoriallist redis  
(integer) 1  
redis 127.0.0.1:6379> sadd tutoriallist mongodb  
(integer) 1  
redis 127.0.0.1:6379> sadd tutoriallist rabitmq  
(integer) 1  
redis 127.0.0.1:6379> sadd tutoriallist rabitmq  
(integer) 0  
redis 127.0.0.1:6379> smembers tutoriallist  

1) "rabitmq"  
2) "mongodb"  
3) "redis"

**NOTE:** In the above example rabitmq is added twice but due to unique property of set it is added only once.

---

### Thinking about Redis

https://www.quora.com/What-are-5-mistakes-to-avoid-when-using-Redis

#### Here are a few things I'd suggest thinking about when you are considering using Redis by Jim Dennis


+ Choose consistent ways to name and prefix your keys.  Manage your namespace.
+ Create a "registry" of key prefixes which maps each to your internal (perhaps wiki) documents for those application which "own" them.
+ For every class of data you put into your Redis infrastructure: design, implement and test the mechanisms for garbage collection and/or data migration to archival storage.
+ Design, implement and test a sharding (consistent hashing) library before you've invested much into your application deployment and ensure that you keep a registry of "shards" replicated on each server.
+ Isolate all your K/V store and related operations into a your own library/API or service and absolutely enforce the use of that API/service with an unrelenting and mercilessly iron hand

### Let me explain each of these points in turn:

You should assume, from the outset, that your Redis infrastructure will be a common resource used by a number of applications or separate modules.  You can have multiple databases on each server (numbered 0 through 31 by default, though you can increase the number of these).  However, it's best to assume that you'll need to use key prefixes to avoid collisions among various different application/modules.


#### Consistent key prefixing: Manage your namespace:

Your applications/modules should provide the flexibility to change these key prefixes dynamically.  Be sure that all keys are synthesized from the application/module prefix concatenated with the key that you're manipulating; make hard-coding of key strings verboten.


#### Registry: Document and Track your namespace:

I suggest that you have certain key patterns (prefixes or glob patterns) as "reserved" on your Redis servers.  For example you can have __key_registry__ (similar to the Python reserved method/attribute names) as a hash of key prefixes to URLs into your wiki or Trac or whatever internal documentation site you use.  Thus you can perform housekeeping on your database contents and track down who/what is responsible for every key you find in any database.  (Institute a policy that any key which doesn't match any pattern in your registry can/will be summarily removed by your automated housekeeping).


#### Garbage Collection:

In a persistent, shared, key/value store, and in the case of Redis, in particular (where the full key/value store must fit in RAM at all times --- VM/swap storage is deprecated) the collection of garbage is probably the single major maintenance issue.

So you need to consider how you're going to select the data that needs to be migrated out of Redis (perhaps into your SQL/RDBMS or into some other form of archival storage), and how you're going to track and purge data which is out-of-date or useless.

The obvious approaches involve the use of the EXPIRE or EXPIREAT features/commands.  This allows Redis to manage the garbage collection for you, either relative to your manipulation (creation or update) of any given key, or in terms of an absolute (seconds since the epoch) time specification.  The only trick about Redis expiration is that you must re-set it every each update to a key.

This approach is fine for situations where it applies naturally.  However, it's not useful if you want to cache data/keys indefinitely and only clean entries. For that I'd recommend you use a "ZSET" --- a set with values and "scores" which can be queried by ranges of those scores ... usually used for ordered/sorted collections.  In my model you'd add items to the set, keys, and their associated timestamps (when you've added or updated the corresponding key/value pairs to the database itself.  Then you can query that ZSET with something like: ZRANGEBYSCORE __EXPIRY__ 0 $SOME_EPOCH_TIME to get the subset of these which were added/updated more than a specific time ago.  You can iterate over this results removing the corresponding key/value pairs until you've freed up enough space on the server to meet your needs (or accomplished other garbage collection requirement you'd set for yourself).

Another useful approach is to maintain one or more queues (Redis lists, manipulated by the RPUSH/LPUSH and RPOP/LPOP commands, their B* blocking cousins, or the rather tricky BRPOPLPUSH/RPOPLPUSH commands) which you use for persisting things to more durable bulk storage (such as your SQL RDBMS).  Basically this functions as a work queue listing the keys (and perhaps some form of identifier regarding the canonical data store) that have been changed in Redis and which, therefore, need to be replicated out to your bulk storage.  You can monitor the length of such queues to ensure that you're not experiencing an ever growing backlog.

#### Sharding:

Redis doesn't provide sharding.  You should probably assume that you'll grow beyond the capacity of a single Redis server (slaves are for redundancy, not for scaling, though you can offload some read-only operations to slaves if you have some way to manage the data consistency --- for example the ZSET of key/timestamp values describe for expiry can also be used for some offline bulk processing operations; also the pub/sub features can be used for the master to provide hints regarding the quiescence of selected keys/data.

So you should consider writing your own abstraction layer to provide sharding.  Basically imagine that you have implemented a consistent hashing method ... you run every synthesized key through that before you use it.  While you only have a single Redis server then the hash to server mapping always ends up pointing to your only server.  Later if you need to add more servers then you can adjust the mapping so that half or a third of your keys resolve to your other  servers.  Of course you'll want to implement this so that the failure on a primary server causes your library/service module to automatically retry on the secondary and possibly any tertiary server.   Depending on your application you might even have the tertiary attempts fetch certain types of data from an another data source entirely (your SQL RDBMS, for example).

This is one area, in particular, where I'd like to see a robust third party (or antirez created) library written to handle all the fussy bits.  Consistent hashing isn't difficult ... but it does require quite a bit of carefully written code to implement --- and it's something that should be kept well away from most of your applications coders.  You want something that just works and continues to work even as you add additional server capacity (requiring migration utilities for  all the keys which have been affected) and failures (requiring that you have already handled replication of data to secondary and/or tertiary servers).

#### Decouple your Redis Operations from your Application:

I hope you've seen foreshadowing of just how important this suggestion is to every other suggestion I've made.  None of this will work if you have people just using your Redis infrastructure as an ad hoc, bottomless resource.

**Don't think of your application as using Redis!**

You are writing an application which uses a set of services.  It just so happens that (some of) those services are provided by Redis servers.  However, you should treat that as an implementation detail and focus on what services you need and what API you are providing to your application developers to provide them.

There is nothing that Redis offers that you can't accomplish through numerous alternatives.  Every feature of Redis can be approximated or provided, to some degree, using an SQL/RDBMS backend if you write the correct sorts of code (possibly including stored procedures, triggers, etc and/or separate daemons) and implement the correct schema to do it.

If you focus on the API that you need to provide to your applications developers than you have some hope of replacing those parts which, inevitably, need to be replaced later (for whatever reason).  You also provide considerably more flexibility as you develop additional applications and modules that need to interact with your existing databases.

---

#### [A thread is sequence of instructions that can be scheduled and executed in the context of a process. Several threads can be running at the same time.](http://lucaguidi.com/2014/03/27/thread-safety-with-ruby.html)

Ruby’s VM process allocates a memory heap, which is shared and writable by threads. If incorrectly coordinated, those threads can lead to unexpected behaviors.

Local variables are exempt from these problems, because they don’t hold a state.

From this picture, we can infer that stateless programs are always thread safe.

===

**Atomic operation** - An operation during which a processor can simultaneously read a location and write it in the same bus operation. This prevents any other processor or I/O device from writing or reading memory until the operation is complete.  
Atomic implies indivisibility and irreducibility, so an atomic operation must be performed entirely or not performed at all.

Modern concurrency tools for Ruby.  
https://github.com/ruby-concurrency/concurrent-ruby

Mutex and ConditionVariable  
Also check out the Monitor and MonitorMixin classes that are part of the Ruby standard libraries

---

### [Trends in Ruby](https://infinum.co/the-capsized-eight/articles/analyzing-rubygems-stats-v2015)

##### Details about getting the data
The database infrastructure of rubygems.org consists of a Postgres database and a Redis store - Postgres holds data about gems, gem versions and users 

while Redis is in charge of holding all the information about daily and total downloads per gem and per gem version.

---

### [pub/sub pattern](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)

**publish–subscribe** is a messaging pattern where senders of messages, called publishers, 
do not program the messages to be sent directly to specific receivers, called subscribers, 

but instead characterize published messages into classes without knowledge of which subscribers, if any, there may be. 

Similarly, subscribers express interest in one or more classes and only receive messages that are of interest, without knowledge of which publishers, if any, there are.

Pub/sub is a sibling of the message queue paradigm, and is typically one part of a larger message-oriented middleware system. Most messaging systems support both the pub/sub and message queue models in their API.

![pub/sub diagram](https://i-msdn.sec.s-msft.com/dynimg/IC141963.gif)

