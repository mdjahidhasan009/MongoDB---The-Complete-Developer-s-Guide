# Query Diagnosis and Query Planning

1. 'queryPlanner' ⇒ Show Summary for Executed Query + Winning Plan
2. 'executionsStats' ⇒ Show Detailed Summary for Executed Query + Winning Plan + Possibly Rejected Plans
3. 'allPlanExecution' ⇒  Show Detailed Summary for Executed Query + Winning Plan + Winning Plan Decision Process

### Efficient Queries and Covered Queries

1. Milliseconds Process Time
2. IXSCAN typically beats COLLSCAN
3. Number of keys(in index) Examined
4. Number of Documents Examined 
5. Number of Documents Returns

The number of keys(in index) Examined and the Number of Documents Examined should be as close as possible or zero.

The number of Documents Examined and the Number of Documents Returned should be as close as possible or zero.

**Covered Query** ⇒ Number of keys(in index) Examined, Number of Documents Examined, Number of Documents Returns

Understanding Covered Queries

```bash
> db.customers.insertMany([{name: 'Max', age: 29, salary: 3000}, {name: 'Manu', age: 30, salary: 4000}])
**Output**
{
	"acknowledged" : true,
	"insertedIds" : [
		ObjectId("632f30533bb3e298033546d1"),
		ObjectId("632f30533bb3e298033546d2")
	]
}
```

Creating index

```cpp
db.customers.createIndex({name: 1})
db.customers.getIndexes()
```

Get info

```cpp
db.customers.explain('executionStats').find({name: 'Max'})
```

- Output `"totalKeysExamined" : 1,` `"totalDocsExamined" : 1`
    
    ```bash
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "test.customers",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"name" : {
    				"$eq" : "Max"
    			}
    		},
    		"winningPlan" : {
    			"stage" : "FETCH",
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"keyPattern" : {
    					"name" : 1
    				},
    				"indexName" : "name_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"name" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"name" : [
    						"[\"Max\", \"Max\"]"
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
    				"stage" : "IXSCAN",
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
    					"name" : 1
    				},
    				"indexName" : "name_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"name" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"name" : [
    						"[\"Max\", \"Max\"]"
    					]
    				},
    				"keysExamined" : 1,
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
    

Let's implement **covered queries.** If can optimize the query, then have to reach that covered query state. Useful when typically returning the specific fields

```cpp
db.customers.explain('executionStats').find({name: 'Max'},{_id: 0, name: 1})
```

- Output `"totalKeysExamined" : 1`, `"totalDocsExamined" : 0`
    
    ```bash
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "test.customers",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"name" : {
    				"$eq" : "Max"
    			}
    		},
    		"winningPlan" : {
    			"stage" : "PROJECTION",
    			"transformBy" : {
    				"_id" : 0,
    				"name" : 1
    			},
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"keyPattern" : {
    					"name" : 1
    				},
    				"indexName" : "name_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"name" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"name" : [
    						"[\"Max\", \"Max\"]"
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
    		"totalDocsExamined" : 0,
    		"executionStages" : {
    			"stage" : "PROJECTION",
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
    			"transformBy" : {
    				"_id" : 0,
    				"name" : 1
    			},
    			"inputStage" : {
    				"stage" : "IXSCAN",
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
    					"name" : 1
    				},
    				"indexName" : "name_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"name" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"name" : [
    						"[\"Max\", \"Max\"]"
    					]
    				},
    				"keysExamined" : 1,
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
    

**This cover query does not understand fully.**