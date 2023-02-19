# Additional Stages

### Want to find the 10 users, the 10 persons with the oldest birth date, so the lowest birth date

<details>
<summary>See the data format</summary>

```schema
    > db.persons.aggregate([
        {
            $project: {
                _id: 0,
                name: 1,
                birthDate: {
                    $toDate: '$dob.date'
                }
            }
        }
    ]).pretty()
    
    **Output**
    [
      {
        name: { title: 'mr', first: 'carl', last: 'jacobs' },
        birthDate: ISODate("1984-09-30T01:20:26.000Z")
      },
      {
        name: { title: 'mr', first: 'harvey', last: 'chambers' },
        birthDate: ISODate("1988-05-27T00:14:03.000Z")
      },
      {
        name: { title: 'mr', first: 'gideon', last: 'van drongelen' },
        birthDate: ISODate("1971-03-28T04:47:21.000Z")
      },
      {
        name: { title: 'mr', first: 'zachary', last: 'lo' },
        birthDate: ISODate("1988-10-17T03:45:04.000Z")
      },
      {
        name: { title: 'miss', first: 'maeva', last: 'wilson' },
        birthDate: ISODate("1962-08-11T20:51:07.000Z")
      },
      {
        name: { title: 'mrs', first: 'پریا', last: 'پارسا' },
        birthDate: ISODate("1962-01-10T05:26:30.000Z")
      },
      {
        name: { title: 'mrs', first: 'olav', last: 'oehme' },
        birthDate: ISODate("1960-11-28T23:07:18.000Z")
      },
      {
        name: { title: 'mr', first: 'elijah', last: 'lewis' },
        birthDate: ISODate("1986-03-29T06:40:18.000Z")
      },
      {
        name: { title: 'mr', first: 'victor', last: 'pedersen' },
        birthDate: ISODate("1959-02-19T23:56:23.000Z")
      },
      {
        name: { title: 'ms', first: 'louise', last: 'graham' },
        birthDate: ISODate("1971-01-21T20:36:16.000Z")
      },
      {
        name: { title: 'mrs', first: 'madeleine', last: 'till' },
        birthDate: ISODate("1954-05-01T02:34:40.000Z")
      },
      {
        name: { title: 'mr', first: 'isolino', last: 'viana' },
        birthDate: ISODate("1959-03-22T14:53:41.000Z")
      },
      {
        name: { title: 'miss', first: 'mestan', last: 'kaplangı' },
        birthDate: ISODate("1951-12-17T20:03:33.000Z")
      },
      {
        name: { title: 'mrs', first: 'katie', last: 'welch' },
        birthDate: ISODate("1990-10-14T05:02:12.000Z")
      },
      {
        name: { title: 'mr', first: 'بنیامین', last: 'سالاری' },
        birthDate: ISODate("1984-03-10T22:12:43.000Z")
      },
      {
        name: { title: 'miss', first: 'sandra', last: 'lorenzo' },
        birthDate: ISODate("1975-03-23T17:01:45.000Z")
      },
      {
        name: { title: 'miss', first: 'shona', last: 'kemperman' },
        birthDate: ISODate("1948-04-23T03:40:22.000Z")
      },
      {
        name: { title: 'madame', first: 'andreia', last: 'arnaud' },
        birthDate: ISODate("1960-01-31T05:16:10.000Z")
      },
      {
        name: { title: 'mademoiselle', first: 'delia', last: 'durand' },
        birthDate: ISODate("1966-08-03T09:22:41.000Z")
      },
      {
        name: { title: 'miss', first: 'anne', last: 'ruiz' },
        birthDate: ISODate("1982-10-09T12:10:42.000Z")
      }
    ]
    Type "it" for more
```

</details>
    

