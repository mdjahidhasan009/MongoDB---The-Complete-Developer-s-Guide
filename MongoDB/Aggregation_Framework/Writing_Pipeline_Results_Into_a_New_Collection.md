# Writing Pipeline Results Into a New Collection

By getting the output we can store into the another Collection with `$out`, can do work with the out stage.

```bash
db.persons.aggregate([
  {
    $project: {
      _id: 0,
      name: 1,
      email: 1,
      birthdate: { $toDate: '$dob.date' },
      age: "$dob.age",
      location: {
        type: 'Point',
        coordinates: [
          {
            $convert: {
              input: '$location.coordinates.longitude',
              to: 'double',
              onError: 0.0,
              onNull: 0.0
            }
          },
          {
            $convert: {
              input: '$location.coordinates.latitude',
              to: 'double',
              onError: 0.0,
              onNull: 0.0
            }
          }
        ]
      }
    }
  },
  {
    $project: {
      gender: 1,
      email: 1,
      location: 1,
      birthdate: 1,
      age: 1,
      fullName: {
        $concat: [
          { $toUpper: { $substrCP: ['$name.first', 0, 1] } },
          {
            $substrCP: [
              '$name.first',
              1,
              { $subtract: [{ $strLenCP: '$name.first' }, 1] }
            ]
          },
          ' ',
          { $toUpper: { $substrCP: ['$name.last', 0, 1] } },
          {
            $substrCP: [
              '$name.last',
              1,
              { $subtract: [{ $strLenCP: '$name.last' }, 1] }
            ]
          }
        ]
      }
    }
  },
  { $out: "transformedPersons" }
]).pretty();

**Output**
```

Now check all collections. We get new collection transformedPersons which we defined in previous command.

```bash
show collections

**Output**
friends
persons
transformedPersons
```

Checking the data in transformedPersons collection

```bash
db.transformedPersons.find().pretty()

**Output**
[
  {
    _id: ObjectId("634bb5cf1f059ab964bafc04"),
    location: { type: 'Point', coordinates: [ -154.6037, -29.6721 ] },
    email: 'carl.jacobs@example.com',
    birthdate: ISODate("1984-09-30T01:20:26.000Z"),
    age: 33,
    fullName: 'Carl Jacobs'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc05"),
    location: { type: 'Point', coordinates: [ 168.9462, -22.5329 ] },
    email: 'harvey.chambers@example.com',
    birthdate: ISODate("1988-05-27T00:14:03.000Z"),
    age: 30,
    fullName: 'Harvey Chambers'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc06"),
    location: { type: 'Point', coordinates: [ -54.1364, -86.1268 ] },
    email: 'gideon.vandrongelen@example.com',
    birthdate: ISODate("1971-03-28T04:47:21.000Z"),
    age: 47,
    fullName: 'Gideon Van drongelen'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc07"),
    location: { type: 'Point', coordinates: [ -70.2264, 76.4507 ] },
    email: 'zachary.lo@example.com',
    birthdate: ISODate("1988-10-17T03:45:04.000Z"),
    age: 29,
    fullName: 'Zachary Lo'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc08"),
    location: { type: 'Point', coordinates: [ 111.3806, -31.6359 ] },
    email: 'maeva.wilson@example.com',
    birthdate: ISODate("1962-08-11T20:51:07.000Z"),
    age: 56,
    fullName: 'Maeva Wilson'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc09"),
    location: { type: 'Point', coordinates: [ 34.1689, 4.6625 ] },
    email: 'پریا.پارسا@example.com',
    birthdate: ISODate("1962-01-10T05:26:30.000Z"),
    age: 56,
    fullName: 'پریا پارسا'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc0a"),
    location: { type: 'Point', coordinates: [ -67.5738, -52.8348 ] },
    email: 'olav.oehme@example.com',
    birthdate: ISODate("1960-11-28T23:07:18.000Z"),
    age: 57,
    fullName: 'Olav Oehme'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc0b"),
    location: { type: 'Point', coordinates: [ -18.5996, -42.6128 ] },
    email: 'elijah.lewis@example.com',
    birthdate: ISODate("1986-03-29T06:40:18.000Z"),
    age: 32,
    fullName: 'Elijah Lewis'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc0c"),
    location: { type: 'Point', coordinates: [ -31.0208, -29.8113 ] },
    email: 'victor.pedersen@example.com',
    birthdate: ISODate("1959-02-19T23:56:23.000Z"),
    age: 59,
    fullName: 'Victor Pedersen'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc0d"),
    location: { type: 'Point', coordinates: [ 148.0944, 35.5726 ] },
    email: 'louise.graham@example.com',
    birthdate: ISODate("1971-01-21T20:36:16.000Z"),
    age: 47,
    fullName: 'Louise Graham'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc0e"),
    location: { type: 'Point', coordinates: [ -172.3753, 83.3998 ] },
    email: 'madeleine.till@example.com',
    birthdate: ISODate("1954-05-01T02:34:40.000Z"),
    age: 64,
    fullName: 'Madeleine Till'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc0f"),
    location: { type: 'Point', coordinates: [ 101.5995, 78.8545 ] },
    email: 'isolino.viana@example.com',
    birthdate: ISODate("1959-03-22T14:53:41.000Z"),
    age: 59,
    fullName: 'Isolino Viana'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc10"),
    location: { type: 'Point', coordinates: [ 43.9085, 25.1614 ] },
    email: 'mestan.kaplangı@example.com',
    birthdate: ISODate("1951-12-17T20:03:33.000Z"),
    age: 66,
    fullName: 'Mestan Kaplangı'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc11"),
    location: { type: 'Point', coordinates: [ 135.9359, 71.9851 ] },
    email: 'katie.welch@example.com',
    birthdate: ISODate("1990-10-14T05:02:12.000Z"),
    age: 27,
    fullName: 'Katie Welch'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc12"),
    location: { type: 'Point', coordinates: [ -90.9499, 21.3388 ] },
    email: 'بنیامین.سالاری@example.com',
    birthdate: ISODate("1984-03-10T22:12:43.000Z"),
    age: 34,
    fullName: 'بنیامین سالاری'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc13"),
    location: { type: 'Point', coordinates: [ -83.3326, -88.6846 ] },
    email: 'sandra.lorenzo@example.com',
    birthdate: ISODate("1975-03-23T17:01:45.000Z"),
    age: 43,
    fullName: 'Sandra Lorenzo'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc14"),
    location: { type: 'Point', coordinates: [ -8.557, -14.4912 ] },
    email: 'shona.kemperman@example.com',
    birthdate: ISODate("1948-04-23T03:40:22.000Z"),
    age: 70,
    fullName: 'Shona Kemperman'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc15"),
    location: { type: 'Point', coordinates: [ 59.5703, -67.6434 ] },
    email: 'andreia.arnaud@example.com',
    birthdate: ISODate("1960-01-31T05:16:10.000Z"),
    age: 58,
    fullName: 'Andreia Arnaud'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc16"),
    location: { type: 'Point', coordinates: [ -90.4049, -65.0877 ] },
    email: 'delia.durand@example.com',
    birthdate: ISODate("1966-08-03T09:22:41.000Z"),
    age: 52,
    fullName: 'Delia Durand'
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafc17"),
    location: { type: 'Point', coordinates: [ 78.0207, -84.1572 ] },
    email: 'anne.ruiz@example.com',
    birthdate: ISODate("1982-10-09T12:10:42.000Z"),
    age: 35,
    fullName: 'Anne Ruiz'
  }
]
Type "it" for more
```