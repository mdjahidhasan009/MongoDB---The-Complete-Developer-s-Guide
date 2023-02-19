# Turning the Location Into a geoJSON Object

Using multiple aggregate functions to get the next value from the previous.

Name, email, and location will be passed to the second `$project`. And second, the `$project` will print the email, and location(get formatted from the first `$project`) and fullName will be formatted in this(second) `$project`.

```bash
> db.persons.aggregate([
    {
        $project: {
            _id: 0,
            name: 1,
            email: 1,
            location: {
                type: 'Point',
                coordinates: [
                    '$location.coordinates.longitude',
                    '$location.coordinates.latitude',
                ]
            }
        }
    },
    {
        $project: {
            email: 1,
            location: 1,
            gender: 1,
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
										 },
                ]
            }
        }
    }
]).pretty()

**Output**
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"-31.0208",
			"-29.8113"
		]
	},
	"email" : "victor.pedersen@example.com",
	"fullName" : "Victor Pedersen"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"-54.1364",
			"-86.1268"
		]
	},
	"email" : "gideon.vandrongelen@example.com",
	"fullName" : "Gideon Van drongelen"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"168.9462",
			"-22.5329"
		]
	},
	"email" : "harvey.chambers@example.com",
	"fullName" : "Harvey Chambers"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"34.1689",
			"4.6625"
		]
	},
	"email" : "پریا.پارسا@example.com",
	"fullName" : "پریا پارسا"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"111.3806",
			"-31.6359"
		]
	},
	"email" : "maeva.wilson@example.com",
	"fullName" : "Maeva Wilson"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"-18.5996",
			"-42.6128"
		]
	},
	"email" : "elijah.lewis@example.com",
	"fullName" : "Elijah Lewis"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"-67.5738",
			"-52.8348"
		]
	},
	"email" : "olav.oehme@example.com",
	"fullName" : "Olav Oehme"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"-172.3753",
			"83.3998"
		]
	},
	"email" : "madeleine.till@example.com",
	"fullName" : "Madeleine Till"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"-154.6037",
			"-29.6721"
		]
	},
	"email" : "carl.jacobs@example.com",
	"fullName" : "Carl Jacobs"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"101.5995",
			"78.8545"
		]
	},
	"email" : "isolino.viana@example.com",
	"fullName" : "Isolino Viana"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"148.0944",
			"35.5726"
		]
	},
	"email" : "louise.graham@example.com",
	"fullName" : "Louise Graham"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"43.9085",
			"25.1614"
		]
	},
	"email" : "mestan.kaplangı@example.com",
	"fullName" : "Mestan Kaplangı"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"135.9359",
			"71.9851"
		]
	},
	"email" : "katie.welch@example.com",
	"fullName" : "Katie Welch"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"-83.3326",
			"-88.6846"
		]
	},
	"email" : "sandra.lorenzo@example.com",
	"fullName" : "Sandra Lorenzo"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"-90.9499",
			"21.3388"
		]
	},
	"email" : "بنیامین.سالاری@example.com",
	"fullName" : "بنیامین سالاری"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"59.5703",
			"-67.6434"
		]
	},
	"email" : "andreia.arnaud@example.com",
	"fullName" : "Andreia Arnaud"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"-8.5570",
			"-14.4912"
		]
	},
	"email" : "shona.kemperman@example.com",
	"fullName" : "Shona Kemperman"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"-70.2264",
			"76.4507"
		]
	},
	"email" : "zachary.lo@example.com",
	"fullName" : "Zachary Lo"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"78.0207",
			"-84.1572"
		]
	},
	"email" : "anne.ruiz@example.com",
	"fullName" : "Anne Ruiz"
}
{
	"location" : {
		"type" : "Point",
		"coordinates" : [
			"130.0105",
			"88.1818"
		]
	},
	"email" : "gonca.alnıaçık@example.com",
	"fullName" : "Gonca Alnıaçık"
}
Type "it" for more
```

