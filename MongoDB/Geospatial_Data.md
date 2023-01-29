# Geospatial Data

# Working with geospatial data

Storing Geospatial data in Geo-JSON Format
Querying Geospatial Data

GeoJson value is an embedded document, it contains two fields

1. **type** ⇒ specifies the **GeoJson object type**
2. **coordinates** ⇒ two values **[longitude, latitude]** in this format

some operations (`$near`) require such an index.
but other operations like `$geoWithin` do not require an index.
still can be used to speed up queries

GeoJson object Type: 1. Point 2. Line String 3. MultiLineString 4. Polygon 5. MultiPolygon 6. MultiPoint 7. GeometryCollection

`$GeoSpatial` Queries : `$near`, `$geoWithin`, `$geoIntersects`
GeoSpatial queries work with GeoJson data

```cpp
> use awesomeplaces
switched to db awesomeplaces

> db.places.insertOne(
		{
				name:'California Academy of Science',
				location:  {
						type:'Point',
						coordinates:[-122.4724356, 37.7672544]
				}
		}
)
**Output**
{
	"acknowledged" : true,
	"insertedId" : ObjectId("633137658e481228c8def957")
}
```

this is the GeoJson object

```cpp
db.places.findOne()
**Output**
{
	"_id" : ObjectId("5f2f7ac7f82aee4b45288303"),
	"name" : "California Academy of Science",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4724356,
			37.7672544
		]
	}
}
```

let execute query into GeoJson data
to get our current location we have to use web API or mobile API have to use any other process so that users can locate themselves

# Finding the nearest places from the current location

I want to find some places near my current location (sherpur home)
Let's this is my location
latitude : 25.0218715, longitude : 90.0106577

 Sherpur Government University College position --> latitude: 25.017493, longitude : 90.011495

```cpp
db.places.insertOne(
		{
				name:'Sherpur Government University College',
				location:  {type:'Point',coordinates:[90.011495,25.017493]}
		}
)
**Output**
{
	"acknowledged" : true,
	"insertedId" : ObjectId("6339a8585610e4d4a7fbd9c0")
}
```

Certain radius
First, we have to create a **GeoSpatial index** to track the distance `$near` needs this
Here the index name is '**2dsphere**'

```cpp
db.places.createIndex({location: '2dsphere'})
**Output**
{
	"numIndexesBefore" : 2,
	"numIndexesAfter" : 2,
	"note" : "all indexes already exist",
	"ok" : 1
}
```

Let's find my nearest places(Sherpur Government University College is the nearest which we inserted into the database earlier). As we do not define any max or min distance so it will return all places in the database.

```cpp
db.places.find(
		{
				location: {
						$near: {
								$geometry: {
										type: 'Point', coordinates: [90.0106577, 25.0218715] //Raju's current location
								}
						}
				}
		}
).pretty()
**Output**
{
	"_id" : ObjectId("5f2f81bef82aee4b45288304"),
	"name" : "Sherpur Government University College",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			90.011495,
			25.017493
		]
	}
}
{
	"_id" : ObjectId("5f2f7ac7f82aee4b45288303"),
	"name" : "California Academy of Science",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4724356,
			37.7672544
		]
	}
}
```

We can add also **max** and **min** **distance** into the query

```cpp
db.places.find(
		{
				location: {
						$near: {
								$geometry: { type: 'Point', coordinates: [90.0106577, 25.0218715]}, 
								$maxDistance: 30, 
								$minDistance: 10
						}
				}
		}
).pretty()
```

We don't get any value, cause no places found with the distance that gives

I calculate the distance between my current location and Sherpur Government University College is 200.00m to 350m approximate

So we have to add max distance a little bit large

```cpp
db.places.find(
	{
		location: {
			$near: {
				$geometry: {type: 'Point', coordinates: [90.0106577, 25.0218715]}, 
				$maxDistance: 500, $minDistance: 20
			}
		}
	}
).pretty()
**Output**
{
	"_id" : ObjectId("5f2f81bef82aee4b45288304"),
	"name" : "Sherpur Government University College",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			90.011495,
			25.017493
		]
	}
}
```

