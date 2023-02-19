# Multi Key Indexes

Insert new data into a new table

```cpp
db.contacts.insertOne(
		{
				name:'Max', hobbies:['Cooking', 'Sports'], 
				address: [{street: 'Main Street'}, {street: 'Second Street'}]
		}
)
**Output**
{
	"acknowledged" : true,
	"insertedId" : ObjectId("633024183bb3e298033546d4")
}

db.contacts.findOne()
**Output**
{
	"_id" : ObjectId("633024183bb3e298033546d4"),
	"name" : "Max",
	"hobbies" : [
		"Cooking",
		"Sports"
	],
	"address" : [
		{
			"street" : "Main Street"
		},
		{
			"street" : "Second Street"
		}
	]
}
```

Create an index

```cpp
db.contacts.createIndex({hobbies: 1})
**Output**
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}

db.contacts.find({hobbies: 'Sports'}).pretty()
**Output**
{
	"_id" : ObjectId("633024183bb3e298033546d4"),
	"name" : "Max",
	"hobbies" : [
		"Cooking",
		"Sports"
	],
	"address" : [
		{
			"street" : "Main Street"
		},
		{
			"street" : "Second Street"
		}
	]
}
```

Execute explain
Here multi-key is true, it is created when documents into an array. MongoDB takes all values of the key of all documents in this case array and stores them separately for indexing. 

Such as if all keys have 4 values in the array key and have a total of 1000 documents then it will store 4000 elements for indexing.

```cpp
db.contacts.explain('executionStats').find({hobbies: 'Sports'})
```

<details>
<summary>Output  `"stage" : "IXSCAN"` `"isMultiKey" : true`</summary>

```schema
     {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "test.contacts",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"hobbies" : {
    				"$eq" : "Sports"
    			}
    		},
    		"winningPlan" : {
    			"stage" : "FETCH",
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"keyPattern" : {
    					"hobbies" : 1
    				},
    				"indexName" : "hobbies_1",
    				"isMultiKey" : true,
    				"multiKeyPaths" : {
    					"hobbies" : [
    						"hobbies"
    					]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"hobbies" : [
    						"[\"Sports\", \"Sports\"]"
    					]
    				}
    			}
    		},
    		"rejectedPlans" : [ ]
    	},
    	"executionStats" : {
    		"executionSuccess" : true,
    		"nReturned" : 1,
    		"executionTimeMillis" : 0,
    		"totalKeysExamined" : 1,
    		"totalDocsExamined" : 1,
    		"executionStages" : {
    			"stage" : "FETCH",
    			"nReturned" : 1,
    			"executionTimeMillisEstimate" : 0,
    			"works" : 2,
    			"advanced" : 1,
    			"needTime" : 0,
    			"needYield" : 0,
    			"saveState" : 0,
    			"restoreState" : 0,
    			"isEOF" : 1,
    			"invalidates" : 0,
    			"docsExamined" : 1,
    			"alreadyHasObj" : 0,
    			"inputStage" : {
    				**"stage" : "IXSCAN",**
    				"nReturned" : 1,
    				"executionTimeMillisEstimate" : 0,
    				"works" : 2,
    				"advanced" : 1,
    				"needTime" : 0,
    				"needYield" : 0,
    				"saveState" : 0,
    				"restoreState" : 0,
    				"isEOF" : 1,
    				"invalidates" : 0,
    				"keyPattern" : {
    					"hobbies" : 1
    				},
    				"indexName" : "hobbies_1",
    				**"isMultiKey" : true,**
    				"multiKeyPaths" : {
    					"hobbies" : [
    						"hobbies"
    					]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"hobbies" : [
    						"[\"Sports\", \"Sports\"]"
    					]
    				},
    				"keysExamined" : 1,
    				"seeks" : 1,
    				"dupsTested" : 1,
    				"dupsDropped" : 0,
    				"seenInvalidated" : 0
    			}
    		}
    	},
    	"serverInfo" : {
    		"host" : "jahid-HP-ProBook-6470b",
    		"port" : 27017,
    		"version" : "3.6.8",
    		"gitVersion" : "8e540c0b6db93ce994cc548f000900bdc740f80a"
    	},
    	"ok" : 1
    }
```

