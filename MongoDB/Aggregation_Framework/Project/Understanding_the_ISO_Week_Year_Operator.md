# Understanding the ISO Week Year Operator

`$isoWeekYear` retries the year out of date

`_id: { birthYear: { $isoWeekYear: '$birthdate' } },` ⇒ Group using `birthdate`  field get the first project, convert it in `isoWeekYear`, and returned key/field name will be `birthYear`
`numPersons: { $sum: 1 }` ⇒ Return the total number/count of person in a year.

```cpp
> db.persons.aggregate([
    {
        $project: {
            _id: 0,
            name: 1,
            email: 1,
            birthdate: { $toDate: '$dob.date' },
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
    },
    { 
				$group: { 
						_id: { birthYear: { $isoWeekYear: '$birthdate' } }, 
						numPersons: { $sum: 1 } 
				} 
		}
]).pretty()

**Output**
[
  { _id: { birthYear: Long("1988") }, numPersons: 89 },
  { _id: { birthYear: Long("1950") }, numPersons: 99 },
  { _id: { birthYear: Long("1954") }, numPersons: 95 },
  { _id: { birthYear: Long("1953") }, numPersons: 97 },
  { _id: { birthYear: Long("1977") }, numPersons: 86 },
  { _id: { birthYear: Long("1959") }, numPersons: 93 },
  { _id: { birthYear: Long("1971") }, numPersons: 88 },
  { _id: { birthYear: Long("1962") }, numPersons: 90 },
  { _id: { birthYear: Long("1992") }, numPersons: 96 },
  { _id: { birthYear: Long("1966") }, numPersons: 92 },
  { _id: { birthYear: Long("1946") }, numPersons: 100 },
  { _id: { birthYear: Long("1947") }, numPersons: 93 },
  { _id: { birthYear: Long("1993") }, numPersons: 110 },
  { _id: { birthYear: Long("1968") }, numPersons: 96 },
  { _id: { birthYear: Long("1980") }, numPersons: 86 },
  { _id: { birthYear: Long("1982") }, numPersons: 96 },
  { _id: { birthYear: Long("1963") }, numPersons: 98 },
  { _id: { birthYear: Long("1979") }, numPersons: 84 },
  { _id: { birthYear: Long("1975") }, numPersons: 107 },
  { _id: { birthYear: Long("1951") }, numPersons: 79 }
]
Type "it" for more
```

Adding sort

```cpp
> db.persons.aggregate([
    {
        $project: {
            _id: 0,
            name: 1,
            email: 1,
            birthdate: { $toDate: '$dob.date' },
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
    },
        { 
				$group: { 
						_id: { birthYear: { $isoWeekYear: '$birthdate' } }, 
						numPersons: { $sum: 1 } 
				} 
		},
    { $sort: { numPersons: -1}}
]).pretty()

**Output**
[
  { _id: { birthYear: Long("1955") }, numPersons: 113 },
  { _id: { birthYear: Long("1961") }, numPersons: 111 },
  { _id: { birthYear: Long("1993") }, numPersons: 110 },
  { _id: { birthYear: Long("1960") }, numPersons: 110 },
  { _id: { birthYear: Long("1975") }, numPersons: 107 },
  { _id: { birthYear: Long("1945") }, numPersons: 106 },
  { _id: { birthYear: Long("1976") }, numPersons: 105 },
  { _id: { birthYear: Long("1967") }, numPersons: 104 },
  { _id: { birthYear: Long("1990") }, numPersons: 103 },
  { _id: { birthYear: Long("1994") }, numPersons: 102 },
  { _id: { birthYear: Long("1981") }, numPersons: 102 },
  { _id: { birthYear: Long("1958") }, numPersons: 101 },
  { _id: { birthYear: Long("1995") }, numPersons: 101 },
  { _id: { birthYear: Long("1946") }, numPersons: 100 },
  { _id: { birthYear: Long("1948") }, numPersons: 100 },
  { _id: { birthYear: Long("1950") }, numPersons: 99 },
  { _id: { birthYear: Long("1983") }, numPersons: 99 },
  { _id: { birthYear: Long("1970") }, numPersons: 99 },
  { _id: { birthYear: Long("1963") }, numPersons: 98 },
  { _id: { birthYear: Long("1965") }, numPersons: 98 }
]
Type "it" for more
```

#