<details>
<summary>Converting coordinate into geoJson type(Point)</summary>

  ```schema
    > db.persons.aggregate([
        {
            $project: {
                _id: 0,
                name: 1,
                email: 1,
                location: {
                    type: 'Point',
                    coordinates: [
                       '$location.coordinates.longitude',
                       '$location.coordinates.latitude',
                    ]
                }
            }
        },
        {
            $project: {
                email: 1,
                location: 1,
                gender: 1,
                fullName: {
                    $concat: [
                        {
                            $toUpper: {
                                $substrCP: ['$name.first', 0, 1]
                            }
                        }, {
                            $substrCP: [
                                '$name.first', 1, {
                                    $subtract: [
                                        { $strLenCP: '$name.first' }, 1
                                    ]
                                }]
                        },
                        ' ',
                        {
                            $toUpper: {
                                $substrCP: ['$name.last', 0, 1]
                            }
                        },
                        {
                            $substrCP: [
                                '$name.last', 1, {
                                    $subtract: [
                                        { $strLenCP: '$name.last' }, 1
                                    ]
                                }]
                        }
                    ]
                }
            }
        }
    ]).pretty()
    
    **Output**
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"-31.0208",
    			"-29.8113"
    		]
    	},
    	"email" : "victor.pedersen@example.com",
    	"fullName" : "Victor Pedersen"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"-54.1364",
    			"-86.1268"
    		]
    	},
    	"email" : "gideon.vandrongelen@example.com",
    	"fullName" : "Gideon Van drongelen"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"168.9462",
    			"-22.5329"
    		]
    	},
    	"email" : "harvey.chambers@example.com",
    	"fullName" : "Harvey Chambers"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"34.1689",
    			"4.6625"
    		]
    	},
    	"email" : "پریا.پارسا@example.com",
    	"fullName" : "پریا پارسا"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"111.3806",
    			"-31.6359"
    		]
    	},
    	"email" : "maeva.wilson@example.com",
    	"fullName" : "Maeva Wilson"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"-18.5996",
    			"-42.6128"
    		]
    	},
    	"email" : "elijah.lewis@example.com",
    	"fullName" : "Elijah Lewis"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"-67.5738",
    			"-52.8348"
    		]
    	},
    	"email" : "olav.oehme@example.com",
    	"fullName" : "Olav Oehme"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"-172.3753",
    			"83.3998"
    		]
    	},
    	"email" : "madeleine.till@example.com",
    	"fullName" : "Madeleine Till"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"-154.6037",
    			"-29.6721"
    		]
    	},
    	"email" : "carl.jacobs@example.com",
    	"fullName" : "Carl Jacobs"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"101.5995",
    			"78.8545"
    		]
    	},
    	"email" : "isolino.viana@example.com",
    	"fullName" : "Isolino Viana"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"148.0944",
    			"35.5726"
    		]
    	},
    	"email" : "louise.graham@example.com",
    	"fullName" : "Louise Graham"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"43.9085",
    			"25.1614"
    		]
    	},
    	"email" : "mestan.kaplangı@example.com",
    	"fullName" : "Mestan Kaplangı"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"135.9359",
    			"71.9851"
    		]
    	},
    	"email" : "katie.welch@example.com",
    	"fullName" : "Katie Welch"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"-83.3326",
    			"-88.6846"
    		]
    	},
    	"email" : "sandra.lorenzo@example.com",
    	"fullName" : "Sandra Lorenzo"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"-90.9499",
    			"21.3388"
    		]
    	},
    	"email" : "بنیامین.سالاری@example.com",
    	"fullName" : "بنیامین سالاری"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"59.5703",
    			"-67.6434"
    		]
    	},
    	"email" : "andreia.arnaud@example.com",
    	"fullName" : "Andreia Arnaud"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"-8.5570",
    			"-14.4912"
    		]
    	},
    	"email" : "shona.kemperman@example.com",
    	"fullName" : "Shona Kemperman"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"-70.2264",
    			"76.4507"
    		]
    	},
    	"email" : "zachary.lo@example.com",
    	"fullName" : "Zachary Lo"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"78.0207",
    			"-84.1572"
    		]
    	},
    	"email" : "anne.ruiz@example.com",
    	"fullName" : "Anne Ruiz"
    }
    {
    	"location" : {
    		"type" : "Point",
    		"coordinates" : [
    			"130.0105",
    			"88.1818"
    		]
    	},
    	"email" : "gonca.alnıaçık@example.com",
    	"fullName" : "Gonca Alnıaçık"
    }
    Type "it" for more
  ``` 

</details>
    

We are getting GeoJson coordinates as a string, so have to convert them into numbers.

