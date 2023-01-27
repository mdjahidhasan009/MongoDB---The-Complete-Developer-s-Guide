# Element

`$exists` `$type`

### `$exists`

- Inserting data
    
    ```bash
    > db.users.insertMany([
    		{
    				name: 'Max', 
    				hobbies: [
    					  {title: 'Sports', frequency: 3},
    						{title: 'Cooking', frequency: 6}
    			  ], 
    				phone: 0123495334
    		},
    		{
    				name: 'Manuel', 
    				hobbies: [
    						{title: 'Cooking', frequency: 5},
    						{title: 'Cars', frequency: 6}
    				], 
    				phone: '043453495334', 
    				age: 30
    		}
    ])
    **Output**
    {
    	"acknowledged" : true,
    	"insertedIds" : [
    		ObjectId("5f172a343a76a40cd42b836a"),
    		ObjectId("5f172a343a76a40cd42b836b")
    	]
    }
    
    > db.users.find().pretty()
    {
    	"_id" : ObjectId("5f172a343a76a40cd42b836a"),
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
    	"phone" : 123495334
    }
    {
    	"_id" : ObjectId("5f172a343a76a40cd42b836b"),
    	"name" : "Manuel",
    	"hobbies" : [
    		{
    			"title" : "Cooking",
    			"frequency" : 5
    		},
    		{
    			"title" : "Cars",
    			"frequency" : 6
    		}
    	],
    	"phone" : "043453495334",
    	"age" : 30
    }
    ```
    

Here checking whether an element exists or not

```bash
 > db.users.find({age: {$exists: true}}).pretty()
**Output**
{
	"_id" : ObjectId("5f172a343a76a40cd42b836b"),
	"name" : "Manuel",
	"hobbies" : [
		{
			"title" : "Cooking",
			"frequency" : 5
		},
		{
			"title" : "Cars",
			"frequency" : 6
		}
	],
	"phone" : "043453495334",
	"age" : 30
}
```

Can also check multiple logic element age exits and also greater than 30

```cpp
> db.users.find({age: {$exists: true, $gt: 30}}).pretty()
> db.users.find({age: {$exists: true, $gte: 30}}).pretty()
```

`null` ⇒ `true` for `$exists: true` but `false` for `$exists: false`

```bash
// if the element value is null then it also be exists(
> db.users.insertMany([{name: 'Anna', hobbies: [{title: 'Sports', frequency: 2},{title: 'Yoga', frequency: 3}], phone: 01234953345, age: null}])
**Output**
{
	"acknowledged" : true,
	"insertedIds" : [
		ObjectId("5f172c593a76a40cd42b836c")
	]
}

> db.users.find({age: {$exists: true}}).pretty()
**Output**
{
	"_id" : ObjectId("62e167dbc5b6842cf5a061c0"),
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
	"phone" : 1234953345,
	"age" : null
}
```

But if we check with exists value is false and if an element value has null but exists then it also does not show

```cpp
> db.users.find({age: {$exists: false}}).pretty()
**Output** => No output as age is null
```

Checking is age exits and the value is not `null`

```bash
> db.users.find({age: {$exists: true, $ne: null}}).pretty()
```

# Types

```bash
Type 	                    Number       Alias           	Notes
Double    	              1         “double”              	 
String   	                2 	      “string” 	 
Object  	                3 	      “object” 	 
Array 	                  4 	      “array” 	 
Binary data 	            5 	      “binData” 	 
Undefined 	              6 	      “undefined” 	       Deprecated.
ObjectId 	                7 	      “objectId” 	 
Boolean 	                8 	      “bool” 	 
Date 	                    9 	      “date” 	 
Null 	                    10 	      “null” 	 
Regular Expression        11 	      “regex” 	 
DBPointer 	              12 	      “dbPointer” 	       Deprecated.
JavaScript 	              13 	      “javascript” 	 
Symbol 	                  14 	      “symbol” 	           Deprecated.
JavaScript (with scope)   15 	      “javascriptWithScope” 	 
32-bit integer 	          16 	      “int” 	 
Timestamp 	              17 	      “timestamp” 	 
64-bit integer 	          18 	      “long” 	 
Decimal128 	              19 	      “decimal” 	       New in version 3.4.
Min key 	                -1 	      “minKey” 	 
Max key 	                127 	    “maxKey”
```

### Checking with alias

Will return document which has key phone which value is number type.

```bash
// checking with alias
> db.users.find({phone: {$type: 'number'}}).pretty()
```

As **shell** is **based** **on** **JavaScript** **number** and **double** would be the **same answer**. In the **database**, the number is stored into a **floating point** number as **double**. JS drivers only know it always double

```cpp
> db.users.find({phone: {$type: 'double'}}).pretty()
```

Also can add multiple types of double or string(both cases true)

```cpp
> db.users.find({phone: {$type: ['double','string']}}).pretty()
```