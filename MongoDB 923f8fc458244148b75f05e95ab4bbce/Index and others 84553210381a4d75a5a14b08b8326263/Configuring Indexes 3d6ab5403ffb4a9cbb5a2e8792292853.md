# Configuring Indexes

### Make all key values unique

Every collection _id is unique by default. Also can make any key not to add the same value to the document if we make `{unique: true}`. This will ensure all values of the given key will be unique. 

```cpp
> db.contacts.createIndex({email: 1}, {unique: true})
**Output**
{
	"ok" : 0,
	"errmsg" : "E11000 duplicate key error collection: contactData.contacts index: email_1 dup key: { : \"abigail.clark@example.com\" }",
	"code" : 11000,
	"codeName" : "DuplicateKey"
}
```

### Understanding Partial Filters

Creating an index on age, not on gender but age but only for elements where the underlying document is for a male. This means documents that have gender female will not include in the index.

```cpp
db.contacts.createIndex({'dob.age': 1}, {partialFilterExpression:{gender: 'male'}})
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```

Also can also use the age query

```cpp
> db.contacts.createIndex({'dob.age': 1}, {partialFilterExpression:{'dob.age': {$gt: 60}}})
```

This does not work. because as a partial index we have to also use gender and do not specify it in query so it will also return the document with the gender female also.

```cpp
> db.contacts.explain().find({'dob.age': {$gt: 60}})
```

This works with an index scan

```cpp
db.contacts.explain().find({'dob.age': {$gt: 60}, gender: 'male'})
```

### Applying the Partial Index

```cpp
> db.users.insertMany([{name: 'Max', email: 'max@test.com'},{name: 'Manu'}])
```

Implementing a unique key with email

```cpp
> db.users.createIndex({email: 1}, {unique: true})
```

If now want to add a new user without email, it says duplicate index error, because no values store twice. (If there is a collection without email)

```cpp
> db.users.insertOne({name: 'Anna'})
```

But a person could not have an email

```cpp
> db.users.dropIndex({email: 1})
```

Now we create indexes a bit differently.

```cpp
> db.users.createIndex({email: 1},{unique: true, partialFilterExpression: {email: {$exists: true}}})
```

Now add users without email, it works as exists true for null also

```cpp
db.users.insertOne({name: 'Anna'})
```

Now we have three users one has email and the other two do without email

```cpp
db.users.find().pretty()
```

As a partial index has already been created we can not add a new user with the same email

```cpp
db.users.insertOne({name: 'Anna', email: 'max@test.com'})
```