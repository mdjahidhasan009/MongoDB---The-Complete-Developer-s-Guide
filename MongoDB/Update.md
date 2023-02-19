# Update

1. Document Updating Operator 2. Updating Fields 3. Updating Arrays

# Document Updating Operator

`$min`

`$max` 

`$mul` 

`$inc` ⇒ increment or decrement of a value.

`$set` ⇒ simply takes a document where you describe some fields that should be changed or added to the existing document. So $set does not. override the existing value instead it simply just defines some changes that MongoDB evaluates then if they make sense, it changes the. an existing document by adding or overriding these fields. All the existing fields are left untouched they are not removed.

`$unset` 

`$slice`

The set changed or added a new document

<details>
  <summary>Inserting data</summary>

```schema
    > use user
    switched to db user

    > db.users.insertMany(
    		[
    			  {
    			    "name": "Max",
    			    "hobbies": [
    			      {
    			        "title": "Sports",
    			        "frequency": 3
    			      },
    			      {
    			        "title": "Cooking",
    			        "frequency": 6
    			      }
    			    ],
    			    "phone": 131782734
    			  },
    			  {
    			    "name": "Manuel",
    			    "hobbies": [
    			      {
    			        "title": "Cooking",
    			        "frequency": 5
    			      },
    			      {
    			        "title": "Cars",
    			        "frequency": 2
    			      }
    			    ],
    			    "phone": "012177972",
    			    "age": 30
    			  },
    			  {
    			    "name": "Anna",
    			    "hobbies": [
    			      {
    			        "title": "Sports",
    			        "frequency": 2
    			      },
    			      {
    			        "title": "Yoga",
    			        "frequency": 3
    			      }
    			    ],
    			    "phone": "80811987291",
    			    "age": null
    			  },
    			  {
    			    "name": "Chris",
    			    "hobbies": ["Sports", "Cooking", "Hiking"]
    			  }
    		]
    )
    **Output**
    {
    	"acknowledged" : true,
    	"insertedIds" : [
    		ObjectId("6325bc0299c5db380a83e0d9"),
    		ObjectId("6325bc0299c5db380a83e0da"),
    		ObjectId("6325bc0299c5db380a83e0db"),
    		ObjectId("6325bc0299c5db380a83e0dc")
    	]
    }
    
    > db.users.find().pretty()
    **Output**
    {
    	"_id" : ObjectId("6325bc0299c5db380a83e0d9"),
    	"name" : "Max",
    	"hobbies" : [
    		{
    			"title" : "Sports",
    			"frequency" : 3
    		},
    		{
    			"title" : "Cooking",
    			"frequency" : 6
    		}
    	],
    	"phone" : 131782734
    }
    {
    	"_id" : ObjectId("6325bc0299c5db380a83e0da"),
    	"name" : "Manuel",
    	"hobbies" : [
    		{
    			"title" : "Cooking",
    			"frequency" : 5
    		},
    		{
    			"title" : "Cars",
    			"frequency" : 2
    		}
    	],
    	"phone" : "012177972",
    	"age" : 30
    }
    {
    	"_id" : ObjectId("6325bc0299c5db380a83e0db"),
    	"name" : "Anna",
    	"hobbies" : [
    		{
    			"title" : "Sports",
    			"frequency" : 2
    		},
    		{
    			"title" : "Yoga",
    			"frequency" : 3
    		}
    	],
    	"phone" : "80811987291",
    	"age" : null
    }
    {
    	"_id" : ObjectId("6325bc0299c5db380a83e0dc"),
    	"name" : "Chris",
    	"hobbies" : [
    		"Sports",
    		"Cooking",
    		"Hiking"
    	]
    }
  ```
</details>

# `updateOne`

Either override or do not update(if given data exactly same as the current data).

This will not update those elements to the existing array through, it will simply override the existing hobbies. (as the structure is not the same as before)

