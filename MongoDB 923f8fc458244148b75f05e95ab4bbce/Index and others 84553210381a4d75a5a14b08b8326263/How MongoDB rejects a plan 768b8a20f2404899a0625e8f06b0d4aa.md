# How MongoDB rejects a plan

Creating a compound index
Order is important for compound index
Creating an index putting name first wouldn't make much sense(As we already have an index of names then it will be the same as that).
If age comes first, we can also filter just for age and take advantage of this index.
If filtered for just a name and didn't have that index, the name could not be supported by the index.

Here we can use just age or a combination of age and name.

```cpp
db.customers.createIndex({age: 1, name: 1})

> db.customers.getIndexes()
[
	{
		"v" : 2,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "test.customers"
	},
	{
		"v" : 2,
		"key" : {
			"name" : 1
		},
		"name" : "name_1",
		"ns" : "test.customers"
	},
	{
		"v" : 2,
		"key" : {
			"age" : 1,
			"name" : 1
		},
		"name" : "age_1_name_1",
		"ns" : "test.customers"
	}
]
```

Let execute query when executing query order does not matter in a compound index. Will reject this index if there are names only as it already has an index see rejectionPlan and will work for if find on basic of name and age both field see winningPlan.

```cpp
db.customers.explain().find({age: 30, name: 'Max'})
db.customers.explain().find({name: 'Max', age: 30})

**Output** 
{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "test.customers",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"$and" : [
				{
					"age" : {
						"$eq" : 30
					}
				},
				{
					"name" : {
						"$eq" : "Max"
					}
				}
			]
		},
		"**winningPlan**" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"age" : 1,
					"name" : 1
				},
				"indexName" : "age_1_name_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"age" : [ ],
					"name" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"age" : [
						"[30.0, 30.0]"
					],
					"name" : [
						"[\"Max\", \"Max\"]"
					]
				}
			}
		},
		"**rejectedPlans**" : [
			{
				"stage" : "FETCH",
				"filter" : {
					"age" : {
						"$eq" : 30
					}
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
			}
		]
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

Wining plan

1. Approach 1
2. Approach 2
3. Approach 3 ⇒ winning Plan ⇒ Cached ⇒ Cache ⇒ but the cache is not there forever

Here approach 3 is the best match compared to 1 and 2.

When the winning plan gets removed.

1. Write Threshold (currently 1,000)
2. Index is Rebuilt
3. Other Indexes are Added or Removed
4. MongoDB Server is Restarted

```cpp
db.customers.insertOne({name:'Raju', age:22, salary: 1000})
```

Get details of all plan
Here we get the all details of the plan which is good and the execution time

```cpp
db.customers.explain('allPlansExecution').find({age: 30, name: 'Max'})
```

- Output
    
    ```bash
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "test.customers",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"$and" : [
    				{
    					"age" : {
    						"$eq" : 30
    					}
    				},
    				{
    					"name" : {
    						"$eq" : "Max"
    					}
    				}
    			]
    		},
    		"winningPlan" : {
    			"stage" : "FETCH",
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"keyPattern" : {
    					"age" : 1,
    					"name" : 1
    				},
    				"indexName" : "age_1_name_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"age" : [ ],
    					"name" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"age" : [
    						"[30.0, 30.0]"
    					],
    					"name" : [
    						"[\"Max\", \"Max\"]"
    					]
    				}
    			}
    		},
    		"rejectedPlans" : [
    			{
    				"stage" : "FETCH",
    				"filter" : {
    					"age" : {
    						"$eq" : 30
    					}
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
    			}
    		]
    	},
    	"executionStats" : {
    		"executionSuccess" : true,
    		"nReturned" : 0,
    		"executionTimeMillis" : 0,
    		"totalKeysExamined" : 0,
    		"totalDocsExamined" : 0,
    		"executionStages" : {
    			"stage" : "FETCH",
    			"nReturned" : 0,
    			"executionTimeMillisEstimate" : 0,
    			"works" : 2,
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
    				"stage" : "IXSCAN",
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
    					"age" : 1,
    					"name" : 1
    				},
    				"indexName" : "age_1_name_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"age" : [ ],
    					"name" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"age" : [
    						"[30.0, 30.0]"
    					],
    					"name" : [
    						"[\"Max\", \"Max\"]"
    					]
    				},
    				"keysExamined" : 0,
    				"seeks" : 1,
    				"dupsTested" : 0,
    				"dupsDropped" : 0,
    				"seenInvalidated" : 0
    			}
    		},
    		"allPlansExecution" : [
    			{
    				"nReturned" : 0,
    				"executionTimeMillisEstimate" : 0,
    				"totalKeysExamined" : 1,
    				"totalDocsExamined" : 1,
    				"executionStages" : {
    					"stage" : "FETCH",
    					"filter" : {
    						"age" : {
    							"$eq" : 30
    						}
    					},
    					"nReturned" : 0,
    					"executionTimeMillisEstimate" : 0,
    					"works" : 1,
    					"advanced" : 0,
    					"needTime" : 1,
    					"needYield" : 0,
    					"saveState" : 0,
    					"restoreState" : 0,
    					"isEOF" : 0,
    					"invalidates" : 0,
    					"docsExamined" : 1,
    					"alreadyHasObj" : 0,
    					"inputStage" : {
    						"stage" : "IXSCAN",
    						"nReturned" : 1,
    						"executionTimeMillisEstimate" : 0,
    						"works" : 1,
    						"advanced" : 1,
    						"needTime" : 0,
    						"needYield" : 0,
    						"saveState" : 0,
    						"restoreState" : 0,
    						"isEOF" : 0,
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
    			{
    				"nReturned" : 0,
    				"executionTimeMillisEstimate" : 0,
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
    						"stage" : "IXSCAN",
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
    							"age" : 1,
    							"name" : 1
    						},
    						"indexName" : "age_1_name_1",
    						"isMultiKey" : false,
    						"multiKeyPaths" : {
    							"age" : [ ],
    							"name" : [ ]
    						},
    						"isUnique" : false,
    						"isSparse" : false,
    						"isPartial" : false,
    						"indexVersion" : 2,
    						"direction" : "forward",
    						"indexBounds" : {
    							"age" : [
    								"[30.0, 30.0]"
    							],
    							"name" : [
    								"[\"Max\", \"Max\"]"
    							]
    						},
    						"keysExamined" : 0,
    						"seeks" : 1,
    						"dupsTested" : 0,
    						"dupsDropped" : 0,
    						"seenInvalidated" : 0
    					}
    				}
    			}
    		]
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