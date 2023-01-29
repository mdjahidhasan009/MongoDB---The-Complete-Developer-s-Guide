# Array

# Pushing Elements Into Newly Created Arrays

- Adding initial data
    
    ```bash
    > db.friends.insertMany(
    	[
    	  {
    	    "name": "Max",
    	    "hobbies": ["Sports", "Cooking"],
    	    "age": 29,
    	    "examScores": [
    	      { "difficulty": 4, "score": 57.9 },
    	      { "difficulty": 6, "score": 62.1 },
    	      { "difficulty": 3, "score": 88.5 }
    	    ]
    	  },
    	  {
    	    "name": "Manu",
    	    "hobbies": ["Eating", "Data Analytics"],
    	    "age": 30,
    	    "examScores": [
    	      { "difficulty": 7, "score": 52.1 },
    	      { "difficulty": 2, "score": 74.3 },
    	      { "difficulty": 5, "score": 53.1 }
    	    ]
    	  },
    	  {
    	    "name": "Maria",
    	    "hobbies": ["Cooking", "Skiing"],
    	    "age": 29,
    	    "examScores": [
    	      { "difficulty": 3, "score": 75.1 },
    	      { "difficulty": 8, "score": 44.2 },
    	      { "difficulty": 6, "score": 61.5 }
    	    ]
    	  }
    	]
    )
    
    **Output**
    {
      acknowledged: true,
      insertedIds: {
        '0': ObjectId("6342b193861a3d80d340bf17"),
        '1': ObjectId("6342b193861a3d80d340bf18"),
        '2': ObjectId("6342b193861a3d80d340bf19")
      }
    }
    ```
    

Push Operator allows pushing a new element into the all hobbies array for every incoming document. This means pushing the whole individual array of a document as an element(nested array).

```cpp
> db.friends.aggregate([
     { 
					$group: { 
							_id: { age: '$age' }, 
							allHobbies: {$push: '$hobbies'}
					}
			}
 ]).pretty()

**Output**
{
	"_id" : {
		"age" : 29
	},
	"allHobbies" : [
		[
			"Sports",
			"Cooking"
		],
		[
			"Cooking",
			"Skiing"
		]
	]
}
{
	"_id" : {
		"age" : 30
	},
	"allHobbies" : [
		[
			"Eating",
			"Data Analytics"
		]
	]
}
```

# Understanding the unwind Stage

Do not want to insert into a nested array. The unwind stage is always a great stage when having an array of which want to pull out the elements. Will take out every element in an array and will create a document with that. (This will lead many data duplication).

```cpp
db.friends.aggregate([
	{ $unwind:'$hobbies' }
]).pretty()

**Output**
[
  {
    _id: ObjectId("6342b193861a3d80d340bf17"),
    name: 'Max',
    hobbies: 'Sports',
    age: 29,
    examScores: [
      { difficulty: 4, score: 57.9 },
      { difficulty: 6, score: 62.1 },
      { difficulty: 3, score: 88.5 }
    ]
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf17"),
    name: 'Max',
    hobbies: 'Cooking',
    age: 29,
    examScores: [
      { difficulty: 4, score: 57.9 },
      { difficulty: 6, score: 62.1 },
      { difficulty: 3, score: 88.5 }
    ]
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf18"),
    name: 'Manu',
    hobbies: 'Eating',
    age: 30,
    examScores: [
      { difficulty: 7, score: 52.1 },
      { difficulty: 2, score: 74.3 },
      { difficulty: 5, score: 53.1 }
    ]
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf18"),
    name: 'Manu',
    hobbies: 'Data Analytics',
    age: 30,
    examScores: [
      { difficulty: 7, score: 52.1 },
      { difficulty: 2, score: 74.3 },
      { difficulty: 5, score: 53.1 }
    ]
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf19"),
    name: 'Maria',
    hobbies: 'Cooking',
    age: 29,
    examScores: [
      { difficulty: 3, score: 75.1 },
      { difficulty: 8, score: 44.2 },
      { difficulty: 6, score: 61.5 }
    ]
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf19"),
    name: 'Maria',
    hobbies: 'Skiing',
    age: 29,
    examScores: [
      { difficulty: 3, score: 75.1 },
      { difficulty: 8, score: 44.2 },
      { difficulty: 6, score: 61.5 }
    ]
  }
]
```

