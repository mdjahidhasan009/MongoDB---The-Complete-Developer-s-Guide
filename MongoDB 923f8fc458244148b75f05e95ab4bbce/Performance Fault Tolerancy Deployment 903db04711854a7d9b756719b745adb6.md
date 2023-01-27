# Performance Fault Tolerancy Deployment

### What influences Performance

**Developer/ DB Admin**

1. Efficient Queries/Operations
2. Indexes
3. Fitting Data Schema

**DB Admin/ System Admin**

1. Hardware & Network
2. Sharding
3. Replica Sets

### Understanding Capped Collections

<aside>
💡 **Capped collections** are a special type of collection that have to create explicitly where limit the amount of data and old data is automatically deleted when uploads new data if the memory or data limit is exceeded.

</aside>

- The oldest data is automatically deleted when new data comes in.
- This is efficient for application logs where the most recent logs are stored, or as caching some data.

> For capped collection, it returns documents as the same order as it was inserted. But for normal collection it is not always true.
> 

Let's create a collection with capped, `max` is optional

> Default `size` 4 MB, `max` is the number of documents that can be stored there.
> 

```cpp
> use performance
switched to db performance

> db.createCollection('capped', {capped: true, size: 10000, max: 3})
**Output**
{ ok: 1 }
```

Access capped collections

```cpp
> db.capped.insertOne({name: 'Max'})
**Output**
{
  acknowledged: true,
  insertedId: ObjectId("6353cc9a88121c3743e45cef")
}

> db.capped.insertOne({name: 'Manu'})
**Output**
{
  acknowledged: true,
  insertedId: ObjectId("6353ccb588121c3743e45cf0")
}

db.capped.insertOne({name: 'Anna'})
**Output**
{
  acknowledged: true,
  insertedId: ObjectId("6353cccc88121c3743e45cf1")
}
```

```cpp
> db.capped.find().pretty()

**Output**
[
  { _id: ObjectId("6353cc9a88121c3743e45cef"), name: 'Max' },
  { _id: ObjectId("6353ccb588121c3743e45cf0"), name: 'Manu' },
  { _id: ObjectId("6353cccc88121c3743e45cf1"), name: 'Anna' }
]
```

If any time need to order the documents to descending order

```cpp
> db.capped.find().sort({$natural: -1}).pretty()
**Output**
[
  { _id: ObjectId("6353cccc88121c3743e45cf1"), name: 'Anna' },
  { _id: ObjectId("6353ccb588121c3743e45cf0"), name: 'Manu' },
  { _id: ObjectId("6353cc9a88121c3743e45cef"), name: 'Max' }
]
```

Now if we insert an new document then the first document will automatically get deleted(name: 'Max') and will not show error msg.

```cpp
> db.capped.insertOne({name: 'Maria'})

**Output**
{
  acknowledged: true,
  insertedId: ObjectId("6353fac444b95bf60083ef55")
}

> db.capped.find().pretty()

**Output**
[
  { _id: ObjectId("6353ccb588121c3743e45cf0"), name: 'Manu' },
  { _id: ObjectId("6353cccc88121c3743e45cf1"), name: 'Anna' },
  { _id: ObjectId("6353fac444b95bf60083ef55"), name: 'Maria' }
]
```

But the data is nor permanently deleted, If by using Cache the can retrieve the full data

### Replica Sets