```cpp
> db.users.updateOne(
	{_id: ObjectId("6325bc0299c5db380a83e0dc")}, 
	{
			$set:  
					{
							hobbies:
									[
											{title: 'Sports', frequency: 5},
											{title: 'Cooking', frequency: 3}, 
											{title: 'Hiking', frequency: 1}
									]
					}
	}
)
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

// after above command the cris object will be
{
	"_id" : ObjectId("61c351e8d7b30fa4425c3ca5"),
	"name" : "Chris",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 5
		},
		{
			"title" : "Cooking",
			"frequency" : 3
		},
		{
			"title" : "Hiking",
			"frequency" : 1
		}
	]
}
and so on.
```

If we run same command again then we will get `{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 0 }` as value already same.

# `updateMany`

`$set` **add a new value** or **update existing value** updates the users whose hobby is Sports

```cpp
> db.users.updateMany(
		{'hobbies.title': 'Sports'}, 
		{
				$set: {isSporty: true}
		}
)
**Output**
{ "acknowledged" : true, "matchedCount" : 3, "modifiedCount" : 3 }

// all user except Manuel will get an additional field isSpoty: true(as in hobbies array he has no hobby titled Sports)
// after this users document will be like 
{
	"_id" : ObjectId("61c351e8d7b30fa4425c3ca5"),
	"name" : "Chris",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 5
		},
		{
			"title" : "Cooking",
			"frequency" : 3
		},
		{
			"title" : "Hiking",
			"frequency" : 1
		}
	],
	"isSporty" : true
}
{
	"_id" : ObjectId("61c351e8d7b30fa4425c3ca6"),
	"name" : "Manuel",
	"hobbies" : [
		{
			"title" : "Cooking",
			"frequency" : 5
		},
		{
			"title" : "Cars",
			"frequency" : 2
		}
	],
	"phone" : "012177972",
	"age" : 30
}
{
	"_id" : ObjectId("61c351e8d7b30fa4425c3ca7"),
	"name" : "Max",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 3
		},
		{
			"title" : "Cooking",
			"frequency" : 6
		}
	],
	"phone" : 131782734,
	"isSporty" : true
}
{
	"_id" : ObjectId("61c351e8d7b30fa4425c3ca8"),
	"name" : "Anna",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 2
		},
		{
			"title" : "Yoga",
			"frequency" : 3
		}
	],
	"phone" : "80811987291",
	"age" : null,
	"isSporty" : true
}
```

# `$set`

<aside>
💡 Can update arrays, one or multiple fields, and embedded documents.

</aside>

Adding(as those fields were not present before) multiple elements using `$set` Chris

```cpp
> db.users.updateOne(
		{_id: ObjectId("61c351e8d7b30fa4425c3ca5")}, 
		{
				$set: 
						{
								age: 40, 
								phone: 082344289399
						}
		}
)
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

> db.users.find({_id: ObjectId("61c351e8d7b30fa4425c3ca5")}).pretty()
**Output**
{
	"_id" : ObjectId("61c351e8d7b30fa4425c3ca5"),
	"name" : "Chris",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 5
		},
		{
			"title" : "Cooking",
			"frequency" : 3
		},
		{
			"title" : "Hiking",
			"frequency" : 1
		}
	],
	"isSporty" : true,
	"age" : 40,
	"phone" : 82344289399
}
```

# `$inc`

<aside>
💡 Can manually increment or decrement the value of field/fields of documents.

</aside>

```cpp
> db.users.updateOne(
		{name: "Manuel"}, 
		{
				$inc: {age: 2}
		}
) //first match document
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

> db.users.find({name: "Manuel"}).pretty()
**Output**
{
	"_id" : ObjectId("61c351e8d7b30fa4425c3ca6"),
	"name" : "Manuel",
	"hobbies" : [
		{
			"title" : "Cooking",
			"frequency" : 5
		},
		{
			"title" : "Cars",
			"frequency" : 2
		}
	],
	"phone" : "012177972",
	"age" : 32
}

> db.users.updateOne({name: "Manuel"}, {$inc: {age: -2}})
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

> db.users.find({name: "Manuel"}).pretty()
**Output**
{
	"_id" : ObjectId("61c351e8d7b30fa4425c3ca6"),
	"name" : "Manuel",
	"hobbies" : [
		{
			"title" : "Cooking",
			"frequency" : 5
		},
		{
			"title" : "Cars",
			"frequency" : 2
		}
	],
	"phone" : "012177972",
	"age" : 30
}
```

