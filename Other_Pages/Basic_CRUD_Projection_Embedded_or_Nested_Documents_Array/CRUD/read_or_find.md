# Read ⇒ find

- `find()` return **cursor** that’s why can use `pretty()`. But when there is a big array then `find()` gives **the first 20 data** and gives the **"it" cursor** for getting other data.
- `find()` **returns all** as no argument in `find()`. Only find will return **the first 20** and a cursor "it" to iterate further but `find().toArray()` will return **all data as an array**.
- `findOne(filter)` **return object** that's why can not use `pretty()` it exits on the cursor.

```scheme
find(filter, options)
findOne(filter, options)
```

- Initial data in db

    ```scheme
    {
    	"_id" : ObjectId("5f132aebd022deabe244f26d"),
    	"departureAirport" : "MUC",
    	"arrivalAirport" : "SFO",
    	"aircraft" : "Airbus A380",
    	"distance" : 12000,
    	"intercontinental" : true
    }
    {
    	"_id" : ObjectId("5f132aebd022deabe244f26e"),
    	"departureAirport" : "LHR",
    	"arrivalAirport" : "TXL",
    	"aircraft" : "Airbus A320",
    	"distance" : 950,
    	"intercontinental" : false
    }
    ```


### `find()` with different scenarios

```scheme
//Find all flight that has intercontinental: true property.
> db.flightData.find(
    {
        intercontinental: true
    }
).pretty() 

Output
{
	"_id" : ObjectId("5f132aebd022deabe244f26d"),
	"departureAirport" : "MUC",
	"arrivalAirport" : "SFO",
	"aircraft" : "Airbus A380",
	"distance" : 12000,
	"intercontinental" : true
}

//Will return the first one which fulfills the condition.
> db.flightData.findOne(
    {
        distance: 
            { 
                $gt: 900 
            }
    }
)

**Output**
{
	"_id" : ObjectId("5f132aebd022deabe244f26d"),
	"departureAirport" : "MUC",
	"arrivalAirport" : "SFO",
	"aircraft" : "Airbus A380",
	"distance" : 12000,
	"intercontinental" : true
}
```

**Can do some process at each data.**

- Code

    ```scheme
    > db.passengers
    .find()
    .forEach((passengerData) => {printjson(passengerData)})
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
    	"age" : 68
    }
    {
    	"_id" : ObjectId("5f1339f7d022deabe244f283"),
    	"name" : "Gordon Black",
    	"age" : 38
    }
    ```


**Get the statistics of database**

- Inserting data for the below command

    ```scheme
    > db.numbers.insertOne({a: 1})
    {
    	"acknowledged" : true,
    	"insertedId" : ObjectId("5f13ed5a0249b11a6aa5e380")
    }
    > db.numbers.findOne()
    { "_id" : ObjectId("5f13ed5a0249b11a6aa5e380"), "a" : 1 }
    ```


```scheme
> db.stats
**Output**
function (scale) {
        return this.runCommand({dbstats: 1, scale: scale});
    }
```

### Statistics of db

```scheme
> db.stats()

Output
{
	"db" : "companyData",
	"collections" : 2,
	"views" : 0,
	"objects" : 2,
	"avgObjSize" : 135,
	"dataSize" : 270,
	"storageSize" : 20480,
	"numExtents" : 0,
	"indexes" : 2,
	"indexSize" : 20480,
	"fsUsedSize" : 54183743488,
	"fsTotalSize" : 61754699776,
	"ok" : 1
}
```

**Getting the datatype**

- Inserting the document for the command below

    ```scheme
    > db.numbers.insertOne({a: 1.5,b: "r"})
    Output
    {
    	"acknowledged" : true,
    	"insertedId" : ObjectId("5f13f19e0249b11a6aa5e386")
    }
    ```


```scheme
> typeof db.numbers.findOne({b: "r"}).a
**Output**
number
```

### Getting all data from an collection

By default `find()` returns the first 20 documents and a cursor `it` for further iteration. We can use `.toArray()` to get whole collection.

```bash
> db.passengers.find().toArray()
```