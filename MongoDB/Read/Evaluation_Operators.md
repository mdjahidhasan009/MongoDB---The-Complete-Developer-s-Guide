# Evaluation Operators

`$expr` `$regex` `$text` `$where`

`$expr` ⇒ Compare two fields inside one document then return that fields.

`$regex` ⇒ Allows text search.

### `$regex`

Return the document that found the word. it is not the best way(efficient) to find text in this way

```bash
> db.movies.find({summary: {$regex: /musical/}}).pretty()
```

### `$expr`
<details>
  <summary>Inserting Demo Data</summary>

  ```schema
    > use financialDatalet 
    switched to db financialData
    > db.sales.insertMany([{volume: 100, target: 120},{volume: 89, target: 80},{volume: 200, target: 177}])

    > db.sales.find().pretty()
    {
    	"_id" : ObjectId("5f17491c3a76a40cd42b836d"),
    	"volume" : 100,
    	"target" : 120
    }
    {
    	"_id" : ObjectId("5f17491c3a76a40cd42b836e"),
    	"volume" : 89,
    	"target" : 80
    }
    {
    	"_id" : ObjectId("5f17491c3a76a40cd42b836f"),
    	"volume" : 200,
    	"target" : 177
    }
  ```
</details>
    
    
    

We want to find all entries and all items in this collection where the volume is above the target.

we have to use single/double quotes in the query, have to pass reference fields name have use dollar signs before fields also. This worked successfully.

```bash
> db.sales.find({$expr: {$gt: ['$volume', '$target']}}).pretty()
**Output**
{
	"_id" : ObjectId("5f17491c3a76a40cd42b836e"),
	"volume" : 89,
	"target" : 80
}
{
	"_id" : ObjectId("5f17491c3a76a40cd42b836f"),
	"volume" : 200,
	"target" : 177
}
```

This will not work cause do not use the `$` sign.

```bash
> db.sales.find({$expr: {$gt: ['volume', 'target']}}).pretty()
```

### `$cond` **⇒ conditional because we are in the document.**

This is actually from the aggregation framework.

> If the volume is greater than or equal to 190 then subtract 10 from the volume and use subtracted value for further operation else use the original value of volume for further operation and we get volume dynamically.
> 

```bash
{$cond: {if: {$gte: ['$volume', 190]}, then: {$subtract: ['$volume', 10]}, else: '$volume'}}
// db.sales.find({$expr: {$gt: ['$volume', '$target']}}).pretty()
```

```bash
> db.sales.find({$expr: {$gt: [{$cond: {if: {$gte: ['$volume', 190]}, then: {$subtract: ['$volume', 10]}, else: '$volume'}}, '$target']}}).pretty()
**Output**
{
	"_id" : ObjectId("5f17491c3a76a40cd42b836e"),
	"volume" : 89,
	"target" : 80
}
{
	"_id" : ObjectId("5f17491c3a76a40cd42b836f"),
	"volume" : 200,
	"target" : 177
}
```

> If I increase the subtracted value logically then the result might be changed.
> 

```bash
> db.sales.find({$expr: {$gt: [{$cond: {if: {$gte: ['$volume', 190]}, then: {$subtract: ['$volume', 30]}, else: '$volume'}}, '$target']}}).pretty()
**Output**
{
	"_id" : ObjectId("5f17491c3a76a40cd42b836e"),
	"volume" : 89,
	"target" : 80
}
```