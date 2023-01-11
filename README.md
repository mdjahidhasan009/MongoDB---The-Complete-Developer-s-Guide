### This is the udemy mongoDB course.This course is taken by my favorite course instructor Maximilian Schwarzmüller.
#### In this repsitory I also added some extra information about mongoDB. 
# MongoDB

[Initial Concepts](readme.md)

[Basic CRUD, Projection, Embedded/Nested Documents, Array](Other_Pages/Basic_CRUD_Projection_Embedded_or_Nested_Documents_Array.md)

[Schema & Relations]()

[Exploring The Shell and The Server]()

[Create/Insert, Write concern, Atomicity]()

[Read]()

[Update]()

[Delete]()

[Index and others]()

[Geospatial Data]()

[Aggregation Framework]()

[Numeric Data]()

[Security]()

[Performance Fault Tolerancy Deployment]()

[Transactions]()


# Initial Concepts

>> MongoDB names came from **humongous** as it can **store** lots and **lots of data**.

>  - Store data as documents like JavaScript Object
>  - noSQL database
>  - Store like JSON
>  - BSON (b ⇒ binary) Data Structure into server
>  - No schema(can add if wanted)
>  - No / few relations
>  - Flow of data
>      - Database
>      - Collection(Like table)
>      - Document(like JSON object values these are schemaless


>> **Normalizing**: Storing and distributing data in multiple tables where every table has a clear schema and use lots of relations(in SQL).
> 

> As MongoDB saves all in one single document so for fetching it does not need any complex operation like join(SQL) so MongoDB can fetch query is more simple, more flexible, and more efficient(as no need to 
> merge collection/table) than SQL. 

> MongoDB is popular for **read and write heavy** applications (like sensors which send data every second/online shop/blog).

# Some very basic commands

### Start MongoDB on the shell

```powershell
mongo
```

### Show all databases(admin, config, and local are predefined there)

```powershell
show dbs
```

### Use existing database or create a new one

Will connected with database name 'shop' if already exits or if the database is not exited yet MongoDB will create that

```powershell
use shop
```

Output will be

> switched to db shop
> 

**Note:** If the database is not created previously this command will create that database but the shop database will not create instantly(as a result `show dbs` will not list shop db). When we will first add data to the shops database than MongoDB implicitly will create that database and also `show dbs` commands that will list the database.

### Get all documents of a collection.

Will give all data as we add not add any argument.

```powershell
db.products.find()
```

### Get JSON like format

Will return exactly the same data as JSON like format which is easy to read.

```powershell
db.products.find().pretty
```

### Clear the screen

```powershell
cls
```

## MongoDB Ecosystem

### MongoDB Database

1. Self - Managed / Enterprise
    1. CloudManager / OpsManager
2. Atlas(Cloud) 
3. Mobile

Others options

1. Compass
2. BI Connectors
3. MongoDB Charts

### Stitch


> 💡 Basically a serverless backend solution.


That gives


> 💡 **Serverless Query Api** **⇒** a tool sets or tools to directly database query from inside client apps.

> 💡 **Serverless Functions(in the cloud ⇒ related to js) ⇒** a tool sets or tools to directly database query from inside client apps.

> 💡 **Database Triggers ⇒** that allows us to events in a database, like a document was inserted and then execute a function in response to that and that function could then maybe send an e-mail.

> 💡 **Real-Time Sync ⇒** basically is built to synchronize a database in a cloud with that mobile offline supporting database.

## How MongoDB works

### Application

- Frontend(UI)
- Backend(Server) ⇒ Which interacts/communicates with MongoDB server using their corresponding drivers
    - Node.js
    - Java
    - Python
    - MongoDB Shell(does not depends on any specific programming language).

### Data

- MongoDB server
    - MongoDB server use a storage engine default for MongoDB is **Wired Tiger**. Which access file/data where the data kept(database information collection, table, etc)
        - Storage Engine
            - Read + Write Data to Files(slow)
            - Read + Write Data in Memory(fast) ⇒ Memory(RAM)

### Documents Created Implicity

JSON data converts into BSON data

`{
  "name" : "MAX",
  "age" : 29
}`

it converts into BSON

BSON 

1. Binary data 
2. Extends JSON Types(e.g more detailed Number Types) 
3. Efficient Storage

### MongoD service


>💡 **mongod** stands for “**Mongo Daemon**”. mongod is a **background process** used by MongoDB. The main purpose of mongod is to manage all the MongoDB server tasks. For instance, accepting requests, 
> responding to clients, and memory management.
> mongo is a command line shell that can interact with the client (for example, system administrators and developers).


### Start mongod process(default port is 27017)

```bash
# To start in default port 27017
mongo
# To start at 27018 port 
mongo --port 27018
```

### Start MongoDB on the server

```bash
# To start in default port 27017
mongo
# To start at 27018 port 
mongo --port 27018
```
