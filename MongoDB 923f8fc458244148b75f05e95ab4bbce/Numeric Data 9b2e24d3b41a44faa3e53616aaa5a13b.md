# Numeric Data

# Working With Numeric Data

Numeric data is most important in scientific calculation
Number more complex than any other
3 types of number in mongoDB.(Integers,Longs,Doubles)

### Integers(int32)

1. Only full Numbers(+- 2^32). **-2,147,483,648 to +2,147,483,647**
2. Use for 'normal' Integers

### Longs(int64)

1. Only full Numbers(+- 2^64). **-9,223,372,036,854,775,808 to +9,223,372,036,854,775,807**
2. Use for large Integers

### Doubles(64bit)

1. Numbers with Decimal Places(Decimal values are approximated). 
2. Use for floats where high precision is not required

### High Precision Doubles(128bit)

1. Numbers with Decimal Places(Decimal values are stored with high precision(**34 decimal digits**)).
2. Use for floats where high precision is required

In mongoDB driver is a javaScript based driver. All numeric values stored as a double. Such as 74 is stored as double and also 74.00 stored as double in MongoDB shell. But in Python 55 is int and 55.0 is float.

In this  case age of Max maybe store as 29.0000002 and will be some imprecision at some point. But we do not see this but if we use MongoDB shell this will save number like this. [Proof](Numeric%20Data%209b2e24d3b41a44faa3e53616aaa5a13b.md)

```bash
> use numeric
switched to db numeric
> db.persons.insertOne({name: 'Max', age: 29})

**Output**
{
  acknowledged: true,
  insertedId: ObjectId("634c030a4ff2758a4da7409c")
}

> db.persons.find()

Ouput
[ { _id: ObjectId("634c030a4ff2758a4da7409c"), name: 'Max', age: 29 } ]
```

# Working with Int32

> Hided all others fields of stats() function as we do not interested on those.
> 

Here can see the size

```cpp
> db.persons.stats()

**Output**
	"size" : 49,
	"count" : 1,

> db.persons.deleteMany({})
**Output**
{ acknowledged: true, deletedCount: 1 }
```

Here can see that size is decrease

```bash
> db.persons.insertOne({ age: 29})

**Output**
{
  acknowledged: true,
  insertedId: ObjectId("634c03b14ff2758a4da7409d")
}

> db.persons.stats()

**Output**
		"size" : 35,
		"count" : 1,

> db.persons.deleteMany({})

**Output**
{ acknowledged: true, deletedCount: 1 }
```

Here also can see that size now also more decrease. We can store number with and without quotation. Using `NumberInt` we are saving as integer and saving some space. 

> `NumberInt` is only available for MongoDB shell it will different for other programming language driver.
> 

```cpp
> db.persons.insertOne({ age: NumberInt(29)})
> db.persons.insertOne({ age: NumberInt("29")})

**Output**
	"size" : 31,
	"count" : 1,
```

# Working with Int64

Here can the output is a wrong value as enter very big value which Int32 can not store.

```cpp
> db.companies.insertOne({valuation: NumberInt('50000000000')})

**Output**
{
  acknowledged: true,
  insertedId: ObjectId("634c06f14ff2758a4da7409e")
}

> db.companies.findOne()

**Output**
{ _id: ObjectId("634c06f14ff2758a4da7409e"), valuation: -1539607552 }
```

If decrease value then it works

```cpp
> db.companies.insertOne({valuation: NumberInt('500000000')})

**Output**
{
  acknowledged: true,
  insertedId: ObjectId("634c087d4ff2758a4da740a0")
}
```

But if the data stored as a 64 bit double then it works as 64 bit double can store more than 32 bit int.

```cpp
> db.companies.insertOne({valuation: 50000000000'})
```

```cpp
> db.companies.find()
{ "_id" : ObjectId("5f3e81e7d0209e4d3a0ec072"), "valuation" : -1539607552 }
{ "_id" : ObjectId("5f3e8635d0209e4d3a0ec073"), "valuation" : 50000000000 }
{ "_id" : ObjectId("5f3e866cd0209e4d3a0ec074"), "valuation" : 705032704 }
```

To store biggest possible number as Integers, always have to use quotation marks, basically it take number as double and than convert into given type(in this case NumberLong). So if given number is not fit in Double than in older version of MongoDB will throw error and in updated/newer version will give warning.

> For my case it give warning and store number currectly but in course (as it was older version) give error.
> 

```cpp
> db.companies.insertOne({valuation: NumberLong(9223372036854775807)})

**Output**
Warning: NumberLong: specifying a number as argument is deprecated and may lead to loss of precision, pass a string instead
{
  acknowledged: true,
  insertedId: ObjectId("634c0a114ff2758a4da740a3")
}

> db.companies.find()
{ "_id" : ObjectId("5f3e81e7d0209e4d3a0ec072"), "valuation" : -1539607552 }
{ "_id" : ObjectId("5f3e8635d0209e4d3a0ec073"), "valuation" : 50000000000 }
{ "_id" : ObjectId("5f3e866cd0209e4d3a0ec074"), "valuation" : 705032704 }
{ "_id" : ObjectId("5f3e86ecd0209e4d3a0ec075"), "valuation" : NumberLong("50000000000") }
```