</details>
    

Lets create another index

```cpp
db.contacts.createIndex({addresses: 1})
**Output**
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 2,
	"numIndexesAfter" : 3,
	"ok" : 1
}
```

Here index does not work, because it holds all documents not the nested elements in the address key. 

```cpp
db.contacts.explain('executionStats').find({'addresses.street': 'Main Street'})
```

<details>
<summary>Output `"stage" : "COLLSCAN"`</summary>

```schema
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "test.contacts",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"addresses.street" : {
    				"$eq" : "Main Street"
    			}
    		},
    		"winningPlan" : {
    			"stage" : "COLLSCAN",
    			"filter" : {
    				"addresses.street" : {
    					"$eq" : "Main Street"
    				}
    			},
    			"direction" : "forward"
    		},
    		"rejectedPlans" : [ ]
    	},
    	"executionStats" : {
    		"executionSuccess" : true,
    		"nReturned" : 0,
    		"executionTimeMillis" : 0,
    		"totalKeysExamined" : 0,
    		"totalDocsExamined" : 2,
    		"executionStages" : {
    			**"stage" : "COLLSCAN",**
    			"filter" : {
    				"addresses.street" : {
    					"$eq" : "Main Street"
    				}
    			},
    			"nReturned" : 0,
    			"executionTimeMillisEstimate" : 0,
    			"works" : 4,
    			"advanced" : 0,
    			"needTime" : 3,
    			"needYield" : 0,
    			"saveState" : 0,
    			"restoreState" : 0,
    			"isEOF" : 1,
    			"invalidates" : 0,
    			"direction" : "forward",
    			"docsExamined" : 2
    		}
    	},
    	"serverInfo" : {
    		"host" : "jahid-HP-ProBook-6470b",
    		"port" : 27017,
    		"version" : "3.6.8",
    		"gitVersion" : "8e540c0b6db93ce994cc548f000900bdc740f80a"
    	},
    	"ok" : 1
    }
```

</details>
    

It works when querying as we are searching for the whole address document and we create an index of whole documents not for values in the nested document.

```cpp
db.contacts.explain('executionStats').find({addresses: {street: 'Main Street'}})
```

<details>
<summary>Output `"stage" : "IXSCAN"`</summary>

```schema
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "test.contacts",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"addresses" : {
    				"$eq" : {
    					"street" : "Main Street"
    				}
    			}
    		},
    		"winningPlan" : {
    			"stage" : "FETCH",
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"keyPattern" : {
    					"addresses" : 1
    				},
    				"indexName" : "addresses_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"addresses" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"addresses" : [
    						"[{ street: \"Main Street\" }, { street: \"Main Street\" }]"
    					]
    				}
    			}
    		},
    		"rejectedPlans" : [ ]
    	},
    	"executionStats" : {
    		"executionSuccess" : true,
    		"nReturned" : 0,
    		"executionTimeMillis" : 1,
    		"totalKeysExamined" : 0,
    		"totalDocsExamined" : 0,
    		"executionStages" : {
    			"stage" : "FETCH",
    			"nReturned" : 0,
    			"executionTimeMillisEstimate" : 0,
    			"works" : 1,
    			"advanced" : 0,
    			"needTime" : 0,
    			"needYield" : 0,
    			"saveState" : 0,
    			"restoreState" : 0,
    			"isEOF" : 1,
    			"invalidates" : 0,
    			"docsExamined" : 0,
    			"alreadyHasObj" : 0,
    			"inputStage" : {
    				**"stage" : "IXSCAN",**
    				"nReturned" : 0,
    				"executionTimeMillisEstimate" : 0,
    				"works" : 1,
    				"advanced" : 0,
    				"needTime" : 0,
    				"needYield" : 0,
    				"saveState" : 0,
    				"restoreState" : 0,
    				"isEOF" : 1,
    				"invalidates" : 0,
    				"keyPattern" : {
    					"addresses" : 1
    				},
    				"indexName" : "addresses_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"addresses" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"addresses" : [
    						"[{ street: \"Main Street\" }, { street: \"Main Street\" }]"
    					]
    				},
    				"keysExamined" : 0,
    				"seeks" : 1,
    				"dupsTested" : 0,
    				"dupsDropped" : 0,
    				"seenInvalidated" : 0
    			}
    		}
    	},
    	"serverInfo" : {
    		"host" : "jahid-HP-ProBook-6470b",
    		"port" : 27017,
    		"version" : "3.6.8",
    		"gitVersion" : "8e540c0b6db93ce994cc548f000900bdc740f80a"
    	},
    	"ok" : 1
    }
```
</details>
    

