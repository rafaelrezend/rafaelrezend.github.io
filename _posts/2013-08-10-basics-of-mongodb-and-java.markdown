---
layout: post
title: Basics of MongoDB and Java
---

In the growing trend of NoSQL databases, MongoDB has been getting [a lot of attention (and money)](http://gigaom.com/2013/10/04/mongodb-proves-its-king-of-nosql-databases-with-150m-in-new-funding/) among the current over 150 existing solutions.

NoSQL is a schema-free database concept that promises to solve one of the biggest issues of the modern web development: scalability.
It shouldn't be seen, however, as a generic solution for every existing problem.

In fact, NoSQL is a much wider concept than the known and mature SQL databases (composed of tables, columns, with indexes, foreign keys and so on), with at least five different categories listed so far according to the mechanism used to store the information: key-values pairs, graphs, documents, columns and even relational (the community refers to *NoSQL* nowadays as *"not only SQL"*).
Anyway, every category focuses on different requirements.
This way, a careful evaluation of the problem you are trying to solve, followed by a deep research on available options can be really worthy.

There are plenty of material about NoSQL available on internet, and no single post would be capable to nail your decision alone.
One old reference, however, has a very interesting picture to start with: [A Visual Guide to NoSQL systems](http://blog.nahurst.com/visual-guide-to-nosql-systems).

So, let's implement a simple example in Java showing the basic capabilities of MongoDB.
The only requirements for this example are a [MongoDB installation](http://www.mongodb.org/downloads) and the respective [Java MongoDB driver](http://docs.mongodb.org/ecosystem/drivers/java/).

```java
// Connecting to MongoDB
MongoClient mongo = new MongoClient("localhost", 27017);

// Get a database (automatically create if it does not exist)
DB db = mongo.getDB("sandbox");

// Get a collection from sandbox
// Collection is automatically created if it does not exist
DBCollection collection = db.getCollection("alarm");
```

Since databases are schema-free, they can be seen merely as folders, easy to create on the fly as shown above.
*Collections* are equivalent to tables in relational databases and are composed of *documents*.
Every *document* corresponds to a row in a table, with the main difference that the first ones don't have a strict set of fields.

To create and insert a new document, a `BasicDBObject()` is instantiate and populate with key-value pairs:

```java
// Create objects with key and value
BasicDBObject alarm1 = new BasicDBObject();
alarm1.put("_id", 0);
alarm1.put("owner", 1);
alarm1.put("priv", false);
alarm1.put("instant", new Date(0));

BasicDBObject alarm2 = new BasicDBObject();
alarm2.put("_id", 1);
alarm2.put("owner", 1);
alarm2.put("priv", true);
alarm2.put("instant", new Date(0));

// Insert object into the collection
collection.insert(alarm1, alarm2);
```

By default, the index of documents is given by `_id`.
If no key `_id` is explicitly added, MongoDB will generate it automatically.

BasicDBObject is also used as reference for searching objects in the database. The query will match the objects that have the same key-value pairs as the reference one.
The code below will retrieve the first alarm `alarm1` in a `DBCursor`:

```java
BasicDBObject alarmSearch = new BasicDBObject("_id", 0);

// To perform the search for a specific object
DBCursor cursor = collection.find(alarmSearch);
while (cursor.hasNext()) {
	System.out.println(cursor.next());
}
```

Updating a document requires an additional operator `$set` to replace a value, or `$inc` to increment it. So, for instance, the operation below completely overwrites the attribute *owner*...

```java
BasicDBObject oldAlarm = new BasicDBObject("_id", 1);
BasicDBObject updatedAlarm = new BasicDBObject("owner", 999);
BasicDBObject updaterAlarm = new BasicDBObject("$set", updatedAlarm);
collection.update(oldAlarm, updaterAlarm);
```

... while the next one increments the value of the same attribute by one:

```java
oldAlarm = new BasicDBObject("_id", 1);
updatedAlarm = new BasicDBObject("owner", 1);
updaterAlarm = new BasicDBObject("$inc", updatedAlarm);
collection.update(oldAlarm, updaterAlarm);
```

When the user tries to update the document without using the `$set` or `$inc` operator, as in `collection.update(oldAlarm, updatedAlarm)`, the entire document is replaced.
This is a very common mistake when starting with MongoDB.

Also, MongoDB does not allow indexes to be changed. Any attempt will raise a `WriteConcernException`.

Using the same `BasicDBObject oldAlarm`, the document with `_id = 1` can be removed as seen below:

```java
collection.remove(oldAlarm);
```

The complete implementation is available on my [repository](https://github.com/rafaelrezend/MongoDBSandbox).
