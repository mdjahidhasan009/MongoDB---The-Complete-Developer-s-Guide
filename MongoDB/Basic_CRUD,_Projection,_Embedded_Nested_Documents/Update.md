# Update

```scheme
updateOne(filter, data, options)
updateMany(filter, data, options)
replaceOne(filter, data, options)
```

### updateOne

Will **update** **first** **matched** document(with key distance and value 1200 and will set marker key to delete **if exits** the key **otherwise** will **create** that key and value **inside** the document).

```scheme
> db.flightData.updateOne({distance: 1200},{$set:{marker: "delete"}})
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 } //if successfull

> db.flightData.updateOne({_id : ObjectId("5f132aebd022deabe244f26d")},{$set:{delayed: true}})
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 0 }
```

### updateMany

Will **update all documents** which matched the **filter**, in this case, all as there is no filter condition, and will set the value of the key `marker` if **exits** otherwise **will add** a key marker with the value `toDelete` at the document.

```scheme
> db.flightData.updateMany({},{$set:{marker: "toDelete"}})
```

### update

> ****Deprecated this method****
> 

If the targeted document already has a delayed key with the value false then the update command will not update that but updateOne comment will update this means overwriting the same value.

```scheme
> db.flightData.update({_id : ObjectId("5f132aebd022deabe244f26d")},{$set:{delayed: false}})
**Output**
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

Will **delete** all key-value except `delayed: false`.

```scheme
> db.flightData.update({_id : ObjectId("5f132aebd022deabe244f26d")},{delayed: false})

**Output**
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

> db.flightData.find().pretty()
**Output**
{ "_id" : ObjectId("5f132aebd022deabe244f26d"), "delayed" : false } =>modified document
{
	"_id" : ObjectId("5f132aebd022deabe244f26e"),
	"departureAirport" : "LHR",
	"arrivalAirport" : "TXL",
	"aircraft" : "Airbus A320",
	"distance" : 950,
	"intercontinental" : false
}
```

### replaceOne

The same can be done by replaceOne

```scheme
> db.flightData.replaceOne({_id : ObjectId("5f132aebd022deabe244f26d")},{
...     "departureAirport": "MUC",
...     "arrivalAirport": "SFO",
...     "aircraft": "Airbus A380",
...     "distance": 12000,
...     "intercontinental": true
...   })
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

Before running command replaceOne
{ "_id" : ObjectId("5f132aebd022deabe244f26d"), "delayed" : false } 
After running command replaceOne document will be like
{
	"_id" : ObjectId("5f132aebd022deabe244f26d"),
	"departureAirport" : "MUC",
	"arrivalAirport" : "SFO",
	"aircraft" : "Airbus A380",
	"distance" : 12000,
	"intercontinental" : true
}
```

| update | updateOne & updateMany |
| --- | --- |
| Does not need $set | Need $set attribute |
| Does not overwrite the value of a key if the command and database have the same value. (If the document has only one key) | Will overwrite if have the same value in command and database. |
| Keep only keys and values which are given in command others will be deleted automatically. The same can be done by replaceOne | Will not delete any key value. |