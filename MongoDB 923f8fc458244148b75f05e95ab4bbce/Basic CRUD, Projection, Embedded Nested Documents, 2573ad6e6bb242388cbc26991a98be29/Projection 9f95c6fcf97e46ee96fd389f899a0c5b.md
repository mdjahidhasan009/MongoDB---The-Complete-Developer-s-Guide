# Projection

### Get only selected key value

Returns only _id(automatically returns this) and name(like filtering)

```scheme
> db.passengers.find({},{name: 1}).pretty()

**Output**
{
	"_id" : ObjectId("5f1339f7d022deabe244f26f"),
	"name" : "Max Schwarzmueller"
}
{ "_id" : ObjectId("5f1339f7d022deabe244f270"), "name" : "Manu Lorenz" }
{ "_id" : ObjectId("5f1339f7d022deabe244f271"), "name" : "Chris Hayton" }
{ "_id" : ObjectId("5f1339f7d022deabe244f272"), "name" : "Sandeep Kumar" }
{ "_id" : ObjectId("5f1339f7d022deabe244f273"), "name" : "Maria Jones" }
{ "_id" : ObjectId("5f1339f7d022deabe244f274"), "name" : "Alexandra Maier" }
{ "_id" : ObjectId("5f1339f7d022deabe244f275"), "name" : "Dr. Phil Evans" }
{ "_id" : ObjectId("5f1339f7d022deabe244f276"), "name" : "Sandra Brugge" }
{ "_id" : ObjectId("5f1339f7d022deabe244f277"), "name" : "Elisabeth Mayr" }
{ "_id" : ObjectId("5f1339f7d022deabe244f278"), "name" : "Frank Cube" }
{ "_id" : ObjectId("5f1339f7d022deabe244f279"), "name" : "Karandeep Alun" }
{ "_id" : ObjectId("5f1339f7d022deabe244f27a"), "name" : "Michaela Drayer" }
{ "_id" : ObjectId("5f1339f7d022deabe244f27b"), "name" : "Bernd Hoftstadt" }
{ "_id" : ObjectId("5f1339f7d022deabe244f27c"), "name" : "Scott Tolib" }
{ "_id" : ObjectId("5f1339f7d022deabe244f27d"), "name" : "Freddy Melver" }
{ "_id" : ObjectId("5f1339f7d022deabe244f27e"), "name" : "Alexis Bohed" }
{ "_id" : ObjectId("5f1339f7d022deabe244f27f"), "name" : "Melanie Palace" }
{ "_id" : ObjectId("5f1339f7d022deabe244f280"), "name" : "Armin Glutch" }
{ "_id" : ObjectId("5f1339f7d022deabe244f281"), "name" : "Klaus Arber" }
{ "_id" : ObjectId("5f1339f7d022deabe244f282"), "name" : "Albert Twostone" }
Type "it" for more
```

### Select and remove key and value while querying

Returns only name also filtering _id which automatically added by MongoDB.

```scheme
> db.passengers.find({},{name: 1,_id: 0}).pretty()

**Output**
{ "name" : "Max Schwarzmueller" }
{ "name" : "Manu Lorenz" }
{ "name" : "Chris Hayton" }
{ "name" : "Sandeep Kumar" }
{ "name" : "Maria Jones" }
{ "name" : "Alexandra Maier" }
{ "name" : "Dr. Phil Evans" }
{ "name" : "Sandra Brugge" }
{ "name" : "Elisabeth Mayr" }
{ "name" : "Frank Cube" }
{ "name" : "Karandeep Alun" }
{ "name" : "Michaela Drayer" }
{ "name" : "Bernd Hoftstadt" }
{ "name" : "Scott Tolib" }
{ "name" : "Freddy Melver" }
{ "name" : "Alexis Bohed" }
{ "name" : "Melanie Palace" }
{ "name" : "Armin Glutch" }
{ "name" : "Klaus Arber" }
{ "name" : "Albert Twostone" }
```