```cpp
> db.persons.aggregate([
    {
        $project: {
            _id: 0,
            name: 1,
            email: 1,
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
            email: 1,
            location: 1,
            gender: 1,
            fullName: {
                $concat: [
                    {
                        $toUpper: {
                            $substrCP: ['$name.first', 0, 1]
                        }
                    }, {
                        $substrCP: [
                            '$name.first', 1, {
                                $subtract: [
                                    { $strLenCP: '$name.first' }, 1
                                ]
                            }]
                    },
                    ' ',
                    {
                        $toUpper: {
                            $substrCP: ['$name.last', 0, 1]
                        }
                    },
                    {
                        $substrCP: [
                            '$name.last', 1, {
                                $subtract: [
                                    { $strLenCP: '$name.last' }, 1
                                ]
                            }]
                    }
                ]
            }
        }
    }
]).pretty()

**Output**
[
  {
    location: { type: 'Point', coordinates: [ -154.6037, -29.6721 ] },
    email: 'carl.jacobs@example.com',
    fullName: 'Carl Jacobs'
  },
  {
    location: { type: 'Point', coordinates: [ 168.9462, -22.5329 ] },
    email: 'harvey.chambers@example.com',
    fullName: 'Harvey Chambers'
  },
  {
    location: { type: 'Point', coordinates: [ -54.1364, -86.1268 ] },
    email: 'gideon.vandrongelen@example.com',
    fullName: 'Gideon Van drongelen'
  },
  {
    location: { type: 'Point', coordinates: [ -70.2264, 76.4507 ] },
    email: 'zachary.lo@example.com',
    fullName: 'Zachary Lo'
  },
  {
    location: { type: 'Point', coordinates: [ 111.3806, -31.6359 ] },
    email: 'maeva.wilson@example.com',
    fullName: 'Maeva Wilson'
  },
  {
    location: { type: 'Point', coordinates: [ 34.1689, 4.6625 ] },
    email: 'پریا.پارسا@example.com',
    fullName: 'پریا پارسا'
  },
  {
    location: { type: 'Point', coordinates: [ -67.5738, -52.8348 ] },
    email: 'olav.oehme@example.com',
    fullName: 'Olav Oehme'
  },
  {
    location: { type: 'Point', coordinates: [ -18.5996, -42.6128 ] },
    email: 'elijah.lewis@example.com',
    fullName: 'Elijah Lewis'
  },
  {
    location: { type: 'Point', coordinates: [ -31.0208, -29.8113 ] },
    email: 'victor.pedersen@example.com',
    fullName: 'Victor Pedersen'
  },
  {
    location: { type: 'Point', coordinates: [ 148.0944, 35.5726 ] },
    email: 'louise.graham@example.com',
    fullName: 'Louise Graham'
  },
  {
    location: { type: 'Point', coordinates: [ -172.3753, 83.3998 ] },
    email: 'madeleine.till@example.com',
    fullName: 'Madeleine Till'
  },
  {
    location: { type: 'Point', coordinates: [ 101.5995, 78.8545 ] },
    email: 'isolino.viana@example.com',
    fullName: 'Isolino Viana'
  },
  {
    location: { type: 'Point', coordinates: [ 43.9085, 25.1614 ] },
    email: 'mestan.kaplangı@example.com',
    fullName: 'Mestan Kaplangı'
  },
  {
    location: { type: 'Point', coordinates: [ 135.9359, 71.9851 ] },
    email: 'katie.welch@example.com',
    fullName: 'Katie Welch'
  },
  {
    location: { type: 'Point', coordinates: [ -90.9499, 21.3388 ] },
    email: 'بنیامین.سالاری@example.com',
    fullName: 'بنیامین سالاری'
  },
  {
    location: { type: 'Point', coordinates: [ -83.3326, -88.6846 ] },
    email: 'sandra.lorenzo@example.com',
    fullName: 'Sandra Lorenzo'
  },
  {
    location: { type: 'Point', coordinates: [ -8.557, -14.4912 ] },
    email: 'shona.kemperman@example.com',
    fullName: 'Shona Kemperman'
  },
  {
    location: { type: 'Point', coordinates: [ 59.5703, -67.6434 ] },
    email: 'andreia.arnaud@example.com',
    fullName: 'Andreia Arnaud'
  },
  {
    location: { type: 'Point', coordinates: [ -90.4049, -65.0877 ] },
    email: 'delia.durand@example.com',
    fullName: 'Delia Durand'
  },
  {
    location: { type: 'Point', coordinates: [ 78.0207, -84.1572 ] },
    email: 'anne.ruiz@example.com',
    fullName: 'Anne Ruiz'
  }
]
Type "it" for more
```

Transforming the `BirthDate` into data format

