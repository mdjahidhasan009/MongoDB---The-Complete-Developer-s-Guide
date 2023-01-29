# Using indexes for sorting

This query also works like indexes. This also uses index scan `"indexName": "dob.age_1_gender_1"` as MongoDB already has index it will be easy to sort by gender using the already created index and also have to do less work. 

```bash
> db.contacts.explain().find({'dob.age': 35}).sort({gender: 1})
**Output**
{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "contactData.contacts",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"dob.age" : {
				"$eq" : 35
			}
		},
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"dob.age" : 1,
					"gender" : 1
				},
				"indexName" : "dob.age_1_gender_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"dob.age" : [ ],
					"gender" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"dob.age" : [
						"[35.0, 35.0]"
					],
					"gender" : [ => Means MongoDB uses already exits index to sort by gender
						"[MinKey, MaxKey]"
					]
				}
			}
		},
		"rejectedPlans" : [ ]
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

MongoDB db reserves 32MB for fetched documents when using sort it could be cross it’s this limit but if the key we want to sort already being used in any index that will need less memory to sort and also will be helpful if can not sort by any key for memory limitation.

Understanding the default index and finding how many indexes in documents

```cpp
> db.contacts.getIndexes()
[
	{
		"v" : 2,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "contactData.contacts"
	},
	{
		"v" : 2,
		"key" : {
			"dob.age" : 1,
			"gender" : 1
		},
		"name" : "dob.age_1_gender_1",
		"ns" : "contactData.contacts"
	}
]
```