Also, can do with multiple update queries.

```cpp
> db.users.updateOne(
		{name: "Manuel"}, 
		{
				$inc: {age: -2}, 
				$set:{isSporty: false}
		}
)
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

> db.users.find({name: "Manuel"}).pretty()
**Output**
{
	"_id" : ObjectId("61c351e8d7b30fa4425c3ca6"),
	"name" : "Manuel",
	"hobbies" : [
		{
			"title" : "Cooking",
			"frequency" : 5
		},
		{
			"title" : "Cars",
			"frequency" : 2
		}
	],
	"phone" : "012177972",
	"age" : 28,
	"isSporty" : false
}
```

Two operations are not allowed into the same fields.

```cpp
> db.users.updateOne(
		{name: "Manuel"}, 
		{
				$inc: {age: -2}, 
				$set: {age: 30}
		}
)

2022-09-17T19:22:21.646+0600 E QUERY    [thread1] WriteError: Updating the path 'age' would create a conflict at 'age' :
WriteError({
	"index" : 0,
	"code" : 40,
	"errmsg" : "Updating the path 'age' would create a conflict at 'age'",
	"op" : {
		"q" : {
			"name" : "Manuel"
		},
		"u" : {
			"$inc" : {
				"age" : -2
			},
			"$set" : {
				"age" : 30
			}
		},
		"multi" : false,
		"upsert" : false
	}
})
WriteError@src/mongo/shell/bulk_api.js:466:48
Bulk/mergeBatchResults@src/mongo/shell/bulk_api.js:846:49
Bulk/executeBatch@src/mongo/shell/bulk_api.js:910:13
Bulk/this.execute@src/mongo/shell/bulk_api.js:1154:21
DBCollection.prototype.updateOne@src/mongo/shell/crud_api.js:572:17
@(shell):1:1
```

# `$min` `$max` `$mul`

<aside>
💡 `$min` ⇒ Set the minimum of the current and given value for the selected field.
`$max` ⇒ Set the maximum of the current and given value for the selected field.
`$mul` ⇒ Set the selected value by multiplying by the given value.

</aside>

 Update the age value with min value(final value will be minimum of current and given value)
As the current value is 37 and the given value is 35 then after this command age will be 35 minimum among them.

```cpp
> db.users.updateOne({name: "Chris"}, {$min: {age: 35}})
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

> db.users.find({name: "Chris"}).pretty()
**Output**
{
	"_id" : ObjectId("61c351e8d7b30fa4425c3ca5"),
	"name" : "Chris",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 5
		},
		{
			"title" : "Cooking",
			"frequency" : 3
		},
		{
			"title" : "Hiking",
			"frequency" : 1
		}
	],
	"isSporty" : true,
	"age" : 35,
	"phone" : 82344289399
}
```

Will not update as the current value is 35 which is the minimum between the given and current value

```cpp
db.users.updateOne({name: "Chris"}, {$min: {age: 39}})
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 0 }
```

Will be updated as given value 39 is max between current value 35 and given value 39

```cpp
db.users.updateOne({name: "Chris"}, {$max: {age: 39}})
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

db.users.find({name: "Chris"}).pretty()
{
"_id" : ObjectId("61c351e8d7b30fa4425c3ca5"),
"name" : "Chris",
"hobbies" : [
{
"title" : "Sports",
"frequency" : 5
},
{
"title" : "Cooking",
"frequency" : 3
},
{
"title" : "Hiking",
"frequency" : 1
}
],
"isSporty" : true,
"age" : 39,
"phone" : 82344289399
}
```

Multiply age by a number specify 10 %

```cpp
> db.users.updateOne({name: "Chris"}, {$mul: {age: 1.1}})
```

# Updating Fields(not field value)

<aside>
💡 `$unset` ⇒ To delete a field.
`$rename` ⇒ To rename a field.
`$upsert`
                  false ⇒ Will does not create a document or field if does not exits in the update operation. This is the default value for the upsert.
                  true ⇒ Will create a document or field if does not exits in the update operation.

</aside>

`$upsert`

Renaming fields

,`$set`, upsert