**result ⇒** every array element will get a document.

Now add a group to every document according to age

```cpp
> db.friends.aggregate([
     { $unwind: '$hobbies' },
     { $group: { _id: { age: '$age' }, allHobbies: { $push: '$hobbies' } } }
 ]).pretty()

**Output**
{
	"_id" : {
		"age" : 29
	},
	"allHobbies" : [
		"Sports",
		"Cooking",
		"Cooking",
		"Skiing"
	]
}
{
	"_id" : {
		"age" : 30
	},
	"allHobbies" : [
		"Eating",
		"Data Analytics"
	]
}
```

# Eliminating Duplicate Values

But can see that have some duplicate values instead of $push have to use `$addToSet`
`$addToSet` pushes but avoid duplicating element.

```cpp
db.friends.aggregate([
    {
        $unwind: '$hobbies'
    },
    {
        $group: {
            _id: {
                age: '$age'
            },
            allHobbies: {
                $addToSet: '$hobbies'
            }
        }
    }
]).pretty()
{
	"_id" : {
		"age" : 29
	},
	"allHobbies" : [
		"Skiing",
		"Sports",
		"Cooking"
	]
}
{
	"_id" : {
		"age" : 30
	},
	"allHobbies" : [
		"Eating",
		"Data Analytics"
	]
}
```

# Using projection, `$slice` with Arrays

Want to print the first document from `examScores` arrays. [Link of the whole document](Array.md)

1 ⇒ means get first element from start or front or array

```cpp
> db.friends.aggregate([
    {
        $project: {
            _id: 0,
            examScore: {
                $slice: ['$examScores', 1]
            }
        }
    }
]).pretty()

**Output**
[
  { examScore: [ { difficulty: 4, score: 57.9 } ] },
  { examScore: [ { difficulty: 7, score: 52.1 } ] },
  { examScore: [ { difficulty: 3, score: 75.1 } ] }
]
```

2⇒ Means two elements from start or front of a array 

```bash

> db.friends.aggregate([
    {
        $project: {
            _id: 0,
            examScore: {
                $slice: ['$examScores', 2]
            }
        }
    }
]).pretty()

**Output**
[
  {
    examScore: [ 
			{ difficulty: 4, score: 57.9 }, 
			{ difficulty: 6, score: 62.1 } ]
  },
  {
    examScore: [ 
			{ difficulty: 7, score: 52.1 }, 
			{ difficulty: 2, score: 74.3 } ]
  },
  {
    examScore: [ 
			{ difficulty: 3, score: 75.1 }, 
			{ difficulty: 8, score: 44.2 } ]
  }
]
```

-1 ⇒ means get first element from last or end of a array

```cpp
> db.friends.aggregate([
    {
        $project: {
            _id: 0,
            examScore: {
                $slice: ['$examScores', -1]
            }
        }
    }
]).pretty()

**Output**
[
  { examScore: [ { difficulty: 3, score: 88.5 } ] },
  { examScore: [ { difficulty: 5, score: 53.1 } ] },
  { examScore: [ { difficulty: 6, score: 61.5 } ] }
]
```

-2 ⇒ means get two elements from last or end of a array

```cpp
> db.friends.aggregate([
    {
        $project: {
            _id: 0,
            examScore: {
                $slice: ['$examScores', -2]
            }
        }
    }
]).pretty()

**Output**
[
  {
    examScore: [ { difficulty: 6, score: 62.1 }, { difficulty: 3, score: 88.5 } ]
  },
  {
    examScore: [ { difficulty: 2, score: 74.3 }, { difficulty: 5, score: 53.1 } ]
  },
  {
    examScore: [ { difficulty: 8, score: 44.2 }, { difficulty: 6, score: 61.5 } ]
  }
]
```

