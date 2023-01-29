# Group

# Understanding the Group Stage

**The group stage allows a group of data by certain fields or by multiple fields.** Have to add `$` sign before selected query document, here accumulate by 
1 --> increasing value 
-1 -> decreasing value

`totalPersons` is the value that how many people are in the same state
`_id` is a unique value ⇒ take which field want to group will be a document.
can not use group into `find()` method
`$location.state`  means iterating every element

```cpp
db.persons.aggregate([
    { $match: { gender: 'female' } },
    { $group: 
				{ _id: 
						{ state: "$location.state" }, 
						totalPersons: { $sum: 1}
				}
		}
]).pretty()
```

This is the group stage in action
Here we get the data with unsorted order
Can also be sorted

```cpp
{ "_id" : { "state" : "berkshire" }, "totalPersons" : 1 }
{ "_id" : { "state" : "michigan" }, "totalPersons" : 1 }
{ "_id" : { "state" : "county down" }, "totalPersons" : 1 }
{ "_id" : { "state" : "loiret" }, "totalPersons" : 1 }
{ "_id" : { "state" : "cornwall" }, "totalPersons" : 2 }
{ "_id" : { "state" : "sivas" }, "totalPersons" : 1 }
{ "_id" : { "state" : "uşak" }, "totalPersons" : 1 }
{ "_id" : { "state" : "sinop" }, "totalPersons" : 3 }
{ "_id" : { "state" : "marne" }, "totalPersons" : 1 }
{ "_id" : { "state" : "northumberland" }, "totalPersons" : 1 }
{ "_id" : { "state" : "leicestershire" }, "totalPersons" : 1 }
{ "_id" : { "state" : "puy-de-dôme" }, "totalPersons" : 1 }
{ "_id" : { "state" : "maryland" }, "totalPersons" : 1 }
{ "_id" : { "state" : "ardèche" }, "totalPersons" : 1 }
{ "_id" : { "state" : "ankara" }, "totalPersons" : 3 }
{ "_id" : { "state" : "dordogne" }, "totalPersons" : 1 }
{ "_id" : { "state" : "antalya" }, "totalPersons" : 1 }
{ "_id" : { "state" : "corrèze" }, "totalPersons" : 1 }
{ "_id" : { "state" : "ardennes" }, "totalPersons" : 1 }
{ "_id" : { "state" : "bas-rhin" }, "totalPersons" : 2 }
Type "it" for more
```

To check aggregation function work correctly

```cpp
db.persons.find({'location.state': 'sinop', gender: 'female'}).count()
3
```

Let also sort the group stage values according to `totalPersons` when executing the query
Sorting is done from the previous stage. `$sort` has the data of `$group` stage and `$group` state has data of `$match` state. In normal `find`, we can not perform operations on the previous data stage. We have to sort in the client side for the `find`. But using aggregate we are doing this in MongoDB shell.

```cpp
db.persons.aggregate([     
		{ $match: { gender: 'female' } },     
		{ $group: { 
				_id: { state: "$location.state" }, 
				totalPersons: { $sum: 1 } } 
		},     
		{ $sort: { totalPersons: -1 } } 
]).pretty()
**Output**
{ "_id" : { "state" : "midtjylland" }, "totalPersons" : 33 }
{ "_id" : { "state" : "nordjylland" }, "totalPersons" : 27 }
{ "_id" : { "state" : "new south wales" }, "totalPersons" : 24 }
{ "_id" : { "state" : "australian capital territory" }, "totalPersons" : 24 }
{ "_id" : { "state" : "syddanmark" }, "totalPersons" : 24 }
{ "_id" : { "state" : "south australia" }, "totalPersons" : 22 }
{ "_id" : { "state" : "hovedstaden" }, "totalPersons" : 21 }
{ "_id" : { "state" : "danmark" }, "totalPersons" : 21 }
{ "_id" : { "state" : "queensland" }, "totalPersons" : 20 }
{ "_id" : { "state" : "overijssel" }, "totalPersons" : 20 }
{ "_id" : { "state" : "sjælland" }, "totalPersons" : 19 }
{ "_id" : { "state" : "nova scotia" }, "totalPersons" : 17 }
{ "_id" : { "state" : "canterbury" }, "totalPersons" : 16 }
{ "_id" : { "state" : "northwest territories" }, "totalPersons" : 16 }
{ "_id" : { "state" : "gelderland" }, "totalPersons" : 16 }
{ "_id" : { "state" : "yukon" }, "totalPersons" : 16 }
{ "_id" : { "state" : "bayern" }, "totalPersons" : 15 }
{ "_id" : { "state" : "northern territory" }, "totalPersons" : 15 }
{ "_id" : { "state" : "tasmania" }, "totalPersons" : 15 }
{ "_id" : { "state" : "noord-brabant" }, "totalPersons" : 14 }
Type "it" for more
```

Check if the answer is correctly

```cpp
db.persons.find({'location.state': 'midtjylland', gender: 'female'}).count()
33
```