If the index is created like then it works, we are creating index for it also have a multi-key index

```cpp
db.contacts.createIndex({'addresses.street': 1})
**Output**
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 3,
	"numIndexesAfter" : 4,
	"ok" : 1
}
```

This is now index scan

```bash
db.contacts.explain('executionStats').find({'addresses.street': 'Main Street'})
```

<details>
<summary>Output `"stage" : "IXSCAN"`</summary>

```schema
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "test.contacts",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"addresses.street" : {
    				"$eq" : "Main Street"
    			}
    		},
    		"winningPlan" : {
    			"stage" : "FETCH",
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"keyPattern" : {
    					"addresses.street" : 1
    				},
    				"indexName" : "addresses.street_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"addresses.street" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"addresses.street" : [
    						"[\"Main Street\", \"Main Street\"]"
    					]
    				}
    			}
    		},
    		"rejectedPlans" : [ ]
    	},
    	"executionStats" : {
    		"executionSuccess" : true,
    		"nReturned" : 0,
    		"executionTimeMillis" : 3,
    		"totalKeysExamined" : 0,
    		"totalDocsExamined" : 0,
    		"executionStages" : {
    			"stage" : "FETCH",
    			"nReturned" : 0,
    			"executionTimeMillisEstimate" : 0,
    			"works" : 1,
    			"advanced" : 0,
    			"needTime" : 0,
    			"needYield" : 0,
    			"saveState" : 0,
    			"restoreState" : 0,
    			"isEOF" : 1,
    			"invalidates" : 0,
    			"docsExamined" : 0,
    			"alreadyHasObj" : 0,
    			"inputStage" : {
    				**"stage" : "IXSCAN",**
    				"nReturned" : 0,
    				"executionTimeMillisEstimate" : 0,
    				"works" : 1,
    				"advanced" : 0,
    				"needTime" : 0,
    				"needYield" : 0,
    				"saveState" : 0,
    				"restoreState" : 0,
    				"isEOF" : 1,
    				"invalidates" : 0,
    				"keyPattern" : {
    					"addresses.street" : 1
    				},
    				"indexName" : "addresses.street_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"addresses.street" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"addresses.street" : [
    						"[\"Main Street\", \"Main Street\"]"
    					]
    				},
    				"keysExamined" : 0,
    				"seeks" : 1,
    				"dupsTested" : 0,
    				"dupsDropped" : 0,
    				"seenInvalidated" : 0
    			}
    		}
    	},
    	"serverInfo" : {
    		"host" : "jahid-HP-ProBook-6470b",
    		"port" : 27017,
    		"version" : "3.6.8",
    		"gitVersion" : "8e540c0b6db93ce994cc548f000900bdc740f80a"
    	},
    	"ok" : 1
    }
```

</details>
    

Still **multi-key index** is super **helpful** if have **queries** that regularly target **array values** or even **nested values** in an **embedded document** in **arrays**.
There are a couple of restrictions or one important restriction to be precise when using multi-key indexes

Create a multi-key compound index, it is also possible, when having one multi-key

```cpp
db.contacts.createIndex({name: 1, hobbies: 1})
**Output**
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 5,
	"numIndexesAfter" : 6,
	"ok" : 1
}
```

But can not add more than one array in a single index

```cpp
db.contacts.createIndex({addresses: 1, hobbies: 1})
```