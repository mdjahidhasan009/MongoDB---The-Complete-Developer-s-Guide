# Comparison

`$ne` `$eq` `$lt` `$lte` `$gt` `$gte` `$in` `$nin`

- `$ne` ⇒ not equal
- `$eq` ⇒ equal
- `$lt` ⇒ less/lower than
- `$lte` ⇒ lower than or equal
- `$gt` ⇒ greater than
- `$gte` ⇒ greater than or equal
- `$in` ⇒ selects the documents where the value of a field **equals any value** in the **specified array**.
- `$nin` ⇒ selects the documents where the value of a field **does not equal any value** in the **specified array**.

`find()` ⇒ returns cursor and technically gives us all documents but we have to iterate. For the shell, it gives the first 20 documents.

`findOne()` ⇒ same as the find and give back the first matched document also no cursor.

### Code Examples

```bash
> db.movies.find({runtime: 60}).pretty()
> db.movies.findOne({runtime: 60})

> db.movies.find({runtime: {$ne: 60}}).pretty()
> db.movies.find({runtime: {$lt: 40}}).pretty()
> db.movies.find({runtime: {$lte: 40}}).pretty()
> db.movies.find({runtime: {$gt: 40}}).pretty()
> db.movies.find({runtime: {$gte: 40}}).pretty()
```

### Query into Embedded array

Will return documents that have Drama in genres array. There can be other element in genres array(like "genres": [ "Drama", "Action", "Anime", "Horror" ]) or can be only Drama.

```bash
> db.movies.find({genres:"Drama"}).pretty()
```

To exact query (exactly this "genres": ["Drama"] array in genres). Will be no other element in the array

```bash
> db.movies.find({genres:["Drama"]}).pretty()
```

It will find us all documents that have a runtime of 30 or 42 but not 60 

```cpp
//[30,42] --> this is set of values not a range
//{$in:[30,42]} => will select all document in which runtime have 30 or 42.
//{$nin:[30,42]} => will select all document in which runtime have not 30 or 42.
> db.movies.find({runtime: {$in:[30,42]}}).pretty()
> db.movies.find({runtime: {$nin:[30,42]}}).pretty()
```

### Embedded Documents

In embedded documents have to use must quotes.

```bash
> db.movies.find({"rating.average": {$gt: 7}}).pretty()
```