```cpp
   > db.persons.aggregate([
    {
        $project: {
            _id: 0,
            name: 1,
            email: 1,
            birthdate: {
                $convert: {
                    input: '$dob.date',
                    to: 'date',
                    onError: 0.0,
                    onNull: 0.0
                }
            },
            age: '$dob.age',
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
            email: 1,
            location: 1,
            gender: 1,
            birthdate: 1,
            age: 1,
            fullName: {
                $concat: [
                    {
                        $toUpper: {
                            $substrCP: ['$name.first', 0, 1]
                        }
                    }, {
                        $substrCP: [
                            '$name.first', 1, {
                                $subtract: [
                                    { $strLenCP: '$name.first' }, 1
                                ]
                            }]
                    },
                    ' ',
                    {
                        $toUpper: {
                            $substrCP: ['$name.last', 0, 1]
                        }
                    },
                    {
                        $substrCP: [
                            '$name.last', 1, {
                                $subtract: [
                                    { $strLenCP: '$name.last' }, 1
                                ]
                            }]
                    }
                ]
            }
        }
    }
]).pretty()

**Output**
[
  {
    location: { type: 'Point', coordinates: [ -154.6037, -29.6721 ] },
    email: 'carl.jacobs@example.com',
    birthdate: ISODate("1984-09-30T01:20:26.000Z"),
    age: 33,
    fullName: 'Carl Jacobs'
  },
  {
    location: { type: 'Point', coordinates: [ 168.9462, -22.5329 ] },
    email: 'harvey.chambers@example.com',
    birthdate: ISODate("1988-05-27T00:14:03.000Z"),
    age: 30,
    fullName: 'Harvey Chambers'
  },
  {
    location: { type: 'Point', coordinates: [ -54.1364, -86.1268 ] },
    email: 'gideon.vandrongelen@example.com',
    birthdate: ISODate("1971-03-28T04:47:21.000Z"),
    age: 47,
    fullName: 'Gideon Van drongelen'
  },
  {
    location: { type: 'Point', coordinates: [ -70.2264, 76.4507 ] },
    email: 'zachary.lo@example.com',
    birthdate: ISODate("1988-10-17T03:45:04.000Z"),
    age: 29,
    fullName: 'Zachary Lo'
  },
  {
    location: { type: 'Point', coordinates: [ 111.3806, -31.6359 ] },
    email: 'maeva.wilson@example.com',
    birthdate: ISODate("1962-08-11T20:51:07.000Z"),
    age: 56,
    fullName: 'Maeva Wilson'
  },
  {
    location: { type: 'Point', coordinates: [ 34.1689, 4.6625 ] },
    email: 'پریا.پارسا@example.com',
    birthdate: ISODate("1962-01-10T05:26:30.000Z"),
    age: 56,
    fullName: 'پریا پارسا'
  },
  {
    location: { type: 'Point', coordinates: [ -67.5738, -52.8348 ] },
    email: 'olav.oehme@example.com',
    birthdate: ISODate("1960-11-28T23:07:18.000Z"),
    age: 57,
    fullName: 'Olav Oehme'
  },
  {
    location: { type: 'Point', coordinates: [ -18.5996, -42.6128 ] },
    email: 'elijah.lewis@example.com',
    birthdate: ISODate("1986-03-29T06:40:18.000Z"),
    age: 32,
    fullName: 'Elijah Lewis'
  },
  {
    location: { type: 'Point', coordinates: [ -31.0208, -29.8113 ] },
    email: 'victor.pedersen@example.com',
    birthdate: ISODate("1959-02-19T23:56:23.000Z"),
    age: 59,
    fullName: 'Victor Pedersen'
  },
  {
    location: { type: 'Point', coordinates: [ 148.0944, 35.5726 ] },
    email: 'louise.graham@example.com',
    birthdate: ISODate("1971-01-21T20:36:16.000Z"),
    age: 47,
    fullName: 'Louise Graham'
  },
  {
    location: { type: 'Point', coordinates: [ -172.3753, 83.3998 ] },
    email: 'madeleine.till@example.com',
    birthdate: ISODate("1954-05-01T02:34:40.000Z"),
    age: 64,
    fullName: 'Madeleine Till'
  },
  {
    location: { type: 'Point', coordinates: [ 101.5995, 78.8545 ] },
    email: 'isolino.viana@example.com',
    birthdate: ISODate("1959-03-22T14:53:41.000Z"),
    age: 59,
    fullName: 'Isolino Viana'
  },
  {
    location: { type: 'Point', coordinates: [ 43.9085, 25.1614 ] },
    email: 'mestan.kaplangı@example.com',
    birthdate: ISODate("1951-12-17T20:03:33.000Z"),
    age: 66,
    fullName: 'Mestan Kaplangı'
  },
  {
    location: { type: 'Point', coordinates: [ 135.9359, 71.9851 ] },
    email: 'katie.welch@example.com',
    birthdate: ISODate("1990-10-14T05:02:12.000Z"),
    age: 27,
    fullName: 'Katie Welch'
  },
  {
    location: { type: 'Point', coordinates: [ -90.9499, 21.3388 ] },
    email: 'بنیامین.سالاری@example.com',
    birthdate: ISODate("1984-03-10T22:12:43.000Z"),
    age: 34,
    fullName: 'بنیامین سالاری'
  },
  {
    location: { type: 'Point', coordinates: [ -83.3326, -88.6846 ] },
    email: 'sandra.lorenzo@example.com',
    birthdate: ISODate("1975-03-23T17:01:45.000Z"),
    age: 43,
    fullName: 'Sandra Lorenzo'
  },
  {
    location: { type: 'Point', coordinates: [ -8.557, -14.4912 ] },
    email: 'shona.kemperman@example.com',
    birthdate: ISODate("1948-04-23T03:40:22.000Z"),
    age: 70,
    fullName: 'Shona Kemperman'
  },
  {
    location: { type: 'Point', coordinates: [ 59.5703, -67.6434 ] },
    email: 'andreia.arnaud@example.com',
    birthdate: ISODate("1960-01-31T05:16:10.000Z"),
    age: 58,
    fullName: 'Andreia Arnaud'
  },
  {
    location: { type: 'Point', coordinates: [ -90.4049, -65.0877 ] },
    email: 'delia.durand@example.com',
    birthdate: ISODate("1966-08-03T09:22:41.000Z"),
    age: 52,
    fullName: 'Delia Durand'
  },
  {
    location: { type: 'Point', coordinates: [ 78.0207, -84.1572 ] },
    email: 'anne.ruiz@example.com',
    birthdate: ISODate("1982-10-09T12:10:42.000Z"),
    age: 35,
    fullName: 'Anne Ruiz'
  }
]
Type "it" for more
```