Start at position two(from start/front) and give one element

```cpp
> db.friends.aggregate([
    {
        $project: {
            _id: 0,
            examScore: {
                $slice: ['$examScores', 2, 1]
            }
        }
    }
]).pretty()

**Output**
[
  { examScore: [ { difficulty: 3, score: 88.5 } ] },
  { examScore: [ { difficulty: 5, score: 53.1 } ] },
  { examScore: [ { difficulty: 6, score: 61.5 } ] }
]
```

# Getting the length of an array

`$size` calculate the length of an array

There are three document in friend collection and each document has `examScores` document where have array. [See data insertion](Array.md).

```cpp
> db.friends.aggregate([
    {
        $project: {
            _id: 0,
            numScores: {
                $size: '$examScores'
            }
        }
    }
]).pretty()

**Output**
[ { numScores: 3 }, { numScores: 3 }, { numScores: 3 } ]
```

# Using the Filter Operator

`$filter` Operator allows to filter out certain elements of an array and only return the data according to the condition in projection face.

### `$filter` score to get only examScorees that is the greater than 60

Here `sc` is a temporary variable for using condition. `sc` is a temporary variable and will be used inside `$filter` only.

`$gt: ['$$sc.score', 60]` `sc` ⇒ will be treated as string, `$sc` will be treated as field, so we use`$$sc` . `$filter` will check the condition over and over again at all array elements in the `examScores`.
 `$cond` --> condition

Here `$gt` is little bit different than `match` and `find`.

```cpp
db.friends.aggregate([
    {
        $project: {
            _id: 0,
            scores: {
                $filter: {
                    input: '$examScores',
                    as: 'sc',
                    cond: {
                        $gt: ['$$sc.score', 60]
                    }
                }
            }
        }
    }
]).pretty()

**Output**
{
	"scores" : [
		{
			"difficulty" : 6,
			"score" : 62.1
		},
		{
			"difficulty" : 3,
			"score" : 88.5
		}
	]
}
{ "scores" : [ { "difficulty" : 2, "score" : 74.3 } ] }
{
	"scores" : [
		{
			"difficulty" : 3,
			"score" : 75.1
		},
		{
			"difficulty" : 6,
			"score" : 61.5
		}
	]
```

# Applying Multiple Operations to our Array

### Wanted to transform our friend objects such that only output the highest exam score.

Using `$unwind` will make `examScores` array as multiple document per person with the scores being a top level element so that we can sort the document and after that will group by person and take the first score that we find for a person.

First unwind the array and sort.

```cpp
> db.friends.aggregate([
    { $unwind: '$examScores' },
    { $sort: { 'examScores.score': -1 } }
]).pretty()

**Output**
[
  {
    _id: ObjectId("6342b193861a3d80d340bf17"),
    name: 'Max',
    hobbies: [ 'Sports', 'Cooking' ],
    age: 29,
    examScores: { difficulty: 3, score: 88.5 }
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf19"),
    name: 'Maria',
    hobbies: [ 'Cooking', 'Skiing' ],
    age: 29,
    examScores: { difficulty: 3, score: 75.1 }
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf18"),
    name: 'Manu',
    hobbies: [ 'Eating', 'Data Analytics' ],
    age: 30,
    examScores: { difficulty: 2, score: 74.3 }
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf17"),
    name: 'Max',
    hobbies: [ 'Sports', 'Cooking' ],
    age: 29,
    examScores: { difficulty: 6, score: 62.1 }
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf19"),
    name: 'Maria',
    hobbies: [ 'Cooking', 'Skiing' ],
    age: 29,
    examScores: { difficulty: 6, score: 61.5 }
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf17"),
    name: 'Max',
    hobbies: [ 'Sports', 'Cooking' ],
    age: 29,
    examScores: { difficulty: 4, score: 57.9 }
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf18"),
    name: 'Manu',
    hobbies: [ 'Eating', 'Data Analytics' ],
    age: 30,
    examScores: { difficulty: 5, score: 53.1 }
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf18"),
    name: 'Manu',
    hobbies: [ 'Eating', 'Data Analytics' ],
    age: 30,
    examScores: { difficulty: 7, score: 52.1 }
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf19"),
    name: 'Maria',
    hobbies: [ 'Cooking', 'Skiing' ],
    age: 29,
    examScores: { difficulty: 8, score: 44.2 }
  }
]
```

