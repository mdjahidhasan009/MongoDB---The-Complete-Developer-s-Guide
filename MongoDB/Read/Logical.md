# Logical

`$or` `$and` `$not` `$nor`

- `$nor` => operation on an array of one or more query expressions and selects the documents that fail all the query expressions in the array.

# Multiple Query

### `$or`, `$nor`

```bash
//Total number of documents with an average rating less than 5 or greater than 9.3 nothing in between

> db.movies.find({$or: [{"rating.average": {$lt: 5}},{"rating.average": {$gt: 9.3}}]}).count()
> db.movies.find({$or: [{"rating.average": {$lt: 5}},{"rating.average": {$gt: 9.3}}]}).pretty()

//Total number of documents with no average rating less than 5 or not greater than 9.3 means none of the conditions matched.
> db.movies.find({$nor: [{"rating.average": {$lt: 5}},{"rating.average": {$gt: 9.3}}]}).count()
```

### `$and`

```bash
// this is the older command

//Total number of documents with an average rating greater than 5 and genres is Drama

> db.movies.find({$and: [{"rating.average": {$gt: 9}},{genres : "Drama"}]}).count()
> db.movies.find({$and: [{"rating.average": {$gt: 9}},{genres : "Drama"}]}).pretty()

// latest command (using the only document) result is same as above({$and: [{"rating.average": {$gt: 9}},{genres : "Drama"}]})

//Total number of documents with an average rating greater than 5 and genres is Drama
> db.movies.find({"rating.average": {$gt: 9}, genres : "Drama"}).count()
```

This does not work, does not give the right value(some time) same object is not permitted in this way here issue is created by the same JSON key genres, this key value replaces the first one when executing the second

```bash
> db.movies.find({genres : "Drama", genres: 'Horror'}).count()

// this become like this
> db.movies.find({genres: 'Horror'}).count()
```

 We have to use `$and` in the same field/key in this issue, we have to use `$and` must. (In this case, we must have to use and)

```bash
> db.movies.find({$and: [{genres : "Drama"}, {genres: 'Horror'}]}).count()
> db.movies.find({runtime: {$not :{$eq: 60}}}).count()
```

### `$not`

`$not` just inverse the logic of the query operator, this is also equal to the 

```cpp
> db.movies.find({runtime: {$not: {$eq: 60}}}).count()
//equals to db.movies.find({runtime: {$ne: 60}}).count()
```