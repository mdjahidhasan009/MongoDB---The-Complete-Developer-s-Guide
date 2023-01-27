# Compound index with text

```cpp
> db.contacts.createIndex({gender: 1})
// get explain after creating index
> db.contacts.explain("executionStats").find({gender: "male"})
```

### Want to find all persons who are older than 30 and male or older than 40 and male.

When using multiple fields for queries in the index, basically one combined index is created from multiple fields. Here created one combined index from two fields.
**Every time have to drop the index if the new index(which we want to create) uses the same field**

Such as

```cpp
> db.contacts.dropIndex({'gender': 1})
> db.contacts.createIndex({'dob.age': 1,'gender': 1})
**Output**
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```

Getting info from the query

```cpp
> db.contacts.explain().find({'dob.age': 35,'gender': 'male'})
```

```bash
> db.contacts.getIndexes()
**Output**
[
	{
		"v" : 2,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "contactData.contacts"
	},
	{
		"v" : 2,
		"key" : {
			"dob.age" : 1,
			"gender" : 1
		},
		"name" : "dob.age_1_gender_1",
		"ns" : "contactData.contacts"
	}
]
```

If want to execute single index from multiple combined index its work left to right `"indexName" : "dob.age_1_gender_1"`

`db.contacts.explain().find({'dob.age': 35})` It works fine, which means it works with an index scan. But `db.contacts.explain().find({'gender': 'male'})` it will do normal scan. As the index will be like `33_male`, `33_male`, and `33_female` So it will be sorted for the first key, not for the second. So if it is multiple keys in an index such as 4(first_second_third_fourth) then if we combine left to right in find condition such as first / first_second / first_second_third / first_second_third_fourth it will use index search. Any discontinue left to right will not perform index search rather will use normal collection search such as second_third, first_third, etc.