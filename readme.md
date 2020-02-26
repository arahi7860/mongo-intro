[![General Assembly Logo](https://camo.githubusercontent.com/1a91b05b8f4d44b5bbfb83abac2b0996d8e26c92/687474703a2f2f692e696d6775722e636f6d2f6b6538555354712e706e67)](https://generalassemb.ly/education/web-development-immersive)

# Intro to MongoDB

![logo](./images/mongodb-logo.jpg)

The applications that we've built thus far have suffered from one major
shortcoming: when the user reloads the page, any data or progress they've made
is lost!

Right now, we can only store information in memory, which is wiped when we
restart our programs. We need some way to fix this.

Enter databases...

## Objectives

By the end of this, developers should be able to:

* Explain the use cases of a database, specifically non-relational databases
* Setup a local MongoDB server
* Define the key terms document and collection in the context of MongoDB

## Databases (10 minutes / 0:10)

There are many ways to store data on a computer (e.g., writing to a text file,
a binary file) but databases offer a number of advantages:

**Performance**: Once we write data to our database, we can be pretty sure it
won't be lost (unless the server catches on fire).

**Speed**: Databases are generally optimized to be fast at retrieving and
updating information. Literally, databases can be 100,000x faster than reading
from a file. This is especially important at scale.

**Consistency**: Databases can enforce rules regarding consistency of data,
especially when handling simultaneous requests to update information.

**Scalability**: Databases can handle lots of requests per second, and many
databases have ways to scale to massive page loads by replicating / syncing
information across multiple versions of a database.

**Querying**: databases make it easy to search, sort, filter and combine
related data using a **Query Language**.

## Types of Databases

There are a number of types of databases that generally fall into two
categories:

1. Relational
2. Non-relational

In a **relational** database, data is organized by columns and rows, much like
an Excel spreadsheet. If you've ever heard of SQL, it is the most popular
relational database ever.

Today, we are going to explore a **non-relational** databases called [MongoDB](https://www.mongodb.com/)

MongoDB is an open-source **document database** that provides:

* High performance
* High availability
* Automatic scaling

MongoDB is more effective when dealing with a high-volume of data with low
complexity and few associations. Those are the technical reasons why you might
use MongoDB over SQL.

For us, as budding developers learning to build full-stack applications, they
offer some additional advantages: MongoDB is generally pretty easy to use and
learn and simple to understand. As you'll see in a bit, with MongoDB, we're
taking JavaScript objects (in the form of JSON) and saving them to a database.

### Terminology

While this is a bit technical, it's worth clarifying some terminology...

* **Database**: The actual set of data being stored. We may create multiple databases on our computer, often one for each application.
* **Database Management System**: The software that lets a user interact (query) the data in a database. Examples are MongoDB, PostgreSQL, MySQL, etc.
* **Database CLI**: A tool offered by most DBMSs that allows us to interact with and query your database from the command line. For MongoDB, we'll use `mongo`. We'll be mostly working in the CLI today.

## Document Database (10 min / 0:20)

### A basic example of a `Person` document:

```json
{
  "name": "Sue",
  "age": 26,
  "status": "Active",
  "groups": ["sass", "express"]
}
```

What do you see in the data above?

### A Document

**A record in MongoDB is a called document.**

* A data structure composed of pairs or fields (keys) and values
  * Similar to JSON objects ([JavaScript Object Notation](https://www.mongodb.com/json-and-bson)
  * Stored as BSON [(binary-encoded JSON)](http://bsonspec.org/ )
* A document can support all data types - numbers, strings, booleans, even arrays and other documents (objects)
* Fields may include other documents and arrays of documents
* A document is analogous to a row in a table

[Documentation Here](https://docs.mongodb.com/manual/introduction/)

### More complicated example of a `Restaurant` document:

```json
{
   "_id" : ObjectId("54c955492b7c8eb21818bd09"),
   "address" : {
      "street" : "2 Avenue",
      "zipcode" : "10075",
      "building" : "1480",
      "coord" : [ -73.9557413, 40.7720266 ]
   },
   "borough" : "Manhattan",
   "cuisine" : "Italian",
   "grades" : [
      {
         "date" : ISODate("2014-10-01T00:00:00Z"),
         "grade" : "A",
         "score" : 11
      },
      {
         "date" : ISODate("2014-01-16T00:00:00Z"),
         "grade" : "B",
         "score" : 17
      }
   ],
   "name" : "Vella",
   "restaurant_id" : "41704620"
}
```

What do you see in the data above?

## Collections (5 min / 0:25)

MongoDB stores documents in collections.

* Collections are analogous to tables in relational databases
* Does **NOT** require its documents to have the same schema or structure
* Each document stored in a collection must have a unique `_id` field that acts as a primary key

Great, now that we have a high level understanding of what Mongo is and what
purpose it serves, let's look at how to use it!

## Installation / Starting (5 min / 0:30)

Check to make sure MongoDB is installed by running the following command in
a terminal window:

```sh
mongo --version
```

If you already have it installed you should see output like this...

```sh
$ mongo --version

MongoDB shell version v4.2.3
git version: 6874650b362138df74be53d366bbefc321ea32d4
allocator: system
modules: none
build environment:
    distarch: x86_64
    target_arch: x86_64
```

If you already have Mongo installed, skip to the **Mongo Shell** section.
Otherwise, follow the instructions below.

### Installation Instructions

**Proceed only if you don't have installed! Consult the previous section.**

If you already have Mongo installed, skip to the **Mongo Shell** section.

**Mac OS X:**

1. Add the custom tap in a MacOS terminal session using:

```bash
$ brew tap mongodb/brew
```

2. Install MongoDB Community Edition:

```bash
$ brew install mongodb-community@4.2
```

> [Linux Instructions on the mongodb website](https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/)

## Mongo shell (10 min / 0:40)

### Start Mongo:

In a new tab in Terminal, run the following:

```
$ brew services start mongodb-community@4.2
```

You should see the following output:

```bash
$ brew services start mongodb-community@4.2

==> Successfully started `mongodb-community` (label: homebrew.mxcl.mongodb-commu
```

> This is good news, the command just starts up a mongo server locally. **NOTE**:
> you need this running in order to use the mongo cli, but you can keep it running in the background.


### Start The Shell

Back in your original Terminal tab:

```
$ mongo
```

> Feels a little bit like a JavaScript REPL

You should see:

```
MongoDB shell version v4.2.3
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("b4207c16-134c-4474-99c6-875f37d9080d") }
MongoDB server version: 4.2.3
Server has startup warnings: 
2020-02-26T10:35:41.523-0500 I  CONTROL  [initandlisten] 
2020-02-26T10:35:41.523-0500 I  CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2020-02-26T10:35:41.523-0500 I  CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2020-02-26T10:35:41.523-0500 I  CONTROL  [initandlisten] 
---
Enable MongoDB's free cloud-based monitoring service, which will then receive and display
metrics about your deployment (disk utilization, CPU, operation statistics, etc).

The monitoring data will be available on a MongoDB website with a unique URL accessible to you
and anyone you share the URL with. MongoDB may use this information to make product
improvements and to suggest MongoDB products and deployment options to you.

To enable free monitoring, run the following command: db.enableFreeMonitoring()
To permanently disable this reminder, run the following command: db.disableFreeMonitoring()
---

>
```

> The `>` is a good sign that you've entered the Mongo shell.

### Help

Type `help` to get a list of available commands.

```
> help
```

#### Think-Pair-Share (2min)

Based on what you see in the help menu:

* What jumps out as important?
* What might be useful for debugging?

<details>
<summary>Some things that jump out:</summary>

* `db.help()` : help with database commands
* `show dbs`: show database names
* `show collections`:  show collections in current database
* `use <db_name>`: set current database
* `db.foo.find()`: list objects in collection foo

Also:

* `<tab>` key completion
* `<up-arrow>` and the `<down-arrow>` for history.
</details>

## Review: Mongo's Key Advantages (5 min / 0:45)

* Usability
* High Performance
* High Availability
* Automatic Scaling
* No SQL

### Usability

* Documents (i.e. objects) correspond to native data types in many programming languages.
* Schema-less: no need to manage migrations.

### High Performance

* Embedded documents and arrays reduce need for expensive joins (reduces I/O).
* Indexes support faster queries and can include keys from embedded documents and arrays. (More info on MongoDB indexing [here](https://dev.to/akazia_it/introduction-to-mongodb-indexing).)

### High Availability

MongoDB’s replication facility, called replica sets, provide:

* automatic failover.
* data redundancy.

replica set:

> is a group of MongoDB servers that maintain the same data set, providing
> redundancy and increasing data availability.

### Automatic Scaling

* Automatic sharding distributes data across a cluster of machines.
* Replica sets can provide eventually-consistent reads for low-latency high
throughput deployments.

> Interested in learning more about [No SQL?](https://www.mongodb.com/nosql-explained)

## Additional Resources

* [Mongo to SQL Mapping Chart](http://docs.mongodb.org/manual/reference/sql-comparison/)
* [bios Collection](http://docs.mongodb.org/manual/reference/bios-example-collection/)

## [License](LICENSE)

1. All content is licensed under a CC­BY­NC­SA 4.0 license.
1. All software code is licensed under GNU GPLv3. For commercial use or
    alternative licensing, please contact legal@ga.co.