```cpp
> db.persons.aggregate([
    { $project: { _id: 0, name: 1, birthDate: { $toDate: '$dob.date' } } },
    { $sort: { birthDate: 1 } },
    { $limit: 10 }
]).pretty()

**Output**
[
  {
    name: { title: 'mrs', first: 'victoria', last: 'hale' },
    birthDate: ISODate("1944-09-07T15:52:50.000Z")
  },
  {
    name: { title: 'mr', first: 'عباس', last: 'یاسمی' },
    birthDate: ISODate("1944-09-12T07:49:20.000Z")
  },
  {
    name: { title: 'miss', first: 'erundina', last: 'porto' },
    birthDate: ISODate("1944-09-13T14:58:41.000Z")
  },
  {
    name: { title: 'mr', first: 'پرهام', last: 'جعفری' },
    birthDate: ISODate("1944-09-16T16:03:28.000Z")
  },
  {
    name: { title: 'mr', first: 'eli', last: 'henry' },
    birthDate: ISODate("1944-09-17T15:04:13.000Z")
  },
  {
    name: { title: 'mr', first: 'kirk', last: 'brown' },
    birthDate: ISODate("1944-09-18T11:03:05.000Z")
  },
  {
    name: { title: 'miss', first: 'alexis', last: 'bélanger' },
    birthDate: ISODate("1944-10-02T22:56:32.000Z")
  },
  {
    name: { title: 'miss', first: 'gina', last: 'beck' },
    birthDate: ISODate("1944-10-04T07:41:31.000Z")
  },
  {
    name: { title: 'mr', first: 'sebastian', last: 'olsen' },
    birthDate: ISODate("1944-10-13T15:29:05.000Z")
  },
  {
    name: { title: 'miss', first: 'lucy', last: 'wilson' },
    birthDate: ISODate("1944-10-25T16:27:56.000Z")
  }
]
```

Concatenating first and last name and creating new field name. 

```bash
> db.persons.aggregate([
    { $project: { _id: 0, name: {$concat:['$name.first', ' ', '$name.last']}, birthDate: { $toDate: '$dob.date' } } },
    { $sort: { birthDate: 1 } },
    { $limit: 10 }
]).pretty()

**Output**
[
  {
    name: 'victoria hale',
    birthDate: ISODate("1944-09-07T15:52:50.000Z")
  },
  {
    name: 'عباس یاسمی',
    birthDate: ISODate("1944-09-12T07:49:20.000Z")
  },
  {
    name: 'erundina porto',
    birthDate: ISODate("1944-09-13T14:58:41.000Z")
  },
  {
    name: 'پرهام جعفری',
    birthDate: ISODate("1944-09-16T16:03:28.000Z")
  },
  { name: 'eli henry', birthDate: ISODate("1944-09-17T15:04:13.000Z") },
  {
    name: 'kirk brown',
    birthDate: ISODate("1944-09-18T11:03:05.000Z")
  },
  {
    name: 'alexis bélanger',
    birthDate: ISODate("1944-10-02T22:56:32.000Z")
  },
  { name: 'gina beck', birthDate: ISODate("1944-10-04T07:41:31.000Z") },
  {
    name: 'sebastian olsen',
    birthDate: ISODate("1944-10-13T15:29:05.000Z")
  },
  {
    name: 'lucy wilson',
    birthDate: ISODate("1944-10-25T16:27:56.000Z")
  }
]
```

Skip first 10

```cpp
> db.persons.aggregate([
    { $project: { _id: 0, name: { $concat: ['$name.first', ' ', '$name.last'] }, birthDate: { $toDate: '$dob.date' } } },
    { $sort: { birthDate: 1 } },
    { $skip: 10},
    { $limit: 10 }
]).pretty()

**Output**
[
  {
    name: 'eva murray',
    birthDate: ISODate("1944-10-29T02:05:56.000Z")
  },
  {
    name: 'elena chevalier',
    birthDate: ISODate("1944-10-31T02:56:40.000Z")
  },
  {
    name: 'gretchen schmidtke',
    birthDate: ISODate("1944-11-01T20:49:03.000Z")
  },
  {
    name: 'joseph thomas',
    birthDate: ISODate("1944-11-06T11:08:45.000Z")
  },
  { name: 'sarah lee', birthDate: ISODate("1944-11-07T07:53:47.000Z") },
  {
    name: 'conrad scheepbouwer',
    birthDate: ISODate("1944-11-08T02:15:17.000Z")
  },
  {
    name: 'martina charles',
    birthDate: ISODate("1944-11-08T07:38:49.000Z")
  },
  {
    name: 'olga blanco',
    birthDate: ISODate("1944-11-17T09:16:50.000Z")
  },
  {
    name: 'elisa morales',
    birthDate: ISODate("1944-11-22T22:51:47.000Z")
  },
  {
    name: 'rafael velasco',
    birthDate: ISODate("1944-11-27T07:12:20.000Z")
  }
]
```

