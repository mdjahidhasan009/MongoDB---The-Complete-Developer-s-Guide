# Read

1. Methods, Filters & Operations
2. Query Selectors (READ)
3. Projection Operators(READ)


>💡 **Aggregation:** This is reading data from the database but it allows to set up of a pipeline of stages to funnel the data throw and permits a bunch of operations that allow the user to shape the data and get back from the database to the form needed in a particular app.


```bash
db.myCollection.find({age: 30)
=> here {age: 30 } 
	--> Filter 
	  age -> Field, 
	  32 --> Value

db.myCollection.find({age: { $gt: 30}})
=> {age: { $gt: 30}} 
	--> Filter(Range) 
			&gt --> Operator
```
    

### Operators

| Read | Update |  |  |
| --- | --- | --- | --- |
| Query & Projection | Update | Query Modifiers | Aggregation |
| Query Selectors | Fields | Change Query Behaviors | Pipeline Stages |
| Projection Operators | Arrays | This is Deprecated now | Pipeline Operators |

### How Operators Impact Our Data

| Type | Purpose | Changes Data | Example |
| --- | --- | --- | --- |
| Query Operator | Locate Data | blocked | $eq |
| Projection Operator | Modify data presentation | blocked | $ |
| Update Operator | Modify + add additional data | not blocked | &inc |

<details>
  <summary>`findOne()` ⇒ First matching document(if filter given) or first element of document | Simple movie sample</summary>
  
  ```bash
  > use movieData
  switched to db movieData
  > cls
  
  // findOne() => First matching document
  
  > db.movies.findOne()
  {
      "_id" : ObjectId("5f15a22a9bfbc37d06f66616"),
      "id" : 1,
      "url" : "http://www.tvmaze.com/shows/1/under-the-dome",
      "name" : "Under the Dome",
      "type" : "Scripted",
      "language" : "English",
      "genres" : [
          "Drama",
          "Science-Fiction",
          "Thriller"
      ],
      "status" : "Ended",
      "runtime" : 60,
      "premiered" : "2013-06-24",
      "officialSite" : "http://www.cbs.com/shows/under-the-dome/",
      "schedule" : {
          "time" : "22:00",
          "days" : [
              "Thursday"
          ]
      },
      "rating" : {
          "average" : 6.5
      },
      "weight" : 91,
      "network" : {
          "id" : 2,
          "name" : "CBS",
          "country" : {
              "name" : "United States",
              "code" : "US",
              "timezone" : "America/New_York"
          }
      },
      "webChannel" : null,
      "externals" : {
          "tvrage" : 25988,
          "thetvdb" : 264492,
          "imdb" : "tt1553656"
      },
      "image" : {
          "medium" : "http://static.tvmaze.com/uploads/images/medium_portrait/0/1.jpg",
          "original" : "http://static.tvmaze.com/uploads/images/original_untouched/0/1.jpg"
      },
      "summary" : "<p><b>Under the Dome</b> is the story of a small town that is suddenly and inexplicably sealed off from the rest of the world by an enormous transparent dome. The town's inhabitants must deal with surviving the post-apocalyptic conditions while searching for answers about the dome, where it came from and if and when it will go away.</p>",
      "updated" : 1529612668,
      "_links" : {
          "self" : {
              "href" : "http://api.tvmaze.com/shows/1"
          },
          "previousepisode" : {
              "href" : "http://api.tvmaze.com/episodes/185054"
          }
      }
  }
  ```

</details>
    
    
    

# Query Selectors & Projection

## Query Selectors

[Comparison](Read/Comparison.md)

[Logical](Read/Logical.md)

[Element](Read/Element.md)

[Evaluation Operators](Read/Evaluation_Operators.md)

[Array](Read/Array.md)

Comments

Geospatial(special)

### Projections Operators

1. $
2. $elemMatch 
3. $meta
4. $slice

[Projection](Read/Projection.md)

# Understanding Cursors

When we use `find()` can get the all data like 100 million

It can reduce if we include a query.

Here cursors a pointer. cursor request batch to the server every time to get that data.

If have a query that meets 1000 documents, but let's say you have a website where you only display 10 items, let's say 10 products you fetched at a time anyways, then there is no need to load all thousand results that matched your query right at the start. Instead, you would only fetch the first 10, display them on the screen and then go ahead and fetch the next 10, when the user navigated to the next page or anything like that. This is the idea behind a cursor.

```cpp
> use movieData
> db.movies.find().count()
240
```

Basically it returns 20 elements

```cpp
db.movies.find().pretty()
// type it for more
> it
```

Get exactly one document, because next() gives the next document.

`db.movies.find().next()` => giving this command will return the same document over and over as it works from scratch(from first) every time.

```cpp
> db.movies.find().next()
```

We can use JavaScript syntax in the mongo shell(We will get documents one by one as we stored them)

```bash
> const dataCursor = db.movies.find()
> dataCursor.next() => first element
> dataCursor.next() => second element
```

`printjson()` is a mongo shell function that helps to print something into shell it fetched all documents(so it will not return cursor)

```bash
dataCursor.forEach(document => {printjson(document)})
```

Check have any next value

```bash
dataCursor.hasNext()
```

Fetching data with sort(), One means ascending

```bash
db.movies.find().sort({'rating.average': 1}).pretty()
```

eMinus one mean descending

```bash
db.movies.find().sort({'rating.average': -1}).pretty()e
```

Sort with multiple documents, here first sort the `'rating.average'` and if `'rating.average'` have the same value into particular indexes and then runtime executes with descending if may exist.

```bash
> db.movies.find().sort({'rating.average': 1, runtime: -1}).pretty()
```

The cursor also works with sort()

```bash
> db.movies.find().sort({'rating.average': 1, runtime: -1}).next()
```

We can skip a certain amount of elements, it is effective in pagination, when we work with pagination on the second page we need data from the 11th element, we can skip the previous 10 elements.

```bash
db.movies.find().sort({'rating.average': 1, runtime: -1}).skip(10).pretty()
```

`skip()` is used to specify the number of documents to skip at the first time.
We can still include `limit()` limit return the exact number of elements will be retrieved at a time.

```bash
db.movies.find().sort({'rating.average': 1,runtime: -1}).skip(100).limit(10).pretty()
```

Here order does not matter. Order check from right such(previous example) : `sort()` -> `skip()` -> `limit()` . what method we **write first**, it **executes first**.