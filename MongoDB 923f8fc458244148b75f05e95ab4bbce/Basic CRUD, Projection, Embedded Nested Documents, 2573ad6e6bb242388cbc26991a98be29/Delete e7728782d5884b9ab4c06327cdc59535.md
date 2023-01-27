# Delete

```scheme
deleteOne(filter, options)
deleteMany(filter, options)
```

Will delete the first document where departureAirport is MUC.

```scheme
db.flightData.deleteOne({departureAirport :"MUC"})
{ "acknowledged": true, "deletedCount" : 1 } => Output
```

Will delete all documents which **have** a **key marker** and value toDelete in flightData collection.

```scheme
> db.flightData.deleteMany({marker: "toDelete"})
```

Will delete all documents in flightData collection as there is no filter.

```scheme
> db.flightData.deleteMany( {} )
```

**Delete a collection from database**

```scheme
> db.numbers.drop() => here numbers is the collection name
```