# Finding Points/Places inside a covered area

Want to find all coordinates around the area
Could be a sphere, any polygon
Which points are inside of the area?
Here consider 4 points
Previous California Academy of Science point also

Insert points

```cpp
db.places.insertOne({name:'Conservatory of Flowers',location:{type:'Point',coordinates:[-122.4615748, 37.7701756]}})
db.places.insertOne({name:'Golden Gate Park Tennis',location:{type:'Point',coordinates:[-122.4593702, 37.7705046]}})
db.places.insertOne({name:'Nopa',location:{type:'Point',coordinates:[-122.4389058, 37.7747415]}})
```

Show all documents

```cpp
db.places.find().pretty()
**Output**
{
	"_id" : ObjectId("633137658e481228c8def957"),
	"name" : "California Academy of Science",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4724356,
			37.7672544
		]
	}
}
{
	"_id" : ObjectId("63326f7d8e481228c8def958"),
	"name" : "Sherpur Government University College",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			90.011495,
			25.017493
		]
	}
}
{
	"_id" : ObjectId("633270438e481228c8def959"),
	"name" : "Conservatory of Flowers",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4615748,
			37.7701756
		]
	}
}
{
	"_id" : ObjectId("633270438e481228c8def95a"),
	"name" : "Golden Gate Park Tennis",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4593702,
			37.7705046
		]
	}
}
{
	"_id" : ObjectId("633270468e481228c8def95b"),
	"name" : "Nopa",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4389058,
			37.7747415
		]
	}
}
```

Let's draw a polygon with the four points to check the area points that exist in the database that are covered by the area

```cpp
const p1 = [-122.4547, 37.77473]
p1
[ -122.4547, 37.77473 ]
const p2 = [-122.45303, 37.76641]
const p3 = [-122.51026, 37.76411]  
const p4 = [-122.51088, 37.77131]
```

Here do not use `$near` instead use `$geoWithin` this can help to find all elements within a certain shape, within a certain object, typically something like polygon
Have to also add p1(first corner) again, cause to complete the polygon and also close the polygon. Will return places inside the polygon.

p1 ----------------- p2
     |                     |
     |                     |
     |			   |
p4 ----------------- p3

Conservatory of Flowers, Golden Gate Park Tennis, Califonia Academy of Science are between in the polygon Nopa is not in that polygon so that will not printed. 

```cpp
db.places.find(
	{
		location: {
			$geoWithin: {
				$geometry: {type: 'Polygon', coordinates: [[p1, p2, p3, p4, p1]]}
			}
		}
	}
).pretty()
**Output**
{
	"_id" : ObjectId("5f2f9194f82aee4b45288305"),
	"name" : "Conservatory of Flowers",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4615748,
			37.7701756
		]
	}
}
{
	"_id" : ObjectId("5f2f931ff82aee4b45288306"),
	"name" : "Golden Gate Park Tennis",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4593702,
			37.7705046
		]
	}
}
{
	"_id" : ObjectId("5f2f7ac7f82aee4b45288303"),
	"name" : "California Academy of Science",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4724356,
			37.7672544
		]
	}
}
```

# Finding Out if a User is Inside a Specific Area

Now another typical use case would be the opposite, which has an application that wants to find out whether the **user** is **in** a **particular area**.

So don't want to find all places in an area but want to store a couple of different areas potentially in the database

Let's say the city's neighborhoods and then the user sends some coordinates because he located himself and wants to find out in which neighborhood the user is.

So essentially the same query as before, just the other way around.

Let's insert data into new collections

```cpp
db.areas.insertOne(
		{
				name: 'Golden Gate Park' ,
				area: {
						type: 'Polygon', 
						coordinates: [[p1, p2, p3, p4, p1]]
				}
		}
)
**Output**
{
	"acknowledged" : true,
	"insertedId" : ObjectId("6339b4a9026aca8876f82bf9")
}

db.areas.find().pretty()
**Output**
{
	"_id" : ObjectId("6339b4a9026aca8876f82bf9"),
	"name" : "Golden Gate Park",
	"area" : {
		"type" : "Polygon",
		"coordinates" : [
			[
				[
					-122.4547,
					37.77473
				],
				[
					-122.45303,
					37.76641
				],
				[
					-122.51026,
					37.76411
				],
				[
					-122.51088,
					37.77131
				],
				[
					-122.4547,
					37.77473
				]
			]
		]
	}
}
```

