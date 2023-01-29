# GeoNear

# Working with the geoNear Stage

First create an index into the transformedPersons Collection

```cpp
> db.transformedPersons.createIndex({location: '2dsphere'})

**Output**
location_2dsphere
```

Create geo location aggregation pipeline stages

have to specify and that is the distance field, because `geoNear` will actually also give us back the distance that is calculated between our point and the document

`geoNear`, it has to be the first element in the pipeline because it needs to use that index and the **first pipeline** element is the **only element** with **direct access** to the **collection**, other pipeline stages just get the output of the previous pipeline stage, this is the only element with **direct access to the collection**. Also can add query. `maxDistance` in meter.

```cpp
> db.transformedPersons.aggregate([
    {
        $geoNear: {
            near: {
                type: 'Point',
                coordinates: [-18.4, -42.8]
            },
            maxDistance: 1000000,
            $limit: 10,
            query: { age: { $gt: 30 } },
            distanceField: 'distance'
        }
    }
]).pretty()

**Output**
[
  {
    _id: ObjectId("634bb5cf1f059ab964bafc0b"),
    location: { type: 'Point', coordinates: [ -18.5996, -42.6128 ] },
    email: 'elijah.lewis@example.com',
    birthdate: ISODate("1986-03-29T06:40:18.000Z"),
    age: 32,
    fullName: 'Elijah Lewis',
    distance: 26473.52536319881
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb0786"),
    location: { type: 'Point', coordinates: [ -16.8251, -41.9369 ] },
    email: 'delores.thompson@example.com',
    birthdate: ISODate("1984-04-11T07:34:45.000Z"),
    age: 34,
    fullName: 'Delores Thompson',
    distance: 161267.42830913173
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb0ac6"),
    location: { type: 'Point', coordinates: [ -19.5492, -44.8346 ] },
    email: 'kajus.moldskred@example.com',
    birthdate: ISODate("1978-09-12T00:25:23.000Z"),
    age: 39,
    fullName: 'Kajus Moldskred',
    distance: 244569.7553327739
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb07a5"),
    location: { type: 'Point', coordinates: [ -20.6738, -40.2524 ] },
    email: 'christian.møller@example.com',
    birthdate: ISODate("1967-07-18T04:08:25.000Z"),
    age: 51,
    fullName: 'Christian Møller',
    distance: 341047.8914183129
  },
  {
    _id: ObjectId("634bb5cf1f059ab964baff3a"),
    location: { type: 'Point', coordinates: [ -12.8517, -44.2241 ] },
    email: 'kübra.oraloğlu@example.com',
    birthdate: ISODate("1981-03-12T02:46:43.000Z"),
    age: 37,
    fullName: 'Kübra Oraloğlu',
    distance: 475031.1813780212
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb08e6"),
    location: { type: 'Point', coordinates: [ -24.1976, -42.2063 ] },
    email: 'gökhan.topaloğlu@example.com',
    birthdate: ISODate("1954-04-17T19:24:48.000Z"),
    age: 64,
    fullName: 'Gökhan Topaloğlu',
    distance: 480270.5071752364
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb07e6"),
    location: { type: 'Point', coordinates: [ -15.6018, -38.2254 ] },
    email: 'ayşe.eliçin@example.com',
    birthdate: ISODate("1959-02-26T17:16:38.000Z"),
    age: 59,
    fullName: 'Ayşe Eliçin',
    distance: 561521.5914865345
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb0380"),
    location: { type: 'Point', coordinates: [ -23.0621, -47.0624 ] },
    email: 'chloe.ennis@example.com',
    birthdate: ISODate("1956-07-16T00:28:06.000Z"),
    age: 62,
    fullName: 'Chloe Ennis',
    distance: 599870.3100224738
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb0ea2"),
    location: { type: 'Point', coordinates: [ -26.0729, -42.8626 ] },
    email: 'kuzey.berberoğlu@example.com',
    birthdate: ISODate("1984-08-27T19:20:20.000Z"),
    age: 34,
    fullName: 'Kuzey Berberoğlu',
    distance: 626211.846542541
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb06ae"),
    location: { type: 'Point', coordinates: [ -10.6398, -41.7477 ] },
    email: 'nellie.chapman@example.com',
    birthdate: ISODate("1982-12-27T07:32:35.000Z"),
    age: 35,
    fullName: 'Nellie Chapman',
    distance: 649597.0679432369
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb0870"),
    location: { type: 'Point', coordinates: [ -14.6511, -36.9876 ] },
    email: 'natão.nascimento@example.com',
    birthdate: ISODate("1955-10-23T04:21:38.000Z"),
    age: 62,
    fullName: 'Natão Nascimento',
    distance: 721718.9326148127
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb09f4"),
    location: { type: 'Point', coordinates: [ -27.2914, -43.7096 ] },
    email: 'silje.christensen@example.com',
    birthdate: ISODate("1946-09-18T13:46:00.000Z"),
    age: 71,
    fullName: 'Silje Christensen',
    distance: 727582.9314351011
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb0621"),
    location: { type: 'Point', coordinates: [ -15.4943, -36.3461 ] },
    email: 'پرنیا.سهيليراد@example.com',
    birthdate: ISODate("1947-03-06T12:26:25.000Z"),
    age: 71,
    fullName: 'پرنیا سهيلي راد',
    distance: 760338.5829500874
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb0cc0"),
    location: { type: 'Point', coordinates: [ -24.7805, -48.0386 ] },
    email: 'naomi.castillo@example.com',
    birthdate: ISODate("1975-04-29T06:55:56.000Z"),
    age: 43,
    fullName: 'Naomi Castillo',
    distance: 766668.6621958854
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb07c8"),
    location: { type: 'Point', coordinates: [ -10.0214, -39.3665 ] },
    email: 'lara.dehooge@example.com',
    birthdate: ISODate("1979-03-19T21:16:26.000Z"),
    age: 39,
    fullName: 'Lara De hooge',
    distance: 799665.4886170039
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb0757"),
    location: { type: 'Point', coordinates: [ -17.255, -50.3131 ] },
    email: 'lumi.wirta@example.com',
    birthdate: ISODate("1962-12-06T13:22:54.000Z"),
    age: 55,
    fullName: 'Lumi Wirta',
    distance: 840901.0420234643
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bafcc5"),
    location: { type: 'Point', coordinates: [ -28.6565, -44.6573 ] },
    email: 'رضا.سالاری@example.com',
    birthdate: ISODate("1972-02-27T22:08:54.000Z"),
    age: 46,
    fullName: 'رضا سالاری',
    distance: 849915.7332153537
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb08ae"),
    location: { type: 'Point', coordinates: [ -7.5113, -43.1898 ] },
    email: 'zoe.hall@example.com',
    birthdate: ISODate("1945-06-30T22:42:58.000Z"),
    age: 73,
    fullName: 'Zoe Hall',
    distance: 886994.5551556123
  },
  {
    _id: ObjectId("634bb5cf1f059ab964bb0c2e"),
    location: { type: 'Point', coordinates: [ -16.5148, -34.6911 ] },
    email: 'justin.moulin@example.com',
    birthdate: ISODate("1961-06-01T15:51:52.000Z"),
    age: 57,
    fullName: 'Justin Moulin',
    distance: 917320.3765606781
  }
]
```

