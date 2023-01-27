# Time-To-Live(TTL) index

This works like a session. Clear data after some duration. Self-destroying data

```cpp
> db.sessions.insertOne({data: 'Sample data', createdAt: new Date()})
**Output**
{
	"acknowledged" : true,
	"insertedId" : ObjectId("632f15e43bb3e298033546d0")
}

> db.sessions.find().pretty()
**Output**
{
	"_id" : ObjectId("632f15e43bb3e298033546d0"),
	"data" : "Sample data",
	"createdAt" : ISODate("2022-09-24T14:36:20.317Z")
}
```

Now add time to live index, can create with normal ascending text

```cpp
> db.sessions.createdIndex({createdAt: 1})
> db.sessions.dropIndex({createdAt: 1})
```

Add indexes differently. After 10 seconds inserted the document will be destroyed.

```cpp
> db.sessions.createIndex({createdAt: 1}, {expireAfterSeconds: 10})
```