But after `$skip` after `$limit` it does not work as first we take 10 persons in `{ $limit: 10 },` and we skip 10 persons `{ $skip: 10 }` so we do not have any person left to show.

```cpp
> db.persons.aggregate([
    { $project: { _id: 0, name: { $concat: ['$name.first', ' ', '$name.last'] }, birthDate: { $toDate: '$dob.date' } } },
    { $sort: { birthDate: 1 } },
    { $limit: 10 },
    { $skip: 10 }
]).pretty()

**Output**
```

So if we `$limit` 20 first and then `$skip` 10 we will get 10 output as by `$limit` 20 we are getting 20 persons documents and after `$skip` 10 we left with 10 documents.

```bash
> db.persons.aggregate([
    { $project: { _id: 0, name: { $concat: ['$name.first', ' ', '$name.last'] }, birthDate: { $toDate: '$dob.date' } } },
    { $sort: { birthDate: 1 } },
    { $limit: 20 },
    { $skip: 10 }
]).pretty()

**Output**
[
  {
    name: 'eva murray',
    birthDate: ISODate("1944-10-29T02:05:56.000Z")
  },
  {
    name: 'elena chevalier',
    birthDate: ISODate("1944-10-31T02:56:40.000Z")
  },
  {
    name: 'gretchen schmidtke',
    birthDate: ISODate("1944-11-01T20:49:03.000Z")
  },
  {
    name: 'joseph thomas',
    birthDate: ISODate("1944-11-06T11:08:45.000Z")
  },
  { name: 'sarah lee', birthDate: ISODate("1944-11-07T07:53:47.000Z") },
  {
    name: 'conrad scheepbouwer',
    birthDate: ISODate("1944-11-08T02:15:17.000Z")
  },
  {
    name: 'martina charles',
    birthDate: ISODate("1944-11-08T07:38:49.000Z")
  },
  {
    name: 'olga blanco',
    birthDate: ISODate("1944-11-17T09:16:50.000Z")
  },
  {
    name: 'elisa morales',
    birthDate: ISODate("1944-11-22T22:51:47.000Z")
  },
  {
    name: 'rafael velasco',
    birthDate: ISODate("1944-11-27T07:12:20.000Z")
  }
]
```

If add sort into last can see the different result. As we first `$skip` 10 documents and take 10 documents from collection and then `$sort` those 10 documents so this will not give us the wanted answer.

```cpp
> db.persons.aggregate([
    { $project: { _id: 0, name: { $concat: ['$name.first', ' ', '$name.last'] }, birthDate: { $toDate: '$dob.date' } } },
    { $skip: 10 },
		{ $limit: 10 },
    { $sort: { birthDate: 1 } },
]).pretty()

**Output**
[
  {
    name: 'shona kemperman',
    birthDate: ISODate("1948-04-23T03:40:22.000Z")
  },
  {
    name: 'mestan kaplangı',
    birthDate: ISODate("1951-12-17T20:03:33.000Z")
  },
  {
    name: 'madeleine till',
    birthDate: ISODate("1954-05-01T02:34:40.000Z")
  },
  {
    name: 'isolino viana',
    birthDate: ISODate("1959-03-22T14:53:41.000Z")
  },
  {
    name: 'andreia arnaud',
    birthDate: ISODate("1960-01-31T05:16:10.000Z")
  },
  {
    name: 'delia durand',
    birthDate: ISODate("1966-08-03T09:22:41.000Z")
  },
  {
    name: 'sandra lorenzo',
    birthDate: ISODate("1975-03-23T17:01:45.000Z")
  },
  { name: 'anne ruiz', birthDate: ISODate("1982-10-09T12:10:42.000Z") },
  {
    name: 'بنیامین سالاری',
    birthDate: ISODate("1984-03-10T22:12:43.000Z")
  },
  {
    name: 'katie welch',
    birthDate: ISODate("1990-10-14T05:02:12.000Z")
  }
]
```

