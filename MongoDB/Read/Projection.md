# Projection

# Using Projection to Share our Results

Want to retrieve elements of specific fields, no need to check the other fields. they are executed by default but objectId always includes them by default.

```cpp
> db.movies.find({}, {name: 1, genres: 1, runtime: 1, rating: 1}).pretty()
```

To exclude the objectId

```cpp
> db.movies.find({}, {name: 1, genres: 1, runtime: 1, rating: 1, _id: 0}).pretty()
```

Can also use embedded documents with path notation

```cpp
> db.movies.find({}, {name: 1, genres: 1, runtime: 1, 'rating.average': 1, _id: 0}).pretty()
```

The schedule has a time and date field but only the time element will be returned.

```cpp
> db.movies.find({}, {name: 1, genres: 1, runtime: 1, 'rating.average': 1, 'schedule.time': 1,_id: 0}).pretty()
```

Can also add logic for the filter.

```cpp
> db.movies.find({'rating.average': {$gt: 8}}, {name: 1, genres: 1, runtime: 1, 'rating.average': 1, 'schedule.time': 1,_id: 0}).pretty()
```

## Projection in Arrays

Simply array query

```bash
> db.movies.find({genres: 'Drama'}).pretty()
```

Return the array projection of the related query(in genres array will be only one element others will be neglected).

```bash
> db.movies.find({genres: 'Drama'},{'genres.$': 1}).pretty()
**Output**
{ "_id" : ObjectId("61beab75a2d88f2d3d82d451"), "genres" : [ "Drama" ] }
{ "_id" : ObjectId("61beab75a2d88f2d3d82d452"), "genres" : [ "Drama" ] }
{ "_id" : ObjectId("61beab75a2d88f2d3d82d454"), "genres" : [ "Drama" ] }
{ "_id" : ObjectId("61beab75a2d88f2d3d82d458"), "genres" : [ "Drama" ] }
and so on.
```

It does not work properly(will return only for "Horror" as "Drama" will be replaced by "Horror").

```cpp
> db.movies.find({genres: {$all: ['Drama', 'Horror']}},{'genres.$': 1}).pretty()
{ "_id" : ObjectId("61beab75a2d88f2d3d82d455"), "genres" : [ "Horror" ] }
{ "_id" : ObjectId("61beab75a2d88f2d3d82d45c"), "genres" : [ "Horror" ] }
and so on.
```

This projection is the element-wise and exact query

`{$elemMatch: {$eq: 'Horror'}}` --> this thing decide which item is displayed or not

Those document which has only Drama in genres will just print the id and those which has both drama and horror in genres in the genres will print "genres" : ["Horror"].

```bash
> db.movies.find({genres: 'Drama'},{genres: {$elemMatch: {$eq: 'Horror'}}}).pretty()
**Ouput**
{ "_id" : ObjectId("61beab75a2d88f2d3d82d451") } => has only genres "Drama" which is must-have.
{ "_id" : ObjectId("61beab75a2d88f2d3d82d452") }
{ "_id" : ObjectId("61beab75a2d88f2d3d82d454") }
{ "_id" : ObjectId("61beab75a2d88f2d3d82d455"), "genres" : [ "Horror" ] } => has both genres "Drama" and "Horror"
and so on.
```

Can also check with other element

```cpp
db.movies.find({'rating.average':{$gt: 9}},{genres: {$elemMatch: {$eq: 'Horror'}}}).pretty()
```

## Projection Slice

Slicing array that I want, can add any number
`$slice: 2` --> how many array elements we want to show from first

```bash
db.movies.find({'rating.average':{$gt: 9}}, {genres: {$slice: 2}, name: 1})
{ "_id" : ObjectId("61beab75a2d88f2d3d82d46f"), "name" : "Berserk", "genres" : [ "Anime", "Fantasy" ] }
{ "_id" : ObjectId("61beab75a2d88f2d3d82d4a6"), "name" : "Game of Thrones", "genres" : [ "Drama", "Adventure" ] }
{ "_id" : ObjectId("61beab75a2d88f2d3d82d4fa"), "name" : "Firefly", "genres" : [ "Adventure", "Science-Fiction" ] }
{ "_id" : ObjectId("61beab75a2d88f2d3d82d4fb"), "name" : "The Wire", "genres" : [ "Drama", "Crime" ] }
and so on.
```

Can also be executed with an array from
// 1--> What lengths of elements do we want to skip (index - start from 1)
// 2--> How many elements do we want to show

```cpp
db.movies.find({'rating.average':{$gt: 9}}, {genres: {$slice: [1, 2]}, name: 1})
{ "_id" : ObjectId("61beab75a2d88f2d3d82d46f"), "name" : "Berserk", "genres" : [ "Fantasy", "Horror" ] }
{ "_id" : ObjectId("61beab75a2d88f2d3d82d4a6"), "name" : "Game of Thrones", "genres" : [ "Adventure", "Fantasy" ] }
{ "_id" : ObjectId("61beab75a2d88f2d3d82d4fa"), "name" : "Firefly", "genres" : [ "Science-Fiction", "Western" ] }
and so on.
// checking
> db.movies.find({_id: ObjectId("5f15a22a9bfbc37d06f66662")},{genres: 1}).pretty()
{
	"_id" : ObjectId("5f15a22a9bfbc37d06f66662"),
	"genres" : [
		"Drama",
		"Adventure",
		"Fantasy"
	]
}
```

```cpp
> db.movies.find({'rating.average':{$gt: 9}}, {genres: {$slice: [2, 2]}, name: 1}).pretty()

{
	"_id" : ObjectId("5f15a22a9bfbc37d06f6662d"),
	"name" : "Berserk",
	"genres" : [
		"Horror"
	]
}
{
	"_id" : ObjectId("5f15a22a9bfbc37d06f66662"),
	"name" : "Game of Thrones",
	"genres" : [
		"Fantasy"
	]
}
{
	"_id" : ObjectId("5f15a22a9bfbc37d06f666b7"),
	"name" : "Breaking Bad",
	"genres" : [
		"Thriller"
	]
}
{
	"_id" : ObjectId("5f15a22a9bfbc37d06f666c0"),
	"name" : "The Wire",
	"genres" : [ ]
}
{
	"_id" : ObjectId("5f15a22a9bfbc37d06f666c1"),
	"name" : "Firefly",
	"genres" : [
		"Western"
	]
}
{
	"_id" : ObjectId("5f15a22a9bfbc37d06f666d8"),
	"name" : "Stargate SG-1",
	"genres" : [
		"Science-Fiction"
	]
}
{
	"_id" : ObjectId("5f15a22a9bfbc37d06f666e2"),
	"name" : "Rick and Morty",
	"genres" : [
		"Science-Fiction"
	]
}
```