Now create an index

```cpp
db.areas.createIndex({area: '2dsphere'})
**Output**
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```

Basically check `$geoIntersects` is true or false
Here .p --> means query point
p1 ----------------- p2
   |                       |
   |          .p          |
   |			   |
p4 ----------------- p3

Golden Gate Park is in -122.49089, 37.76992
Result --> here can see all points intersect

```cpp
db.areas.find(
		{
				area: 
						{
								$geoIntersects: {
										$geometry: {
												type: 'Point', 
												coordinates: [-122.49089, 37.76992]
										}
								}
						}
		}
).pretty()
**Output**
{
	"_id" : ObjectId("6339b4a9026aca8876f82bf9"),
	"name" : "Golden Gate Park",
	"area" : {
		"type" : "Polygon",
		"coordinates" : [
			[
				[
					-122.4547,
					37.77473
				],
				[
					-122.45303,
					37.76641
				],
				[
					-122.51026,
					37.76411
				],
				[
					-122.51088,
					37.77131
				],
				[
					-122.4547,
					37.77473
				]
			]
		]
	}
}
```

Check with the outside point
Result --> do not find any point

```cpp
db.areas.find(
		{
				area: {
						$geoIntersects: {
								$geometry: {
										type: 'Point', 
										coordinates: [-122.48446, 37.77776]
								}
						}
				}
		}
).pretty()
```

# Finding Places Within a Certain Radius

Want to find all elements with unsorted order that are within a certain radius
Want to find all places that are within a place or an area
Here have to use `geoWithin` not `geoIntersects`
Also can use `$centerSphere` operator instead of `$geometry` operator
`$centerSphere` is a helpful operator that allows to quickly get a circle at a point
So essentially it uses a radius and a center and gives the whole circle around that center, therefore.
`$centerSphere` first element--> the coordinates of the center of the circle want to draw.(-122.46203,37.77286)
`$centerSphere` second element --> a radius length with meter(m) , now interested into kilometers
Here use one kilometer
Convert distance to the radius, 6378.1 kilometers is an earth radius
Here use places collection

```cpp
db.places.find(
		{
				location: {
						$geoWithin: {
								$centerSphere: [[-122.46203, 37.77286], 1 / 6378.1]
						}
				}
		}
).pretty()
**Output**
{
	"_id" : ObjectId("5f2f9194f82aee4b45288305"),
	"name" : "Conservatory of Flowers",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4615748,
			37.7701756
		]
	}
}
{
	"_id" : ObjectId("5f2f931ff82aee4b45288306"),
	"name" : "Golden Gate Park Tennis",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4593702,
			37.7705046
		]
	}
}
```

California Academy of Science coordinates are falsely inserted, so update the query

```cpp
db.places.updateOne(
		{_id: ObjectId("5f2f7ac7f82aee4b45288303")}, 
		{
				$set: 
						{
								location: {
										type: 'Point', 
										coordinates: [-122.46636, 37.77014]
								}
						}
		}
)

db.places.find(
		{
				location: {
						$geoWithin: {
								$centerSphere: [[-122.46203, 37.77286], 1 / 6378.1]
						}
				}
		}
).pretty()
**Output**
{
	"_id" : ObjectId("5f2f7ac7f82aee4b45288303"),
	"name" : "California Academy of Science",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.46636,
			37.77014
		]
	}
}
{
	"_id" : ObjectId("5f2f9194f82aee4b45288305"),
	"name" : "Conservatory of Flowers",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4615748,
			37.7701756
		]
	}
}
{
	"_id" : ObjectId("5f2f931ff82aee4b45288306"),
	"name" : "Golden Gate Park Tennis",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			-122.4593702,
			37.7705046
		]
	}
}
```

`result-`-> here get the data in unsorted order. To sort the data apply the manual approach
`$near` is the solution to the sorted list