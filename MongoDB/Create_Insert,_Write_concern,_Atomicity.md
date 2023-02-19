# Create/Insert, Write concern, Atomicity

- [insertOne](Create_Insert,_Write_concern,_Atomicity.md)
- [insertMany](Create_Insert,_Write_concern,_Atomicity.md)
- [insert](Create_Insert,_Write_concern,_Atomicity.md)
- [Working with insert](Create_Insert,_Write_concern,_Atomicity.md)
    - [Ordered List](Create_Insert,_Write_concern,_Atomicity.md)
    - [Unordered List](Create_Insert,_Write_concern,_Atomicity.md)
- [Write Concern](Create_Insert,_Write_concern,_Atomicity.md)
- [Atomicity](Create_Insert,_Write_concern,_Atomicity.md)
- [Importing Data](Create_Insert,_Write_concern,_Atomicity.md)


> `insertOne()` ⇒ inserting one document. <br/>
> `insertManay()` ⇒ inserting many document.(Must have to put the third bracket if one insert one document using this although it only one document otherwise it will throw an error) <br/>
> `insert()` ⇒ insert also exist one many document. But it's not recommended to use it anymore - it also does not return the inserted id's

- We can define `_id` before inserting it in the MongoDB database but if we do not do this MongoDB will add an `_id` automatically.

# insertOne

<details>
  <summary>Selecting database</summary>

  ```scheme
    > use contactData
    **Output**
    switched to db contactData
  ```
</details>
    

```scheme
> db.persons.insertOne({name: "Max", age: 30, hobbies: ["Sports", "Cooking"]})
**Output**
{
	"acknowledged" : true,
	"insertedId" : ObjectId("61be13d29b62de0e3741e818")
}
> db.persons.insertOne({name: "Manuel", age:31, hobbies:["Cars", "Cooking"]})
**Output**
{
	"acknowledged" : true,
	"insertedId" : ObjectId("61be144a9677c2fa174a9886")
}
```

# insertMany

```scheme
> db.persons.insertMany([{name: "Anna", age: 29, hobbies: ["Sports", "Yoga"]}])
**Output**
{
	"acknowledged" : true,
	"insertedIds" : [
		ObjectId("61be157b9677c2fa174a9887")
	]
}

> db.persons.insertMany([{name:"Maria", age: 31}, {name: "Chris", age: 25}])
**Output**
{
	"acknowledged" : true,
	"insertedIds" : [
		ObjectId("61be15dd9677c2fa174a9888"),
		ObjectId("61be15dd9677c2fa174a9889")
	]
}
```

# insert

<details>
  <summary>Selecting user database</summary>

```scheme
> use user
switched to db user
```

</details>
    

```scheme
> db.persons.insert({name: 'Phil', age: 35})
**Output**
WriteResult({ "nInserted" : 1 })

> db.persons.find()
**Output**
{ "_id" : ObjectId("5f151e97e3242ab6a2f87b4e"), "name" : "Phil", "age" : 35 }

> db.persons.find().pretty()
**Output**
{
	"_id" : ObjectId("5f151e97e3242ab6a2f87b4e"),
	"name" : "Phil",
	"age" : 35
}

> db.persons.insert([{name: 'Khil', age: 45},{name: 'RAJU', age: 22}])
**Output**
BulkWriteResult({
	"writeErrors" : [ ],
	"writeConcernErrors" : [ ],
	"nInserted" : 2,
	"nUpserted" : 0,
	"nMatched" : 0,
	"nModified" : 0,
	"nRemoved" : 0,
	"upserted" : [ ]
})

> db.persons.find().pretty()
**Output**
{
	"_id" : ObjectId("5f151e97e3242ab6a2f87b4e"),
	"name" : "Phil",
	"age" : 35
}
{
	"_id" : ObjectId("5f15209ae3242ab6a2f87b4f"),
	"name" : "Khil",
	"age" : 45
}
{
	"_id" : ObjectId("5f15209ae3242ab6a2f87b50"),
	"name" : "RAJU",
	"age" : 22
}
```

# Working with order insert