Doing Maths with Floats Int32 Int64

> We can store integer value as string but problem is we can not perform any number related operation if we store number as string.
> 

```cpp
> db.accounts.insertOne( {name: 'Max', amount: '34234253458373534574524524'} )
// add a small number
> db.accounts.insertOne( {name: 'Max', amount: '10'} )
```

`$inc` or any math calculation does not work with string value

```cpp
> db.accounts.updateOne({}, {$inc: {amount: 10}})

**Output**
MongoServerError: Cannot apply $inc to a value of non-numeric type. {_id: ObjectId('634c0e1f4ff2758a4da740a4')} has the field 'amount' of non-numeric type string
```

Have to insert a Integers value

```cpp
> db.accounts.deleteMany()
> db.accounts.insertOne( { name: 'Max', amount: NumberInt('10') } )

Ouput
{
  acknowledged: true,
  insertedId: ObjectId("634c120e4ff2758a4da740a5")
}

> db.accounts.find().pretty()
```

Here 10 as a double value mongoDB convert the sum as double.

```cpp
db.accounts.updateOne({}, {$inc: {amount: 10}})
```

If update the number with wrapping with NumberInt then the final output be a int.

> But MongoDB print integer and double same way so by printing those in shell we can not see any difference.
> 

```cpp
db.accounts.updateOne({}, {$inc: {amount: NumberInt('10')}})
```

Let delete the document

```cpp
db.companies.deleteMany({})
```

Insert a large number

```cpp
db.companies.insertOne({valuation: NumberLong('34234253458373534574524524')})
```

To calculate math operation with the large number NumberLong should be include in that number, this is incorrect

```cpp
db.companies.updateOne({}, {$inc: {valuation: 1}})
// this is correct
db.companies.updateOne({}, {$inc: {valuation: NumberLong('1')}})
```

# What's Wrong With Normal Doubles

```bash
> db.science.insertOne({a: 0.3, b: 0.1})

**Output**
{
  acknowledged: true,
  insertedId: ObjectId("634c1ee44ff2758a4da740a8")
}

> db.science.findOne()

**Output**
{ _id: ObjectId("634c1ee44ff2758a4da740a8"), a: 0.3, b: 0.1 }
```

Let execute maths calculation

```bash
> db.science.aggregate([
		{ $project: {result: {$subtract: ['$a', '$b'] } } }
])

**Output**
[
  {
    _id: ObjectId("634c1ee44ff2758a4da740a8"),
    result: 0.19999999999999998
  }
]
```

Here should be the subtract value is 0.2. But it's come

```cpp
{ "_id" : ObjectId("5f3e9ec9d0209e4d3a0ec079"), "result" : 0.19999999999999998 }
```

So have to fix the issue

# Working With Decimal 128bit

To get the exact subtract value, have to use `NumberDecimal` constructor. This will different for other language.

```bash
> db.companies.deleteMany({})

**Output**
{ acknowledged: true, deletedCount: 6 }

> db.science.insertOne({a: NumberDecimal("0.3"), b: NumberDecimal("0.1")})

**Output**
{
  acknowledged: true,
  insertedId: ObjectId("634c209d4ff2758a4da740aa")
}

> db.science.find().pretty()

**Output**
[
  {
    _id: ObjectId("634c209d4ff2758a4da740aa"),
    a: Decimal128("0.3"),
    b: Decimal128("0.1")
  }
]

```

Now getting the exact value

```bash
> db.science.aggregate([
		{ $project: { result: { $subtract: ['$a', '$b'] } } } 
])

**Output**
[
  {
    _id: ObjectId("634c209d4ff2758a4da740aa"),
    result: Decimal128("0.2")
  }
]
```

Let execute another query

```bash
> db.science.updateOne({}, {$inc: {a: 0.1}})

**Output**
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}

> db.science.find().pretty()

**Output**
[
  {
    _id: ObjectId("634c209d4ff2758a4da740aa"),
    a: Decimal128("0.400000000000000"),
    b: Decimal128("0.1")
  }
]
```

So to get the right value

```cpp
> db.science.updateOne({}, {$inc: {a: NumberDecimal("0.1")}})

Output
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}

> db.science.find().pretty()

**Output**
[
  {
    _id: ObjectId("634c209d4ff2758a4da740aa"),
    a: Decimal128("0.500000000000000"),
    b: Decimal128("0.1")
  }
]
```

## High precision

Normal precision use less space.

```bash
> db.number.insertOne({num: 0.1})

**Output**
{
  acknowledged: true,
  insertedId: ObjectId("634c231e4ff2758a4da740ab")
}

> db.number.stats()
"size" : 33

> db.number.deleteMany({})

**Output**
{ acknowledged: true, deletedCount: 1 }
```

`NumberDecimal` will use give **high precision** but consume more space

```bash
> db.number.insertOne({num: NumberDecimal("0.1")})

**Output**
{
  acknowledged: true,
  insertedId: ObjectId("634c23574ff2758a4da740ac")
}

> db.number.stats()
"size" : 41
```