### Getting Rid of Fields

Want to drop all value on all persons who are sporty(it will set the phone to null nothing else)

```cpp
> db.users.updateMany({isSporty: true}, {$set: {phone: null}})
**Output**
{ "acknowledged" : true, "matchedCount" : 3, "modifiedCount" : 3 }

> db.users.find({}, {phone: 1}).pretty()
**Output**
{ "_id" : ObjectId("61c351e8d7b30fa4425c3ca5"), "phone" : null }
{ "_id" : ObjectId("61c351e8d7b30fa4425c3ca6"), "phone" : "012177972" }
{ "_id" : ObjectId("61c351e8d7b30fa4425c3ca7"), "phone" : null }
{ "_id" : ObjectId("61c351e8d7b30fa4425c3ca8"), "phone" : null }
```

To get rid of fields(delete the field) the value of the phone will be simply ignored

```cpp
> db.users.updateMany({isSporty: true}, {$unset: {phone: null}})
**Output**
{ "acknowledged" : true, "matchedCount" : 3, "modifiedCount" : 3 }

> db.users.find({}, {phone: 1}).pretty()
**Output**
{ "_id" : ObjectId("61c351e8d7b30fa4425c3ca5") }
{ "_id" : ObjectId("61c351e8d7b30fa4425c3ca6"), "phone" : "012177972" }
{ "_id" : ObjectId("61c351e8d7b30fa4425c3ca7") }
{ "_id" : ObjectId("61c351e8d7b30fa4425c3ca8") }
```

The value in the phone will be simply ignored

```cpp
> db.users.updateMany({isSporty: true}, {$unset: {phone: ''}})
```

### Renaming Fields

Will try to rename all 4 documents but Max does not have an age field so there will be 3 rename.

```cpp
> db.users.updateMany({}, {$rename: {age: 'totalAge'}})
**Output**
{ "acknowledged" : true, "matchedCount" : 4, "modifiedCount" : 3 }

> db.users.find({}, {name: 1, totalAge: 1, _id: 0}).pretty()
**Output**
{ "name" : "Chris", "totalAge" : 39 }
{ "name" : "Manuel", "totalAge" : 28 }
{ "name" : "Max" }
{ "name" : "Anna", "totalAge" : null }
```

### `$upsert`

<aside>
💡 Combination of update and insert.

</aside>

Default insert and update(as there is no document where the name is Maria update will not happen)

```cpp
> db.users.updateOne({name: 'Maria'}, {$set: {age: 29, hobbies: [{title: 'Good food', frequency: 3}], isSporty: true}})
{ "acknowledged" : true, "matchedCount" : 0, "modifiedCount" : 0 }
// above command is same as the command below
> db.users.updateOne({name: 'Maria'}, {$set: {age: 29, hobbies: [{title: 'Good food', frequency: 3}], isSporty: true}}, {upsert: false})
```

Want to update some fields but do not know if they exist.

1. If it exists then override the document.
2. If it does not exist then create a new document.

`{upsert: true}` ⇒ As there is no document whose name is Maria so there will be no update instead it will add a new document to the database. Also will add `name: "Maria"`

```cpp
> db.users.updateOne({name: 'Maria'}, {$set: {age: 29, hobbies: [{title: 'Good food', frequency: 3}], isSporty: true}}, {upsert: true})
{
	"acknowledged" : true,
	"matchedCount" : 0,
	"modifiedCount" : 0,
	"upsertedId" : ObjectId("61c40f432340a6fed0561b0f")
}
> db.users.find({_id: ObjectId("61c40f432340a6fed0561b0f")}).pretty()
{
	"_id" : ObjectId("61c40f432340a6fed0561b0f"),
	"name" : "Maria",
	"age" : 29,
	"hobbies" : [
		{
			"title" : "Good food",
			"frequency" : 3
		}
	],
	"isSporty" : true
}
```

# Updating Arrays

.$ 

$[] 

`$push` 

`$pop` 

`$pull` 

`$each` 

`$addToSet`

 

Want to find a certain amount of persons and persons based on the hobbies array

Want to check all query conditions on each array element but it checks query conditions in multiple array elements at a time.

