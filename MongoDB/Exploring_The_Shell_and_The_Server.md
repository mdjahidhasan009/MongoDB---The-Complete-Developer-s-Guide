# Exploring The Shell and The Server

- MongoDB can be started as a process or service.
- wiredTiger is the default storage engine of mongodb. Mongodb supports many storage engine.
- any one can save your own configaration in a file in .cfg file.

| Command             | What it does                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `mongod --help`       | List of all commands in the MongoDB.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `-dbpath <path>` <br/><br/> `sudo mongod --dbpath <path>` | Where the database files will be stored. <path> is the path of the folder where we wantto store the database files.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `-logpath<path>` <br/><br/>  `sudo mongod --logpath <path>/logs.log` | Where the log files will be stored.Here logs.log is the log file itself. For the logpath we also have to give the log file name also.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `sudo mongod --fork`  | Will show an error as we have to give log path with this. As this will start mongod as an background service and without terminal mongod will not able to show the error and logs those will be stored in the log file. And the command will be  <br/><br/> here logs.log is the file name for the log  <br/><br/> `sudo mongod --fork --logpath <path>/logs.log` ⇒After typing this it will start mongodb as child process(background process).  <br/><br/> **mongo**  <br/> This will show mongo service on terminal with was running at background for fork command.If close the terminal mongodb will be remain running in the background. Then we will forcefully stop that this background process will be stopped net start MongoDB. This will start mongodb as background service in windows.  <br/><br/> **Stoping mongodb service**  <br/>For linux/mac => typing "mongo" will show mongodb service on the terminal. Then have to switch admin database by typing "use admin" then by this "db.shutdownServer()" commond background service will be shurt down. |
| `sudo mongod -f <path>/*.cfg` | By this command we will tell mongodb where is my configaration file is otherwise it will pick its default file. We can also named it as *.conf.  <br/><br/> Simple configaration file structure(file name will be like *.cfg)  <br/>storage:  <br/>`dbPath: <path>`  <br/><br/>systemLog:  <br/>destination: file  <br/>path: <path>/logs.log => Here logs.log is the log file                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `mongo --help` | Options for mongo shell. It has way less options because it's just connecting client not the server of mongodb. We can run it without connecting to a database because the shell is based on javascript. Also can run in quite mode to output less information.  <br/><br/>help  <br/>First type "mongo" command and then "help" command will give list of some important help information/command. We can also go to detail help like "help admin" => will show some useful commands for administrating the database. (after connect with an particular database by "use dbName")  <br/><br/>`db.help()` <br/> Help on db methods => collection of some useful command  <br/><br/>`db.myCollection.help()`  <br/>Help on collection method => collection of some useful command                                                                                                                                                                                                                                                                                                          |

<details>
    <summary>Others</summary>
    <details>
        <summary>mongod --help</summary>

```schema
        mongod --help
        > help
            db.help()                    help on db methods
            db.mycoll.help()             help on collection methods
            sh.help()                    sharding helpers
            rs.help()                    replica set helpers
            help admin                   administrative help
            help connect                 connecting to a db help
            help keys                    key shortcuts
            help misc                    misc things to know
            help mr                      mapreduce
        
            show dbs                     show database names
            show collections             show collections in current database
            show users                   show users in current database
            show profile                 show most recent system.profile entries with time >= 1ms
            show logs                    show the accessible logger names
            show log [name]              prints out the last segment of log in memory, 'global' is default
            use <db_name>                set current database
            db.foo.find()                list objects in collection foo
            db.foo.find( { a : 1 } )     list objects in foo where a == 1
            it                           result of the last line evaluated; use to further iterate
            DBQuery.shellBatchSize = x   set default number of items to display on shell
            exit
```

    
</details>



<details>
        <summary>DB helps</summary>

```scheme
    > use shop
    switched to db shop
```
    
```scheme
    > db.help()
    DB methods:
    	db.adminCommand(nameOrDocument) - switches to 'admin' db, and runs command [just calls db.runCommand(...)]
    	db.aggregate([pipeline], {options}) - performs a collectionless aggregation on this database; returns a cursor
    	db.auth(username, password)
    	db.cloneDatabase(fromhost)
    	db.commandHelp(name) returns the help for the command
    	db.copyDatabase(fromdb, todb, fromhost)
    	db.createCollection(name, {size: ..., capped: ..., max: ...})
    	db.createView(name, viewOn, [{$operator: {...}}, ...], {viewOptions})
    	db.createUser(userDocument)
    	db.currentOp() displays currently executing operations in the db
    	db.dropDatabase()
    	db.eval() - deprecated
    	db.fsyncLock() flush data to disk and lock server for backups
    	db.fsyncUnlock() unlocks server following a db.fsyncLock()
    	db.getCollection(cname) same as db['cname'] or db.cname
    	db.getCollectionInfos([filter]) - returns a list that contains the names and options of the db's collections
    	db.getCollectionNames()
    	db.getLastError() - just returns the err msg string
    	db.getLastErrorObj() - return full status object
    	db.getLogComponents()
    	db.getMongo() get the server connection object
    	db.getMongo().setSlaveOk() allow queries on a replication slave server
    	db.getName()
    	db.getPrevError()
    	db.getProfilingLevel() - deprecated
    	db.getProfilingStatus() - returns if profiling is on and slow threshold
    	db.getReplicationInfo()
    	db.getSiblingDB(name) get the db at the same server as this one
    	db.getWriteConcern() - returns the write concern used for any operations on this db, inherited from server object if set
    	db.hostInfo() get details about the server's host
    	db.isMaster() check replica primary status
    	db.killOp(opid) kills the current operation in the db
    	db.listCommands() lists all the db commands
    	db.loadServerScripts() loads all the scripts in db.system.js
    	db.logout()
    	db.printCollectionStats()
    	db.printReplicationInfo()
    	db.printShardingStatus()
    	db.printSlaveReplicationInfo()
    	db.dropUser(username)
    	db.repairDatabase()
    	db.resetError()
    	db.runCommand(cmdObj) run a database command.  if cmdObj is a string, turns it into {cmdObj: 1}
    	db.serverStatus()
    	db.setLogLevel(level,<component>)
    	db.setProfilingLevel(level,slowms) 0=off 1=slow 2=all
    	db.setWriteConcern(<write concern doc>) - sets the write concern for writes to the db
    	db.unsetWriteConcern(<write concern doc>) - unsets the write concern for writes to the db
    	db.setVerboseShell(flag) display extra information in shell output
    	db.shutdownServer()
    	db.stats()
    	db.version() current version of the server
```


