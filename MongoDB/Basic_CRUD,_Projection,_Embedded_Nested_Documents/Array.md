# Array

- An array of embedded documents.
- An array can hold any data.
Inserting array

```scheme
> db.passengers.updateOne({name: "Albert Twostone"}, {$set:{hobbies:["sports", "cooking"]}})
**Output**
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 0 }

> db.passengers.find({name: "Albert Twostone"}).pretty()
{
	"_id" : ObjectId("5f1339f7d022deabe244f282"),
	"name" : "Albert Twostone",
	"age" : 68,
	"hobbies" : [
		"sports",
		"cooking"
	]
}
```

<details>
  <summary>Output to test(last document where the array was added)</summary>

```scheme
db.passengers.find().pretty()
```
**Output**
```scheme
{
    "_id" : ObjectId("5f1339f7d022deabe244f26f"),
    "name" : "Max Schwarzmueller",
    "age" : 29
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f270"),
    "name" : "Manu Lorenz",
    "age" : 30
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f271"),
    "name" : "Chris Hayton",
    "age" : 35
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f272"),
    "name" : "Sandeep Kumar",
    "age" : 28
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f273"),
    "name" : "Maria Jones",
    "age" : 30
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f274"),
    "name" : "Alexandra Maier",
    "age" : 27
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f275"),
    "name" : "Dr. Phil Evans",
    "age" : 47
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f276"),
    "name" : "Sandra Brugge",
    "age" : 33
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f277"),
    "name" : "Elisabeth Mayr",
    "age" : 29
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f278"),
    "name" : "Frank Cube",
    "age" : 41
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f279"),
    "name" : "Karandeep Alun",
    "age" : 48
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f27a"),
    "name" : "Michaela Drayer",
    "age" : 39
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f27b"),
    "name" : "Bernd Hoftstadt",
    "age" : 22
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f27c"),
    "name" : "Scott Tolib",
    "age" : 44
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f27d"),
    "name" : "Freddy Melver",
    "age" : 41
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f27e"),
    "name" : "Alexis Bohed",
    "age" : 35
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f27f"),
    "name" : "Melanie Palace",
    "age" : 27
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f280"),
    "name" : "Armin Glutch",
    "age" : 35
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f281"),
    "name" : "Klaus Arber",
    "age" : 53
}
{
    "_id" : ObjectId("5f1339f7d022deabe244f282"),
    "name" : "Albert Twostone",
    "age" : 68,
    "hobbies" : [
        "sports",
        "cooking"
    ]
}
```
</details>

Will get only hobbies array as MongoDB return only one document that's why `find()` will not work here(as it return cursor).

```scheme
> db.passengers.findOne({name: "Albert Twostone"}).hobbies
**Output**
[ "sports", "cooking" ]
```

### Query an array

```scheme
> db.passengers.find({hobbies: "sports"}).pretty()
**Output**
{
	"_id" : ObjectId("5f1339f7d022deabe244f282"),
	"name" : "Albert Twostone",
	"age" : 68,
	"hobbies" : [
		"sports",
		"cooking"
	]
}
```