Want to find all people whose hobby is Sports and frequency is greater than or equal to 3 for all hobbies. But for Anna in first hobby title is Sports but the frequency is greater than or equal to 3 in the second array object. This find does not check the condition in a single document but also multiple documents.

```cpp
> db.users.find(
		{
				$and: [
						{'hobbies.title': 'Sports'},
						{'hobbies.frequency': {$gte:3}}
				]
		}, 
		{
				hobbies: 1, 
				name: 1, 
				_id: 0
		}
)
**Output**
{ "name" : "Chris", "hobbies" : [ { "title" : "Sports", "frequency" : 5 }, { "title" : "Cooking", "frequency" : 3 }, { "title" : "Hiking", "frequency" : 1 } ] }
{ "name" : "Max", "hobbies" : [ { "title" : "Sports", "frequency" : 3 }, { "title" : "Cooking", "frequency" : 6 } ] }
{ "name" : "Anna", "hobbies" : [ { "title" : "Sports", "frequency" : 2 }, { "title" : "Yoga", "frequency" : 3 } ] }
```

`$elemMatch` will match all conditions in a single array element not also in multiple documents. If all array elements satisfy this then will return that document. 

```bash
> db.users.find({hobbies: {$elemMatch: {title: 'Sports',frequency: {$gte:3}}}})
**Output**
{ "_id" : ObjectId("61c351e8d7b30fa4425c3ca5"), "name" : "Chris", "hobbies" : [ { "title" : "Sports", "frequency" : 5 }, { "title" : "Cooking", "frequency" : 3 }, { "title" : "Hiking", "frequency" : 1 } ], "isSporty" : true, "totalAge" : 39 }
{ "_id" : ObjectId("61c351e8d7b30fa4425c3ca7"), "name" : "Max", "hobbies" : [ { "title" : "Sports", "frequency" : 3 }, { "title" : "Cooking", "frequency" : 6 } ], "isSporty" : true }
```

### Updating one matched array of elements

Here `.$` ⇒ This will automatically refer to the element in our filter as I want to update the element in hobbies that matched the condition
`$` ⇒ Dollar sign is a placeholder here

Adding new field

<details>
<summary>Before adding the field was</summary>

```schema
    { 
    		"_id" : ObjectId("61c351e8d7b30fa4425c3ca7"), 
    		"name" : "Max", 
    		"hobbies" : [ 
    				{ "title" : "Sports", "frequency" : 3 }, 
    				{ "title" : "Cooking", "frequency" : 6 } 
    		], 
    		"isSporty" : true 
    }
  ```
</details>

```bash
> db.users.updateMany(
		{
				hobbies: {
						$elemMatch: {
								title: 'Sports',
								frequency: {$gte:3}
						}
				}
		}, 
		{
				$set : {"hobbies.$.highFrequency": true}
		}
)
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

> db.users.find({hobbies: {$elemMatch: {title: 'Sports',frequency: {$gte:3}}}})
**Output**
{ 
		"_id" : ObjectId("6325bc0299c5db380a83e0d9"), 
		"name" : "Max", 
		"hobbies" : [ 
				{ "title" : "Sports", "frequency" : 3, "highFrequency" : true }, 
				{ "title" : "Cooking", "frequency" : 6 } 
		], 
		"phone" : 131782734 
}

// {$set : {hobbies: something }} => will replace the whole hobbies document
// {$set: {"hobbies.$": {title: "Sports", frequency: 5}}} => will change all hobbies object in the array
```

### Updating All Array Elements

```cpp
db.users.find({'hobbies.frequency': {$gt: 2}}).pretty()
db.users.find({'hobbies.frequency': {$gt: 2}}).count() //5
```

Updating the elements
But this won't work properly. It will update first matching element in the hobbies array, as Chris also has Cooking hobby which has frequency 3 but this hobby does not get `"goodFrequency" : true`

