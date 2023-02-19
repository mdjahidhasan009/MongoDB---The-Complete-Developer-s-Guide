# Bucket


>💡 Categorizes incoming documents into groups, called buckets, based on a specified expression and bucket boundaries and outputs a document per each bucket. Also can perform some statistics.


Let's prepare a bucket stage, using bucket can create a different categories and filter

Boundaries means range/levels like 0-18, 18-30,30-50, 50-80, 80-120, in every range includes first value execute not last value, 18-30 ⇒ means 18 includes in the range but not includes 30. There are noting in 0 - 18 and 80 to 120 range so the following query give as only three bucket.

> `{ "_id" : 18, "numPersons" : 868, "average" : 25.101382488479263 } => 18 to less than 30` <br/>
> `{ "_id" : 30, "numPersons" : 1828, "average" : 39.4917943107221 } => 30 to less than 59` <br/>
> `{ "_id" : 50, "numPersons" : 2304, "average" : 61.46440972222222 } => 50 to all upperbound`

```cpp
> db.persons.aggregate([
    {
        $bucket: {
            groupBy: '$dob.age',
            boundaries: [0, 18, 30, 50, 80, 120],
            output: {
                numPersons: { $sum: 1 },
                average: { $avg: '$dob.age' },
            }
        }
    }
]).pretty()

**Output**
{ "_id" : 18, "numPersons" : 868, "average" : 25.101382488479263 } 
{ "_id" : 30, "numPersons" : 1828, "average" : 39.4917943107221 }
{ "_id" : 50, "numPersons" : 2304, "average" : 61.46440972222222 }
```

There are no people less than 18 and greater than 80 and equals to 80. Output noting as no data satisfy those conditions.

```cpp
> db.persons.find({'dob.age': {$lt: 18}})
**Output**

> db.persons.find({'dob.age': {$gt: 80}})
**Output**

> db.persons.find({'dob.age': 80})
**Ouput**
```

```cpp
> db.persons.find({'dob.age': {$gt: 17, $lt: 30}}).count()
868

> db.persons.find({'dob.age': {$gt: 29, $lt: 50}}).count()
1828 

> db.persons.find({'dob.age': {$gt: 49, $lt: 80}}).count()
2304
```

Adding more levels

> `{ _id: 18, numPersons: 868, average: 25.101382488479263 }, => 18 to less than 30`<br/>
> `{ _id: 30, numPersons: 910, average: 34.51758241758242 }, => 30 to less than 40`<br/>
> `{ _id: 40, numPersons: 918, average: 44.42265795206972 }, => 40 to less than 50`<br/>
> `{ _id: 50, numPersons: 976, average: 54.533811475409834 }, => 50 to less than 60`<br/>
> `{ _id: 60, numPersons: 1328, average: 66.55798192771084 } ⇒ 60 to all upper-bound`

```cpp
> db.persons.aggregate([
    {
        $bucket: {
            groupBy: '$dob.age',
            boundaries: [18, 30, 40, 50, 60, 120],
            output: {
                numPersons: { $sum: 1 },
                average: { $avg: '$dob.age' },
            }
        }
    }
]).pretty()

**Output**
[
  { _id: 18, numPersons: 868, average: 25.101382488479263 },
  { _id: 30, numPersons: 910, average: 34.51758241758242 },
  { _id: 40, numPersons: 918, average: 44.42265795206972 },
  { _id: 50, numPersons: 976, average: 54.533811475409834 },
  { _id: 60, numPersons: 1328, average: 66.55798192771084 }
]
```

Can also create a auto bucket by defining how many buckets want, almost have equal distributions in each buckets.

```cpp
> db.persons.aggregate([
    {
        $bucketAuto: {
            groupBy: '$dob.age',
            buckets: 5,
            output: {
                numPersons: { $sum: 1 },
                average: { $avg: '$dob.age' },
            }
        }
    }
]).pretty()

{
	"_id" : {
		"min" : 21,
		"max" : 32
	},
	"numPersons" : 1042,
	"average" : 25.99616122840691
}
{
	"_id" : {
		"min" : 32,
		"max" : 43
	},
	"numPersons" : 1010,
	"average" : 36.97722772277228
}
{
	"_id" : {
		"min" : 43,
		"max" : 54
	},
	"numPersons" : 1033,
	"average" : 47.98838334946757
}
{
	"_id" : {
		"min" : 54,
		"max" : 65
	},
	"numPersons" : 1064,
	"average" : 58.99342105263158
}
{
	"_id" : {
		"min" : 65,
		"max" : 74
	},
	"numPersons" : 851,
	"average" : 69.11515863689776
}
```