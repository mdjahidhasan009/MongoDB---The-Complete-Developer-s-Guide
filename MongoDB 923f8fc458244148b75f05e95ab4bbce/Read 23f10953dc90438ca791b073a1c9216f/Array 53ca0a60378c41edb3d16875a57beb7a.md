# Array

`$elemMatch`

 `$size` 

 `$all`

- Initial
    
    ```bash
    > use user
    switched to db user
    ```
    

Find all hobbies that are sports.

This won't work as this will try to get 100% matching object `{ hobbies: 'Sports' }` which does not match.

```bash
> db.users.find({hobbies: 'Sports'}).pretty()
```

Also find nothing when using a nested document(as it does not has a 100% match) as it also has `“frequency”: 3`

```bash
> db.users.find({hobbies: {title:'Sports'}}).pretty()
```

This also can not be a perfect value(as frequency can have other values but our goal is not that)

```bash
> db.users.find({hobbies: {title:'Sports', frequency: 2}}).pretty()
```

Act as an **embedded document**. This is path embedded approach not only on directly embedded documents. This is similar to multiple embedded documents query.

```cpp
> db.users.find({'hobbies.title': 'Sports'}).pretty()
**Output**
{
	"_id" : ObjectId("5f172a343a76a40cd42b836a"),
	"name" : "Max",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 3
		},
		{
			"title" : "Cooking",
			"frequency" : 6
		}
	],
	"phone" : 123495334
}
{
	"_id" : ObjectId("5f172c593a76a40cd42b836c"),
	"name" : "Anna",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 2
		},
		{
			"title" : "Yoga",
			"frequency" : 3
		}
	],
	"phone" : 1234953345,
	"age" : null
}
```

# `$size`

### Want to find all users who have exactly 3 hobbies

```cpp
//Inserting data
> db.users.insertOne({name: 'Chris', hobbies: ['Sports', 'Cooking', 'Hiking']})
//Query
> db.users.find({'hobbies': {$size: 3}}).pretty()
```

> If want to query hobbies greater than 3 or smaller. It does not support MongoDB
> 

# `$all`

- init
    
    ```cpp
    > use boxOffice
    > db.moviestarts.find().pretty()
    ```
    

Want to find movies that have a genre of exactly thriller and action and also in the same order first action then thriller. **Order maters**.

```cpp
> db.moviestarts.find({genre: ['action', 'thriller']}).pretty()
```

`$all` find if the array has all fields like 'action', and 'thriller' **order** does **not matter** but have only those two elements. 

```cpp
> db.moviestarts.find({genre: {$all: ['action', 'thriller']}}).pretty()
```

Want to find all users who have a hobby of sports and the frequency should be greater than or equal to 2.

```cpp
// if we change the query replace 2 with 3. does not work properly
> db.users.find({$and: [{'hobbies.title': 'Sports'},{'hobbies.frequency': {$gte:2}}]}).pretty()
{
	"_id" : ObjectId("5f172a343a76a40cd42b836a"),
	"name" : "Max",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 3
		},
		{
			"title" : "Cooking",
			"frequency" : 6
		}
	],
	"phone" : 123495334
}
{
	"_id" : ObjectId("5f172c593a76a40cd42b836c"),
	"name" : "Anna",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 2
		},
		{
			"title" : "Yoga",
			"frequency" : 3
		}
	],
	"phone" : 1234953345,
	"age" : null
}
```

> This work with different(all available) embedded document but we do not want that. it does not give the expected result.
> 

For "Anna" in the first document, the title is "Sports" and the frequency is 2(we want greater than or equal to 3) and in the second document frequency is 3 so it returns true which we do not want. This applies to all conditions on a single document but also on multiple documents. 

```cpp
> db.users.find({$and: [{'hobbies.title': 'Sports'},{'hobbies.frequency': {$gte:3}}]}).pretty()

{
	"_id" : ObjectId("5f172a343a76a40cd42b836a"),
	"name" : "Max",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 3
		},
		{
			"title" : "Cooking",
			"frequency" : 6
		}
	],
	"phone" : 123495334
}
{
	"_id" : ObjectId("5f172c593a76a40cd42b836c"),
	"name" : "Anna",
	"hobbies" : [
		{
			"title" : "Sports",
			"frequency" : 2
		},
		{
			"title" : "Yoga",
			"frequency" : 3
		}
	],
	"phone" : 1234953345,
	"age" : null
}

> db.users.find({$and: [{'hobbies.title': 'Sports'},{'hobbies.frequency': {$gte:3}}]}).pretty().count()
```

# `$elemMatch`

we want to ensure that query/condition have to perform into same document/element at a time. If all conditions are true then will return that document.

Performing queries into a single document.

```cpp
> db.users.find({hobbies: {$elemMatch:{title: 'Sports', frequency: {$gte: 3}}}}).pretty()
**Output**
{
	"_id" : ObjectId("5f172a343a76a40cd42b836a"),
	"name" : "Max",
	"hobbies" : [
		{ 
			"title" : "Sports",
			"frequency" : 3
		},
		{
			"title" : "Cooking",
			"frequency" : 6
		}
	],
	"phone" : 123495334
}
```