```bash
> db.users.updateMany({'hobbies.frequency': {$gt: 2}}, {$set: {'hobbies.$.goodFrequency': true}})
**Output**
{ "acknowledged" : true, "matchedCount" : 5, "modifiedCount" : 5 }

> db.users.find({}, {name: 1, hobbies: 1, _id: 0}).pretty()
**Output**
{
	"name" : "Chris",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 5,
			"highFrequency" : true,
			"goodFrequency" : true
		},
		{
			"title" : "Cooking",
			"frequency" : 3 //not get updated
		},
		{
			"title" : "Hiking",
			"frequency" : 1
		}
	]
}
{
	"name" : "Manuel",
	"hobbies" : [
		{
			"title" : "Cooking",
			"frequency" : 5,
			"goodFrequency" : true
		},
		{
			"title" : "Cars",
			"frequency" : 2
		}
	]
}
{
	"name" : "Max",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 3,
			"highFrequency" : true,
			"goodFrequency" : true
		},
		{
			"title" : "Cooking",
			"frequency" : 6 //not get updated
		}
	]
}
{
	"name" : "Anna",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 2
		},
		{
			"title" : "Yoga",
			"frequency" : 3,
			"goodFrequency" : true
		}
	]
}
{
	"name" : "Maria",
	"hobbies" : [
		{
			"title" : "Good food",
			"frequency" : 3,
			"goodFrequency" : true
		}
	]
}
```

### Updating element of array

let's say if `totalAge` is greater than 30 then we want to update every array of elements

Will not override
`.$[]` ⇒ update all array elements and for each element because we have an embedded document.

```cpp
> db.users.updateMany({totalAge: {$gt: 30}}, {$inc: {'hobbies.$[].frequency': -1}})
```

Want to find all hobbies with a frequency greater than 2

```cpp
db.users.find({'hobbies.frequency': {$gt: 2}}).pretty()
```

`$` ⇒ First matching element,`.$[]` ⇒ For all elements,`.$[el]` ⇒ For only matched document

`el` ⇒ is an identifier for every item of the array into documents
`{'hobbies.frequency': {$gt: 2}}` ⇒ this filter identify documents
`{'el.frequency': {$gt: 2}}` ⇒ this filter which identify array elements in which update will occurs
Those two conditions do not need to be the same.

```cpp
> db.users.updateMany(
		{
				'hobbies.frequency': {$gt: 2}
		}, 
		{
				$set: {
					'hobbies.$[el].goodFrequency': true}
		}, 
		{
				arrayFilters: [
						{'el.frequency': {$gt: 2}}
				]
		}
)
**Output**
{ "acknowledged" : true, "matchedCount" : 4, "modifiedCount" : 2 }
```

### Adding Elements to Arrays

One element

```cpp
> db.users.updateOne(
		{name: 'Maria'}, 
		{
				$push: {
						hobbies: {title: 'Sports', frequency: 2}
				}
		}
)
```

More than one element 

`$push` is also used with more than one document

`$sort` ⇒ Sort elements in the array in `$each` before push

```cpp
> db.users.updateOne(
		{name: 'Maria'}, 
		{
				$push: {
						hobbies: {
								$each: [
										{title: 'Good Wine', frequency: 1}, 
										{title: 'Hiking', frequency: 2}
								], 
								$sort: {frequency: -1}
						}
				}
		}
)
```
    

Also can add specified amount of element from `$each` array. Such as `$sort: {frequency: -1}, $slice: 1` will first sort array in `$each` by there frequency and then will return first element. `$slice: n` have to replace n with number and `$slice` will return first n numbers of element from the array.

## Removing/Delete Elements from array


>💡 `$pull` describes an object that we want to what/how to pull

What to remove every element where title is “Hiking”

```cpp
db.users.updateOne({name: 'Maria'}, {$pull: {hobbies: {title: 'Hiking'}}})
```
    

### Remove the last element of an array

```cpp
db.users.updateOne({name: 'Chris'}, {$pop: {hobbies: 1}})
```

### Remove the first element of an array

```cpp
db.users.updateOne({name: 'Chris'}, {$pop: {hobbies: -1}})
```

### `$addToSet`

`$addToSet` adds unique value only. Its basically adding a new element. But if the element already exists (have to exact the same) it does not update.

```cpp
db.users.updateOne({name: 'Maria'}, {$addToSet: {hobbies: {title: 'Hiking', frequency: 2}}})
```