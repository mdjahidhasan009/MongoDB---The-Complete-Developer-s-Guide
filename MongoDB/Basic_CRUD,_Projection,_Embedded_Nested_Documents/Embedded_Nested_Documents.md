# Embedded/Nested Documents

- Up to 100 levels of nesting.
- Overall document size has to be below 60MB and a max of 16MB per document.

Adding nested document `status: {description: “on-time”, lastupdated: “i Hour ago”}`

```scheme
> db.flightData.updateMany({},{$set:{status:{description: "on-time", lastupdated: "i Hour ago"}}})
**Output**
{ "acknowledged" : true, "matchedCount" : 2, "modifiedCount" : 0 }

> db.flightData.find().pretty()
**Output**
{
	"_id" : ObjectId("5f132aebd022deabe244f26d"),
	"departureAirport" : "MUC",
	"arrivalAirport" : "SFO",
	"aircraft" : "Airbus A380",
	"distance" : 12000,
	"intercontinental" : true,
	"status" : {
		"description" : "on-time",
		"lastupdated" : "i Hour ago"
	}
}
{
	"_id" : ObjectId("5f132aebd022deabe244f26e"),
	"departureAirport" : "LHR",
	"arrivalAirport" : "TXL",
	"aircraft" : "Airbus A320",
	"distance" : 950,
	"intercontinental" : false,
	"status" : {
		"description" : "on-time",
		"lastupdated" : "i Hour ago"
	}
}
```

Adding one more document `details: {responsible: “Raju”}`

```scheme
> db.flightData.updateMany({},{$set:{status:{description: "on-time", lastupdated: "i Hour ago",details:{responsible: "RAJU"}}}})
**Output**
{ "acknowledged" : true, "matchedCount" : 2, "modifiedCount" : 2 }

> db.flightData.find().pretty()
{
	"_id" : ObjectId("5f132aebd022deabe244f26d"),
	"departureAirport" : "MUC",
	"arrivalAirport" : "SFO",
	"aircraft" : "Airbus A380",
	"distance" : 12000,
	"intercontinental" : true,
	"status" : {
		"description" : "on-time",
		"lastupdated" : "i Hour ago",
		"details" : {
			"responsible" : "RAJU"
		}
	}
}
{
	"_id" : ObjectId("5f132aebd022deabe244f26e"),
	"departureAirport" : "LHR",
	"arrivalAirport" : "TXL",
	"aircraft" : "Airbus A320",
	"distance" : 950,
	"intercontinental" : false,
	"status" : {
		"description" : "on-time",
		"lastupdated" : "i Hour ago",
		"details" : {
			"responsible" : "RAJU"
		}
	}
}
```

**Query a document**

Embedded document flightData > status > description. Have to give double quotation in key "`status.description`" as these are nested.

```scheme
> db.flightData.find({"status.description": "on-time"}).pretty()
**Output**
{
	"_id" : ObjectId("5f132aebd022deabe244f26d"),
	"departureAirport" : "MUC",
	"arrivalAirport" : "SFO",
	"aircraft" : "Airbus A380",
	"distance" : 12000,
	"intercontinental" : true,
	"status" : {
		"description" : "on-time",
		"lastupdated" : "i Hour ago",
		"details" : {
			"responsible" : "RAJU"
		}
	}
}
{
	"_id" : ObjectId("5f132aebd022deabe244f26e"),
	"departureAirport" : "LHR",
	"arrivalAirport" : "TXL",
	"aircraft" : "Airbus A320",
	"distance" : 950,
	"intercontinental" : false,
	"status" : {
		"description" : "on-time",
		"lastupdated" : "i Hour ago",
		"details" : {
			"responsible" : "RAJU"
		}
	}
} 
```

We can do further query flightData > status > details > responsible and also have to wrap key with double quotation `“status.details.responsible”` as they are nested.

 

```bash
> db.flightData.find({"status.details.responsible": "RAJU"}).pretty()
**Output**
{
	"_id" : ObjectId("5f132aebd022deabe244f26d"),
	"departureAirport" : "MUC",
	"arrivalAirport" : "SFO",
	"aircraft" : "Airbus A380",
	"distance" : 12000,
	"intercontinental" : true,
	"status" : {
		"description" : "on-time",
		"lastupdated" : "i Hour ago",
		"details" : {
			"responsible" : "RAJU"
		}
	}
}
{
	"_id" : ObjectId("5f132aebd022deabe244f26e"),
	"departureAirport" : "LHR",
	"arrivalAirport" : "TXL",
	"aircraft" : "Airbus A320",
	"distance" : 950,
	"intercontinental" : false,
	"status" : {
		"description" : "on-time",
		"lastupdated" : "i Hour ago",
		"details" : {
			"responsible" : "RAJU"
		}
	}
}
```