- **By default**, when using **insertMany**(), inserts are **ordered**, which means, that the **inserting process stops if an error occurs**.
- Can change this by switching to '**unordered inserts**' - the inserting process will then **continue**, even **if errors occurred**.
- In both cases, no successful inserts (before the error) will be rolled back.

bulk process

## Ordered Insert

`true` (default) perform an ordered insert of the documents in the array, and if an error occurs with one of the documents, MongoDB will return without processing the remaining documents in the array.

```scheme
> db.hobbies.insertMany
(
	[
		{_id: 'sports', name: 'Sports'},
		{_id:'cooking',name:'Cooking'},
		{_id:'cars',name: 'Cars'}
	]
)
**Output**
{
	"acknowledged" : true,
	"insertedIds" : [
		"sports",
		"cooking",
		"cars"
	]
}

> db.hobbies.find().pretty()
**Output**
{ "_id" : "sports", "name" : "Sports" }
{ "_id" : "cooking", "name" : "Cooking" }
{ "_id" : "cars", "name" : "Cars" }
```

### Trying to inserting with duplicate _id collection

Inserting with duplicate _id(cooking) the second document. The collection with duplicate _id will not be inserted and also if there is any collection after this will not inserted. But the inserted collection will remain untouched.

```scheme
> db.hobbies.insertMany([{_id: 'yago', name: 'Yoga'},{_id:'cooking',name:'Cooking'}])
**Output**
2020-07-20T11:19:04.791+0600 E QUERY    [thread1] BulkWriteError: write error at item 1 in bulk operation :
BulkWriteError({
	"writeErrors" : [
		{
			"index" : 1,
			"code" : 11000,
			"errmsg" : "E11000 duplicate key error collection: user.hobbies index: _id_ dup key: { : \"cooking\" }",
			"op" : {
				"_id" : "cooking",
				"name" : "Cooking"
			}
		}
	],
	"writeConcernErrors" : [ ],
	"nInserted" : 1,
	"nUpserted" : 0,
	"nMatched" : 0,
	"nModified" : 0,
	"nRemoved" : 0,
	"upserted" : [ ]
})
BulkWriteError@src/mongo/shell/bulk_api.js:369:48
BulkWriteResult/this.toError@src/mongo/shell/bulk_api.js:333:24
Bulk/this.execute@src/mongo/shell/bulk_api.js:1177:1
DBCollection.prototype.insertMany@src/mongo/shell/crud_api.js:314:5
@(shell):1:1

// every element is inserted and standalone but if an error creates then exact element and after are is exited. 

> db.hobbies.find().pretty()
{ "_id" : "sports", "name" : "Sports" }
{ "_id" : "cooking", "name" : "Cooking" }
{ "_id" : "cars", "name" : "Cars" }
{ "_id" : "yago", "name" : "Yoga" }
```

Specifying ordered: true but no need for this as the default value is true.

```scheme
> db.hobbies.insertMany([{_id: 'yago', name: 'Yoga'},{_id:'cooking',name:'Cooking'},{_id:'hiking',name:'Hiking'}],{ordered: true})
2020-07-20T11:33:12.922+0600 E QUERY    [thread1] BulkWriteError: write error at item 0 in bulk operation :
BulkWriteError({
	"writeErrors" : [
		{
			"index" : 0,
			"code" : 11000,
			"errmsg" : "E11000 duplicate key error collection: user.hobbies index: _id_ dup key: { : \"yago\" }",
			"op" : {
				"_id" : "yago",
				"name" : "Yoga"
			}
		}
	],
	"writeConcernErrors" : [ ],
	"nInserted" : 0,
	"nUpserted" : 0,
	"nMatched" : 0,
	"nModified" : 0,
	"nRemoved" : 0,
	"upserted" : [ ]
})
BulkWriteError@src/mongo/shell/bulk_api.js:369:48
BulkWriteResult/this.toError@src/mongo/shell/bulk_api.js:333:24
Bulk/this.execute@src/mongo/shell/bulk_api.js:1177:1
DBCollection.prototype.insertMany@src/mongo/shell/crud_api.js:314:5
@(shell):1:1

// every element is inserted and standalone but if an error creates then exact element and after are is exited. 

> db.hobbies.find().pretty()
{ "_id" : "sports", "name" : "Sports" }
{ "_id" : "cooking", "name" : "Cooking" }
{ "_id" : "cars", "name" : "Cars" }
{ "_id" : "yago", "name" : "Yoga" }
```

