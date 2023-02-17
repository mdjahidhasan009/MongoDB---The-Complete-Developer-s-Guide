# Create/Insert

# Create(Insertion)

```scheme
insertOne(data, options)
insertMany(data, options)
```

## insertOne

Will insert JSON object(in the connected database this case shop) on products collection(if the collection does not exist then it will be created)

```scheme
db.products.insertOne({ "name": "Max", price: 12.99})
db.products.insertOne({ name: "A Computer", price:34829.99, description: "This is high quality computer", details:{cpu: "Intel i7 8770",memory: 32}})
```

Like `db.products.insertOne({ name: "Max" })` mongodb will do it automatically.This will give back a command like.

```powershell
{
		"acknowledged": true,
		"insertedId": ObjectId("61691b464aed55381c7714d3")
}
```

That means data is inserted successfully and MongoDB will create a unique id automatically for it which is returned at insertedId.

> We can insert using `db.products.insertOne({ name: "Max", price: 12.99})` . MongoDB will add “ ” for key(like “name”) automatically but must have to wrap value with “ ”(like “Max”).
> 

### insertMany

Will insert more than one document. Have to insert it as an **array**. Will return all ids of the inserted document.

```scheme
> db.flightData.insertMany([
...   {
...     "departureAirport": "MUC",
...     "arrivalAirport": "SFO",
...     "aircraft": "Airbus A380",
...     "distance": 12000,
...     "intercontinental": true
...   },
...   {
...     "departureAirport": "LHR",
...     "arrivalAirport": "TXL",
...     "aircraft": "Airbus A320",
...     "distance": 950,
...     "intercontinental": false
...   }
... ])
```

Output

```scheme
{
	"acknowledged" : true,
	"insertedIds" : [
		ObjectId("5f132aebd022deabe244f26d"),
		ObjectId("5f132aebd022deabe244f26e")
	]
}
```
<detials>
	<summary>Checking using find</summary>
    
    Code
    
    ```scheme
     db.flightData.find().pretty()
    ```
    
    Output
    
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
</details>
 

### Inserting number(double/float), Timestamp, Date

```scheme
> db.companies.insertOne(
	{
		name: "Freash Apples Inc", 
		isStartup: true, 
		employees: 33, 
		funding: 123456789876543219, 
		 details: {
			 cea: "Mark Super"
		 },
		 tags: [
			 {title: "super"},
			 {title: "perfect"}
     ], 
     foundingData: new Date(), 
     insertedAt: new Timestamp()
   }
)

**Output**
{
	"acknowledged" : true,
	"insertedId" : ObjectId("5f13ec400249b11a6aa5e37f")
}
```

- Checking result
    
    ```bash
    > db.companies.find().pretty()
    
    **Output**
    {
    	"_id" : ObjectId("5f13ec400249b11a6aa5e37f"),
    	"name" : "Freash Apples Inc",
    	"isStartup" : true,
    	"employees" : 33,
    	"funding" : 123456789876543220,
    	"details" : {
    		"cea" : "Mark Super"
    	},
    	"tags" : [
    		{
    			"title" : "super"
    		},
    		{
    			"title" : "perfect"
    		}
    	],
    	"foundingData" : ISODate("2020-07-19T06:46:24.175Z"),
    	"insertedAt" : Timestamp(1595141184, 1)
    }
    ```
    

Create int **32 bits** value instant of **default 64 bits floating** point value.

```scheme
> db.numbers.insertOne({a: NumberInt(1)})
**Output**
{
	"acknowledged" : true,
	"insertedId" : ObjectId("5f13ef440249b11a6aa5e382")
}
```