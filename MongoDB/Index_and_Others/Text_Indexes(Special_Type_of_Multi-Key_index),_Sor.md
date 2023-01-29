# Text indexes(Special Type of Multi-Key index), Sorting using Text indexes, Combine Text index, Exclude while find in Text-Index

This is a special type of multi-key index

This product is a must-buy for all fans of modern fiction! Using the text index all words of this sentence will be stored as an array of words, the text index will be like: this, product, must buy, fans, modern, fiction. If an array of single words or an array of keywords is essential to search text.

Create a new collections

```cpp
db.products.insertMany(
		[
				{
						title: 'A book', 
						description: 'This is an awesome book about a young artist!'
				}, 
				{
						title: 'Red T-Shirt', description: 'This T-Shirt is red and it is pretty awesome'
				}
		]
)
**Output**
{
	"acknowledged" : true,
	"insertedIds" : [
		ObjectId("6330504b8fea738a87154400"),
		ObjectId("6330504b8fea738a87154401")
	]
}
```

Create an index. This is a single field index and can search with exact text such as `'This is an awesome book about a young artist!'`. Can not index individual words of description key’s value.

```cpp
db.products.createIndex({description: 1})
```

To create a text index to split the sentence, so drop the previous index

```cpp
db.products.dropIndex({description: 1})
```

### Create text index ⇒ special kind of index

In the text, the index removes all the stop words(such as space) and stores all the keywords into an array essentially.

```cpp
db.products.createIndex({description: 'text'})
**Output**
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```

Now might be wondering why do not need to specify the field in which want to search pretty expensive as can imagine.
If have is a lot of long text that has to be split up, don't want to do this like 10 times per collection and therefore, only have one text index where this could look into.

Can merge multiple fields into one text index. Everything is stored in lowercase.

```cpp
db.products.find({$text: {$search: 'awesome'}}).pretty()
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a4"),
	"title" : "Red T-Shirt",
	"description" : "This T-Shirt is red and it is pretty awesome"
}
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a3"),
	"title" : "A book",
	"description" : "This is an awesome book about a young artist!"
}
```

```cpp
> db.products.find({$text: {$search: 'book'}}).pretty()
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a3"),
	"title" : "A book",
	"description" : "This is an awesome book about a young artist!"
}
```

Here second document has red word and first document has book word. Not searching the words in a single document. 

```cpp
db.products.find({$text: {$search: 'red book'}}).pretty()
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a3"),
	"title" : "A book",
	"description" : "This is an awesome book about a young artist!"
}
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a4"),
	"title" : "Red T-Shirt",
	"description" : "This T-Shirt is red and it is pretty awesome"
}
```

Can search with exactly phrase. **This faster than regular expression.**

```cpp
db.products.find({$text: {$search: "\"red book\""}}).pretty()
db.products.find({$text: {$search: "\"awesome book\""}}).pretty()
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a3"),
	"title" : "A book",
	"description" : "This is an awesome book about a young artist!"
}
```

# Text Indexes Sorting

It works in new version automatically

```cpp
db.products.find({$text: {$search: "awesome t-shirt"}}).pretty()
**Output**
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a4"),
	"title" : "Red T-Shirt",
	"description" : "This T-Shirt is red and it is pretty awesome"
}
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a3"),
	"title" : "A book",
	"description" : "This is an awesome book about a young artist!"
}
```

// but in the previous version

```cpp
db.products.find({$text: {$search: "awesome t-shirt"}}).pretty()
**Output**
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a3"), 
	"title" : "A book",
	"description" : "This is an awesome book about a young artist!"
}
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a4"),
	"title" : "Red T-Shirt",
	"description" : "This T-Shirt is red and it is pretty awesome"
}
```

Let's add a sorting query. in this query check how many words match each document. Score increase with the number of matching words. Which score is higher comes into first position

```cpp
db.products.find({$text: {$search: "awesome t-shirt"}}, {score: {$meta: 'textScore'}}).pretty()
**Output**
{
	"_id" : ObjectId("6330504b8fea738a87154401"),
	"title" : "Red T-Shirt",
	"description" : "This T-Shirt is red and it is pretty awesome",
	"score" : 1.7999999999999998
}
{
	"_id" : ObjectId("6330504b8fea738a87154400"),
	"title" : "A book",
	"description" : "This is an awesome book about a young artist!",
	"score" : 0.625
}
```

If sort does not work automatically add the sort function and sort by score.

```cpp
db.products.find(
		{$text: {$search: "awesome t-shirt"}}, 
		{score: {$meta: 'textScore'}}
).sort(
		{score: {$meta: 'textScore'}}).pretty()
**Output**
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a4"),
	"title" : "Red T-Shirt",
	"description" : "This T-Shirt is red and it is pretty awesome",
	"score" : 1.7999999999999998
}
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a3"),
	"title" : "A book",
	"description" : "This is an awesome book about a young artist!",
	"score" : 0.6
}
```

# Combining Text Indexes

```cpp
db.products.getIndexes()
db.products.findOne()
{
"_id" : ObjectId("5f2adb2fbcaaeedce48e55a3"),
"title" : "A book",
"description" : "This is an awesome book about a young artist!"
}
```

If we now add text indexes with a title like that it would be an error. Already text index is added with description in the document. Index option conflict. In every document we can must add only one text index

```cpp
db.products.createIndex({title: 'text'})
```

Can merge the text of multiple fields together into one text index. Now drop the previous description text index, dropping the text index is a little bit different. Have included the text index name

```cpp
db.products.dropIndex('description_text')
{ "nIndexesWas" : 2, "ok" : 1 }
```

Now add two fields like title and description to create a **combined text index**

```cpp
db.products.createIndex({title:'text',description: 'text'})
**Output**
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}

// insert a new element
db.products.insertOne({title: 'A Ship', description: 'Floats perfectly!'})
**Output**
{
	"acknowledged" : true,
	"insertedId" : ObjectId("63308f298fea738a87154402")
}

```

As we make combined text index with title and description if we search for ship then we will get matched docuemnt.

```bash
> db.products.find({$text: {$search: 'ship'}}).pretty()
**Output**
{
	"_id" : ObjectId("63308f298fea738a87154402"),
	"title" : "A Ship",
	"description" : "Floats perfectly!"
}
```

Search with multiple text

```cpp
db.products.find({$text: {$search: 'ship t-shirt'}}).pretty()
{
	"_id" : ObjectId("63308f298fea738a87154402"),
	"title" : "A Ship",
	"description" : "Floats perfectly!"
}
{
	"_id" : ObjectId("6330504b8fea738a87154401"),
	"title" : "Red T-Shirt",
	"description" : "This T-Shirt is red and it is pretty awesome"
}

> db.products.find({$text: {$search: 'awesome'}}).pretty()
{
	"_id" : ObjectId("6330504b8fea738a87154400"),
	"title" : "A book",
	"description" : "This is an awesome book about a young artist!"
}
{
	"_id" : ObjectId("6330504b8fea738a87154401"),
	"title" : "Red T-Shirt",
	"description" : "This T-Shirt is red and it is pretty awesome"
}
```

# Using Text Index To Exclude Words

To exclude words in search just add '-' before word. Here want to search awesome but in the sentence. If get awesome then exclude t-shirt

```cpp
db.products.find({$text: {$search: 'awesome -t-shirt'}}).pretty()
{
"_id" : ObjectId("5f2adb2fbcaaeedce48e55a3"),
"title" : "A book",
"description" : "This is an awesome book abou a young artist!"
}
```