# Unordered Insert

`false` perform an unordered insert, and if an error occurs with one of `the` documents, continue processing the remaining documents in the array.

yago and cooking _id already have in the database so those will throw an error but _id hiking does not exist yet so it will be inserted despite the first yago and then cooking, and cooking will create an error as they already exit in the database. (If it was ordered insert then after the first error of yago it would stop inserting).

<details>
  <summary>Database before insert</summary>

  ```schema
> db.hobbies.find().pretty()
**Output**
{ "_id" : "sports", "name" : "Sports" }
{ "_id" : "cooking", "name" : "Cooking" }
{ "_id" : "cars", "name" : "Cars" }
{ "_id" : "yago", "name" : "Yoga" }
  ```
</details>
    

```bash
> db.hobbies.insertMany(
	[
		{_id: 'yago', name: 'Yoga'},
		{_id:'cooking',name:'Cooking'},
    {_id:'hiking',name:'Hiking'}
  ],
  {ordered: false}
)
**Output**
2020-07-20T11:33:46.532+0600 E QUERY    [thread1] BulkWriteError: 2 write errors in bulk operation :
BulkWriteError({
	"writeErrors" : [
		{
			"index" : 0,
			"code" : 11000,
			"errmsg" : "E11000 duplicate key error collection: user.hobbies index: _id_ dup key: { : \"yago\" }",
			"op" : {
				"_id" : "yago",
				"name" : "Yoga"
			}
		},
		{
			"index" : 1,
			"code" : 11000,
			"errmsg" : "E11000 duplicate key error collection: user.hobbies index: _id_ dup key: { : \"cooking\" }",
			"op" : {
				"_id" : "cooking",
				"name" : "Cooking"
			}
		}
	],
	"writeConcernErrors" : [ ],
	"nInserted" : 1,
	"nUpserted" : 0,
	"nMatched" : 0,
	"nModified" : 0,
	"nRemoved" : 0,
	"upserted" : [ ]
})
BulkWriteError@src/mongo/shell/bulk_api.js:369:48
BulkWriteResult/this.toError@src/mongo/shell/bulk_api.js:333:24
Bulk/this.execute@src/mongo/shell/bulk_api.js:1177:1
DBCollection.prototype.insertMany@src/mongo/shell/crud_api.js:314:5
@(shell):1:1

> db.hobbies.find().pretty()
**Output**
{ "_id" : "sports", "name" : "Sports" }
{ "_id" : "cooking", "name" : "Cooking" }
{ "_id" : "cars", "name" : "Cars" }
{ "_id" : "yago", "name" : "Yoga" }
{ "_id" : "hiking", "name" : "Hiking" }
```

# Write Concern

>💡 Control the "level of guarantee” <br/>

>💡 Client ⇒ MongoDB Server ⇒ Storage Engine ⇒ 1. Memory 2.Data on Disk

>💡 **Client:** Shell/My app which uses MongoDB <br/>
> **MongoDB Server:** mongod <br/>
> **Storage Engine:** responsible for writing data on disk



>💡 **Write Concern False:** `w: 0` You sent the request but you don't know if it reached the server. If any network connection issue creates. `W:0` is super fast obviously, it tells you nothing about whether this succeeds or not. Does not wait for _id generation(for insertion) or response. The server will not give back an acknowledgment of operation although it successfully happened. <br/><br/>
> **Write Concern True:** `w: 1` This is the **default value**(means should be accepted to write). In write, the number means how many instances you want this write to be acknowledged. With 1 is the default. So the storage engine is aware of it and will eventually write to the disk.