Same also for `$match` . We want 10 oldest mans documents but as discussed previously it will not give right answer. As pipeline order is not correct.

```cpp
> db.persons.aggregate([
    { $match: { gender: 'male' } },
    { $project: { _id: 0, name: { $concat: ['$name.first', ' ', '$name.last'] }, birthDate: { $toDate: '$dob.date' } } },
    { $skip: 10 },
    { $limit: 10 },
    { $sort: { birthDate: 1 } }
]).pretty()
```

If `$match` added after the project without projection, we do not get any result. As we do not add gender in `$project` in first line so all stage below this do not get the gender field. 

```cpp
> db.persons.aggregate([
    { $project: { _id: 0, name: { $concat: ['$name.first', ' ', '$name.last'] }, birthDate: { $toDate: '$dob.date' } } },
    { $sort: { birthDate: 1 } },
    { $match: { gender: 'male' } },
    { $skip: 10 },
    { $limit: 10 },
]).pretty()

**Output**
```

If gender add into projection phase then will get results

```cpp
> db.persons.aggregate([
    { $project: { _id: 0, gender: 1, name: { $concat: ['$name.first', ' ', '$name.last'] }, birthDate: { $toDate: '$dob.date' } } },
    { $sort: { birthDate: 1 } },
    { $match: { gender: 'male' } },
    { $skip: 10 },
    { $limit: 10 },
]).pretty()

**Output**
[
  {
    gender: 'male',
    name: 'pierre boyer',
    birthDate: ISODate("1945-01-01T22:35:55.000Z")
  },
  {
    gender: 'male',
    name: 'emile noel',
    birthDate: ISODate("1945-01-10T03:05:21.000Z")
  },
  {
    gender: 'male',
    name: 'torgeir apeland',
    birthDate: ISODate("1945-01-13T17:04:33.000Z")
  },
  {
    gender: 'male',
    name: 'igor kvistad',
    birthDate: ISODate("1945-01-17T22:13:14.000Z")
  },
  {
    gender: 'male',
    name: 'mariusz gabler',
    birthDate: ISODate("1945-01-22T06:16:30.000Z")
  },
  {
    gender: 'male',
    name: 'lewis freeman',
    birthDate: ISODate("1945-01-28T20:15:28.000Z")
  },
  {
    gender: 'male',
    name: 'theodore moore',
    birthDate: ISODate("1945-02-10T03:34:29.000Z")
  },
  {
    gender: 'male',
    name: 'florian mercier',
    birthDate: ISODate("1945-02-22T04:18:31.000Z")
  },
  {
    gender: 'male',
    name: 'dursun schellekens',
    birthDate: ISODate("1945-02-22T07:28:00.000Z")
  },
  {
    gender: 'male',
    name: 'marcel rey',
    birthDate: ISODate("1945-02-28T02:18:01.000Z")
  }
]
```

But best is, use `$match` before `$project` . As we do not documents which gender is female so if we filtered before projection or any other pipeline then those pipeline have to run operation on fewer data as we already we reduced data by match.

```cpp
> db.persons.aggregate([
    { $match: { gender: 'male' } },
    { $project: { _id: 0, gender: 1, name: { $concat: ['$name.first', ' ', '$name.last'] }, birthDate: { $toDate: '$dob.date' } } },
    { $sort: { birthDate: 1 } },
    { $skip: 10 },
    { $limit: 10 },
]).pretty()
```