Can also add multiple pipeline stages

```bash
db.transformedPersons.aggregate([
    {
        $geoNear: {
            near: {
                type: 'Point',
                coordinates: [-18.4, -42.8]
            },
            maxDistance: 1000000,
            $limit: 10,
            query: { age: { $gt: 30 } },
            distanceField: 'distance'
        }
    },
    { $project: { _id: 0, email: 0, birthdate: 0 } },
    { $sort: { distanceField: 1 } },
]).pretty()

**Output**
[
  {
    location: { type: 'Point', coordinates: [ -18.5996, -42.6128 ] },
    age: 32,
    fullName: 'Elijah Lewis',
    distance: 26473.52536319881
  },
  {
    location: { type: 'Point', coordinates: [ -16.8251, -41.9369 ] },
    age: 34,
    fullName: 'Delores Thompson',
    distance: 161267.42830913173
  },
  {
    location: { type: 'Point', coordinates: [ -19.5492, -44.8346 ] },
    age: 39,
    fullName: 'Kajus Moldskred',
    distance: 244569.7553327739
  },
  {
    location: { type: 'Point', coordinates: [ -20.6738, -40.2524 ] },
    age: 51,
    fullName: 'Christian Møller',
    distance: 341047.8914183129
  },
  {
    location: { type: 'Point', coordinates: [ -12.8517, -44.2241 ] },
    age: 37,
    fullName: 'Kübra Oraloğlu',
    distance: 475031.1813780212
  },
  {
    location: { type: 'Point', coordinates: [ -24.1976, -42.2063 ] },
    age: 64,
    fullName: 'Gökhan Topaloğlu',
    distance: 480270.5071752364
  },
  {
    location: { type: 'Point', coordinates: [ -15.6018, -38.2254 ] },
    age: 59,
    fullName: 'Ayşe Eliçin',
    distance: 561521.5914865345
  },
  {
    location: { type: 'Point', coordinates: [ -23.0621, -47.0624 ] },
    age: 62,
    fullName: 'Chloe Ennis',
    distance: 599870.3100224738
  },
  {
    location: { type: 'Point', coordinates: [ -26.0729, -42.8626 ] },
    age: 34,
    fullName: 'Kuzey Berberoğlu',
    distance: 626211.846542541
  },
  {
    location: { type: 'Point', coordinates: [ -10.6398, -41.7477 ] },
    age: 35,
    fullName: 'Nellie Chapman',
    distance: 649597.0679432369
  },
  {
    location: { type: 'Point', coordinates: [ -14.6511, -36.9876 ] },
    age: 62,
    fullName: 'Natão Nascimento',
    distance: 721718.9326148127
  },
  {
    location: { type: 'Point', coordinates: [ -27.2914, -43.7096 ] },
    age: 71,
    fullName: 'Silje Christensen',
    distance: 727582.9314351011
  },
  {
    location: { type: 'Point', coordinates: [ -15.4943, -36.3461 ] },
    age: 71,
    fullName: 'پرنیا سهيلي راد',
    distance: 760338.5829500874
  },
  {
    location: { type: 'Point', coordinates: [ -24.7805, -48.0386 ] },
    age: 43,
    fullName: 'Naomi Castillo',
    distance: 766668.6621958854
  },
  {
    location: { type: 'Point', coordinates: [ -10.0214, -39.3665 ] },
    age: 39,
    fullName: 'Lara De hooge',
    distance: 799665.4886170039
  },
  {
    location: { type: 'Point', coordinates: [ -17.255, -50.3131 ] },
    age: 55,
    fullName: 'Lumi Wirta',
    distance: 840901.0420234643
  },
  {
    location: { type: 'Point', coordinates: [ -28.6565, -44.6573 ] },
    age: 46,
    fullName: 'رضا سالاری',
    distance: 849915.7332153537
  },
  {
    location: { type: 'Point', coordinates: [ -7.5113, -43.1898 ] },
    age: 73,
    fullName: 'Zoe Hall',
    distance: 886994.5551556123
  },
  {
    location: { type: 'Point', coordinates: [ -16.5148, -34.6911 ] },
    age: 57,
    fullName: 'Justin Moulin',
    distance: 917320.3765606781
  }
]
```