Client(shell , Driver) <--Read-->  MongoDB Server Primary Node --> Replica Set(Primary Node --> 1. Secondary Node 2. Secondary Node

![Screenshot from 2022-10-22 22-45-07.png](Performance%20Fault%20Tolerancy%20Deployment%20903db04711854a7d9b756719b745adb6/Screenshot_from_2022-10-22_22-45-07.png)

> If we add replica node then if we make a write request then it will be go to the Client then MongoDB server then primary node where we have the database. And if we have secondary node MongoDB asynchronously(not instantly) will replicate(copy) to in those secondary node. If primary node goes offline then data read from replica (Secondary Node).
> 

Why Replica Sets?

1. Backup / Fault Tolerance 
2. Improve Read Performance

To read data as first as possible can over distribute read request between the primary and secondary Node. If there are huge amount of read request then this will help to reduce the pressure at primary node.

### Sharding (Horizontal Scaling)

More memory into a single machine. Horizontal scaling which means need more servers.

```
           MongoDB Server
				        |
------------------------------------------------------
server1	  server2  server3   server4  server5  server6

```

That issue here of course is this might sound logical but severs now don't duplicate that data, they are not backups, they split that data. So server 1 on the left, let's say stores data for the same application as the other server but a different chunk of it. So with sharding, we have multiple computers who all run MongoDB servers but these servers don’t work alone but work together and split the available data so that data is distributed across shards not replicated. So queries where find data but also insert update and delete operations therefore have to be run against all the servers or the right servers because each chunk manages its data and its range of the data.

## Data is distributed (not replicated) across Shards

Each chunk manages its data and its a range of data

## Queries are run across all Shards

Data can be stored as A-J, J-K, K-S.. to the sequence of a chunk(server).

### How Sharding Works

```
----------
								| Client |
								----------
									|
							------------------				 
							| mongos(Router) |
							------------------		
									|
 -------------------------------------------------------------------------
        |                           |                         |
 MongoDB(Server/Shard)  	MongoDB(Server/Shard)     MongoDB(Server/Shard)		    
 --------------------------------------------------------------------------
     | Shard Key  |            | Shard Key  |             | Shard Key  | 
 ---------------------------------------------------------------------------
 // shard key: shard key is essential just a field that's added to every document which kid of is important. 

 ------------------------Queries and Sharding-------------------------

                             	----------
								| find() |
								----------
									 |
							 ------------------				 
						 	 | mongos(Router) |
							 ------------------
                                     |
		    -------------------------------------------------------
	        |                                                     | 
 ---------------------------			            ---------------------------			 
 | Option1: Operation does |                        | Option2: Operation does |
 | not contain Shared Key  |                        | contain Shared Key      |
 ---------------------------                        ---------------------------
    |        |       |                          Directly Send   ---> |
BroadCast >	 |       |                          to right Shard       |
    |        |       |                                               |
--------------------------                               --------------------
shared    shared    shared                                         shared
```

![Screenshot from 2022-10-22 23-09-18.png](Performance%20Fault%20Tolerancy%20Deployment%20903db04711854a7d9b756719b745adb6/Screenshot_from_2022-10-22_23-09-18.png)

![Screenshot from 2022-10-22 23-08-19.png](Performance%20Fault%20Tolerancy%20Deployment%20903db04711854a7d9b756719b745adb6/Screenshot_from_2022-10-22_23-08-19.png)

# Deploying a MongoDB Server

![Screenshot from 2022-10-22 23-10-57.png](Performance%20Fault%20Tolerancy%20Deployment%20903db04711854a7d9b756719b745adb6/Screenshot_from_2022-10-22_23-10-57.png)

localhost(mongod) -------> Atlas(mongod)

Have to manage

1. Manage Shards
2. Secure User / Auth Setup
3. Project Web Server / Network
4. Manage Replica Set
5. Encryption(Transportation & Rest)
6. Regular Backups
7. Update Software

# Performance & Fault Tolerancy

1. Consider Capped Collections for cases where want to clear old data automatically.
2. Performance is all about having efficient queries / operations, fitting data formats and a best-practice MongoDB server config.
3. Replica sets provide fault tolerancy (with automatic recovery) and improved read performance.
4. Sharding allows to scale MongoDB server horizontally.

# Deployment $ MongoDB Atlas

1. Deployment is a complex matter since it involves many tasks - some of them are not even directly related to MongoDB.
2. Unless are a experienced admin (or got one), should consider a managed solution like MongoDB Atlas.
3. Atlas is a managed service where can configure a MongoDB environment and pay at a by-usage basis.