>💡 **Journal:** The journal is an additional file that is a storage engine managed like a To-Do file. It works when if the server is down for some reason then the file is still there. If the restart the server or if it recovers basically. **Default** is **false** or **undefined**. If the journal is true then it could be a little bit slower(because entry will have been added to the journal and waited for that journal editing to finish).

>💡 `wtimeout` Amount of time the server will wait for a response.

### inertOne

| {w: 1, j: undefined} |  |
| --- | --- |
| {w: 1, j: true} | Greater security that this will happen and succeed even if the server should face issues |
| {w: 1, wtimeout: 200, j: true} | This simply means which time frame do you give your server to report success for this write before you cancel it. |

<details>
 <summary>`w: 0` ⇒ example</summary>

```schema
    > db.persons.insertOne({name: 'Chrissy', age: 44},{ writeConcern: {w: 0} })
    **Output**
    { "acknowledged" : false }

    > db.persons.find()
    **Output**
    { "_id" : ObjectId("5f151e97e3242ab6a2f87b4e"), "name" : "Phil", "age" : 35 }
    { "_id" : ObjectId("5f15209ae3242ab6a2f87b4f"), "name" : "Khil", "age" : 45 }
    { "_id" : ObjectId("5f15209ae3242ab6a2f87b50"), "name" : "RAJU", "age" : 22 }
    { "_id" : ObjectId("5f154012e3242ab6a2f87b52"), "name" : "Chrissy", "age" : 44 }
  ```

</details>
    
<details>
  <summary>`w: 1, j: false` ⇒ example</summary>

```schema
    > db.persons.insertOne({name: 'Michel', age: 35},{writeConcern: {w: 1, j: false}})
    **Output**
    {
    	"acknowledged" : true,
    	"insertedId" : ObjectId("5f154215e3242ab6a2f87b54")
    }
```

</details>
    
<details>
  <summary>`w: 1, j: true` ⇒ example</summary>

```schema
    > db.persons.insertOne({name: 'Michela', age: 35},{writeConcern: {w: 1, j: true}})
    **Output**
    {
    	"acknowledged" : true,
    	"insertedId" : ObjectId("5f154255e3242ab6a2f87b55")
    }
  ```
</details>
    
<details>
  <summary>`wtimeout: 200` ⇒ example</summary>

  ```schema
    > db.persons.insertOne({name: 'Aliya', age: 35},{writeConcern: {w: 1, j: true,wtimeout: 200}})
    {
    	"acknowledged" : true,
    	"insertedId" : ObjectId("5f1542cee3242ab6a2f87b56")
    }
  ```
</details>
    
    
    

It is super fast and will cause an issue if the network connection is slow.

```bash
> db.persons.insertOne({name: 'Aliya', age: 35},{writeConcern: {w: 1, j: true,wtimeout: 1}})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("5f154309e3242ab6a2f87b57")
}
```

# Atomicity

- Operation (e.g. `insertOne()`) --> Error --> Rolled Back(i.e NOTHING is saved)
- Operation (e.g. `insertOne()`) --> Success --> Saved as Whole

- The Atomicity guarantees that an atomic transaction which means the transaction either succeeds as a whole or it fails as a whole.
- It fails during the writing, everything is rolled back for that document that is inserted.
- It's on a per document level, which means the top-level document, it includes all embedded documents, and all arrays so that is all included.

# Importing Data

- First, navigate to the file location using cd or using file explorer and right-click and use the open with terminal option.
- If already add Mongo binaries to the path variables on the operating system `mongoimport` will work globally otherwise have to navigate to the folder where the Mongo binaries are located.

## Example

`mongoimport tv-shows.json -d movieData -c movies --jsonArray --drop`

| tv-shows.json | The file name for import(where we have data)                                                                                                                                  |
| --- |-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -d <database name> | Which will be created.                                                                                                                                                        |
| -c <collection name> | Which will be created under a specified database name.                                                                                                                        |
| --jsonArray | This means there is an array of documents default one document(to make the mongo import command aware of this)                                                                |
| --drop | The collection should already exist, it will be dropped and then re-added otherwise it we'll append the data to the existing collection and that might also be what you want. |