```bash
> db.friends.aggregate([
	{ $unwind: "$examScores" },
  { $project: { _id: 0, name: 1, age: 1, score: "$examScores.score"} },
	{ $sort: { score: -1 } }
])

**Output**
[
  { name: 'Max', age: 29, score: 88.5 },
  { name: 'Maria', age: 29, score: 75.1 },
  { name: 'Manu', age: 30, score: 74.3 },
  { name: 'Max', age: 29, score: 62.1 },
  { name: 'Maria', age: 29, score: 61.5 },
  { name: 'Max', age: 29, score: 57.9 },
  { name: 'Manu', age: 30, score: 53.1 },
  { name: 'Manu', age: 30, score: 52.1 },
  { name: 'Maria', age: 29, score: 44.2 }
]
```

Grouping document together and show the maximum score among a group. We can group by anything not only by `_id`, but for other field there will be duplication so `_id` is the best choose.

```cpp
> db.friends.aggregate([
    { $unwind: '$examScores' },
    { $project: { _id: 1, name: 1, age: 1, score: '$examScores.score'}},
    { $sort: { score: -1 } },
    { $group: { _id: '$_id', maxScore: { $max: '$score'} } }
]).pretty()

**Output**
[
  { _id: ObjectId("6342b193861a3d80d340bf19"), maxScore: 75.1 },
  { _id: ObjectId("6342b193861a3d80d340bf17"), maxScore: 88.5 },
  { _id: ObjectId("6342b193861a3d80d340bf18"), maxScore: 74.3 }
]
```

Show the name and sort with descending order.

```cpp
> db.friends.aggregate([
    { $unwind: '$examScores' },
    { $project: { _id: 1, name: 1, age: 1, score: '$examScores.score' } },
    { $group: { _id: '$_id', name: { $first: '$name' }, maxScore: { $max: '$score' } } },
    { $sort: { maxScore: -1 } }
]).pretty()

**Output**
{
	"_id" : ObjectId("5f318bb939e723820551436d"),
	"name" : "Max",
	"maxScore" : 88.5
}
{
	"_id" : ObjectId("5f318bb939e723820551436f"),
	"name" : "Maria",
	"maxScore" : 75.1
}
{
	"_id" : ObjectId("5f318bb939e723820551436e"),
	"name" : "Manu",
	"maxScore" : 74.3
}
```

Use the first value encounter. `$first` ⇒ means want to get the name value

```bash
> db.friends.aggregate([
    { $unwind: '$examScores' },
    { $project: { _id: 1, name: 1, age: 1, score: '$examScores.score' } },
    { $group: { _id: '$_id', name: { $first: '$name' }, age: { $first: '$age' }, maxScore: { $max: '$score' } } },
    { $sort: { maxScore: -1 } }
]).pretty()

**Output**
[
  {
    _id: ObjectId("6342b193861a3d80d340bf17"),
    name: 'Max',
    age: 29,
    maxScore: 88.5
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf19"),
    name: 'Maria',
    age: 29,
    maxScore: 75.1
  },
  {
    _id: ObjectId("6342b193861a3d80d340bf18"),
    name: 'Manu',
    age: 30,
    maxScore: 74.3
  }
]
```