</details>


<details>
        <summary>Collection helps</summary>

```scheme
    > show collections
    products
```
    
```scheme
    > db.products.help()
    DBCollection help
    	db.products.find().help() - show DBCursor help
    	db.products.bulkWrite( operations, <optional params> ) - bulk execute write operations, optional parameters are: w, wtimeout, j
    	db.products.count( query = {}, <optional params> ) - count the number of documents that matches the query, optional parameters are: limit, skip, hint, maxTimeMS
    	db.products.copyTo(newColl) - duplicates collection by copying all documents to newColl; no indexes are copied.
    	db.products.convertToCapped(maxBytes) - calls {convertToCapped:'products', size:maxBytes}} command
    	db.products.createIndex(keypattern[,options])
    	db.products.createIndexes([keypatterns], <options>)
    	db.products.dataSize()
    	db.products.deleteOne( filter, <optional params> ) - delete first matching document, optional parameters are: w, wtimeout, j
    	db.products.deleteMany( filter, <optional params> ) - delete all matching documents, optional parameters are: w, wtimeout, j
    	db.products.distinct( key, query, <optional params> ) - e.g. db.products.distinct( 'x' ), optional parameters are: maxTimeMS
    	db.products.drop() drop the collection
    	db.products.dropIndex(index) - e.g. db.products.dropIndex( "indexName" ) or db.products.dropIndex( { "indexKey" : 1 } )
    	db.products.dropIndexes()
    	db.products.ensureIndex(keypattern[,options]) - DEPRECATED, use createIndex() instead
    	db.products.explain().help() - show explain help
    	db.products.reIndex()
    	db.products.find([query],[fields]) - query is an optional query filter. fields is optional set of fields to return.
    	                                              e.g. db.products.find( {x:77} , {name:1, x:1} )
    	db.products.find(...).count()
    	db.products.find(...).limit(n)
    	db.products.find(...).skip(n)
    	db.products.find(...).sort(...)
    	db.products.findOne([query], [fields], [options], [readConcern])
    	db.products.findOneAndDelete( filter, <optional params> ) - delete first matching document, optional parameters are: projection, sort, maxTimeMS
    	db.products.findOneAndReplace( filter, replacement, <optional params> ) - replace first matching document, optional parameters are: projection, sort, maxTimeMS, upsert, returnNewDocument
    	db.products.findOneAndUpdate( filter, update, <optional params> ) - update first matching document, optional parameters are: projection, sort, maxTimeMS, upsert, returnNewDocument
    	db.products.getDB() get DB object associated with collection
    	db.products.getPlanCache() get query plan cache associated with collection
    	db.products.getIndexes()
    	db.products.group( { key : ..., initial: ..., reduce : ...[, cond: ...] } )
    	db.products.insert(obj)
    	db.products.insertOne( obj, <optional params> ) - insert a document, optional parameters are: w, wtimeout, j
    	db.products.insertMany( [objects], <optional params> ) - insert multiple documents, optional parameters are: w, wtimeout, j
    	db.products.mapReduce( mapFunction , reduceFunction , <optional params> )
    	db.products.aggregate( [pipeline], <optional params> ) - performs an aggregation on a collection; returns a cursor
    	db.products.remove(query)
    	db.products.replaceOne( filter, replacement, <optional params> ) - replace the first matching document, optional parameters are: upsert, w, wtimeout, j
    	db.products.renameCollection( newName , <dropTarget> ) renames the collection.
    	db.products.runCommand( name , <options> ) runs a db command with the given name where the first param is the collection name
    	db.products.save(obj)
    	db.products.stats({scale: N, indexDetails: true/false, indexDetailsKey: <index key>, indexDetailsName: <index name>})
    	db.products.storageSize() - includes free space allocated to this collection
    	db.products.totalIndexSize() - size in bytes of all the indexes
    	db.products.totalSize() - storage allocated for all data and indexes
    	db.products.update( query, object[, upsert_bool, multi_bool] ) - instead of two flags, you can pass an object with fields: upsert, multi
    	db.products.updateOne( filter, update, <optional params> ) - update the first matching document, optional parameters are: upsert, w, wtimeout, j
    	db.products.updateMany( filter, update, <optional params> ) - update all matching documents, optional parameters are: upsert, w, wtimeout, j
    	db.products.validate( <full> ) - SLOW
    	db.products.getShardVersion() - only for use with sharding
    	db.products.getShardDistribution() - prints statistics about data distribution in the cluster
    	db.products.getSplitKeysForChunks( <maxChunkSize> ) - calculates split points over all chunks and returns splitter function
    	db.products.getWriteConcern() - returns the write concern used for any operations on this collection, inherited from server/db if set
    	db.products.setWriteConcern( <write concern doc> ) - sets the write concern for writes to the collection
    	db.products.unsetWriteConcern( <write concern doc> ) - unsets the write concern for writes to the collection
    	db.products.latencyStats() - display operation latency histograms for this collection
```


</details>


</details>