Using Shortcuts for Transformations
But Shortcuts Transformations are always not good. the cause can not handle errors in this process as do not provide `onError`, `onNull`

```cpp
> db.persons.aggregate([
    {
        $project: {
            _id: 0,
            name: 1,
            email: 1,
            birthdate: { $toDate: '$dob.date'},
            age: '$dob.age',
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
            email: 1,
            location: 1,
            gender: 1,
            birthdate: 1,
            age: 1,
            fullName: {
                $concat: [
                    {
                        $toUpper: {
                            $substrCP: ['$name.first', 0, 1]
                        }
                    }, {
                        $substrCP: [
                            '$name.first', 1, {
                                $subtract: [
                                    { $strLenCP: '$name.first' }, 1
                                ]
                            }]
                    },
                    ' ',
                    {
                        $toUpper: {
                            $substrCP: ['$name.last', 0, 1]
                        }
                    },
                    {
                        $substrCP: [
                            '$name.last', 1, {
                                $subtract: [
                                    { $strLenCP: '$name.last' }, 1
                                ]
                            }]
                    }
                ]
            }
        }
    }
]).pretty()

**Output**
[
  {
    location: { type: 'Point', coordinates: [ -154.6037, -29.6721 ] },
    email: 'carl.jacobs@example.com',
    birthdate: ISODate("1984-09-30T01:20:26.000Z"),
    age: 33,
    fullName: 'Carl Jacobs'
  },
  {
    location: { type: 'Point', coordinates: [ 168.9462, -22.5329 ] },
    email: 'harvey.chambers@example.com',
    birthdate: ISODate("1988-05-27T00:14:03.000Z"),
    age: 30,
    fullName: 'Harvey Chambers'
  },
  {
    location: { type: 'Point', coordinates: [ -54.1364, -86.1268 ] },
    email: 'gideon.vandrongelen@example.com',
    birthdate: ISODate("1971-03-28T04:47:21.000Z"),
    age: 47,
    fullName: 'Gideon Van drongelen'
  },
  {
    location: { type: 'Point', coordinates: [ -70.2264, 76.4507 ] },
    email: 'zachary.lo@example.com',
    birthdate: ISODate("1988-10-17T03:45:04.000Z"),
    age: 29,
    fullName: 'Zachary Lo'
  },
  {
    location: { type: 'Point', coordinates: [ 111.3806, -31.6359 ] },
    email: 'maeva.wilson@example.com',
    birthdate: ISODate("1962-08-11T20:51:07.000Z"),
    age: 56,
    fullName: 'Maeva Wilson'
  },
  {
    location: { type: 'Point', coordinates: [ 34.1689, 4.6625 ] },
    email: 'پریا.پارسا@example.com',
    birthdate: ISODate("1962-01-10T05:26:30.000Z"),
    age: 56,
    fullName: 'پریا پارسا'
  },
  {
    location: { type: 'Point', coordinates: [ -67.5738, -52.8348 ] },
    email: 'olav.oehme@example.com',
    birthdate: ISODate("1960-11-28T23:07:18.000Z"),
    age: 57,
    fullName: 'Olav Oehme'
  },
  {
    location: { type: 'Point', coordinates: [ -18.5996, -42.6128 ] },
    email: 'elijah.lewis@example.com',
    birthdate: ISODate("1986-03-29T06:40:18.000Z"),
    age: 32,
    fullName: 'Elijah Lewis'
  },
  {
    location: { type: 'Point', coordinates: [ -31.0208, -29.8113 ] },
    email: 'victor.pedersen@example.com',
    birthdate: ISODate("1959-02-19T23:56:23.000Z"),
    age: 59,
    fullName: 'Victor Pedersen'
  },
  {
    location: { type: 'Point', coordinates: [ 148.0944, 35.5726 ] },
    email: 'louise.graham@example.com',
    birthdate: ISODate("1971-01-21T20:36:16.000Z"),
    age: 47,
    fullName: 'Louise Graham'
  },
  {
    location: { type: 'Point', coordinates: [ -172.3753, 83.3998 ] },
    email: 'madeleine.till@example.com',
    birthdate: ISODate("1954-05-01T02:34:40.000Z"),
    age: 64,
    fullName: 'Madeleine Till'
  },
  {
    location: { type: 'Point', coordinates: [ 101.5995, 78.8545 ] },
    email: 'isolino.viana@example.com',
    birthdate: ISODate("1959-03-22T14:53:41.000Z"),
    age: 59,
    fullName: 'Isolino Viana'
  },
  {
    location: { type: 'Point', coordinates: [ 43.9085, 25.1614 ] },
    email: 'mestan.kaplangı@example.com',
    birthdate: ISODate("1951-12-17T20:03:33.000Z"),
    age: 66,
    fullName: 'Mestan Kaplangı'
  },
  {
    location: { type: 'Point', coordinates: [ 135.9359, 71.9851 ] },
    email: 'katie.welch@example.com',
    birthdate: ISODate("1990-10-14T05:02:12.000Z"),
    age: 27,
    fullName: 'Katie Welch'
  },
  {
    location: { type: 'Point', coordinates: [ -90.9499, 21.3388 ] },
    email: 'بنیامین.سالاری@example.com',
    birthdate: ISODate("1984-03-10T22:12:43.000Z"),
    age: 34,
    fullName: 'بنیامین سالاری'
  },
  {
    location: { type: 'Point', coordinates: [ -83.3326, -88.6846 ] },
    email: 'sandra.lorenzo@example.com',
    birthdate: ISODate("1975-03-23T17:01:45.000Z"),
    age: 43,
    fullName: 'Sandra Lorenzo'
  },
  {
    location: { type: 'Point', coordinates: [ -8.557, -14.4912 ] },
    email: 'shona.kemperman@example.com',
    birthdate: ISODate("1948-04-23T03:40:22.000Z"),
    age: 70,
    fullName: 'Shona Kemperman'
  },
  {
    location: { type: 'Point', coordinates: [ 59.5703, -67.6434 ] },
    email: 'andreia.arnaud@example.com',
    birthdate: ISODate("1960-01-31T05:16:10.000Z"),
    age: 58,
    fullName: 'Andreia Arnaud'
  },
  {
    location: { type: 'Point', coordinates: [ -90.4049, -65.0877 ] },
    email: 'delia.durand@example.com',
    birthdate: ISODate("1966-08-03T09:22:41.000Z"),
    age: 52,
    fullName: 'Delia Durand'
  },
  {
    location: { type: 'Point', coordinates: [ 78.0207, -84.1572 ] },
    email: 'anne.ruiz@example.com',
    birthdate: ISODate("1982-10-09T12:10:42.000Z"),
    age: 35,
    fullName: 'Anne Ruiz'
  }
]
Type "it" for more
```