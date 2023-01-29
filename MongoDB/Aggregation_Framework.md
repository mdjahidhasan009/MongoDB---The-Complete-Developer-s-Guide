# Aggregation Framework

**Retrieving Data Efficiently and In a Structured Way**

What is aggregation Framework
Pipeline stages
Steps for find (follow top to down)
                      Collection
                            |               /
                   { `$match` }      /
                            |            /    Every stage receives
                   { `$sort` }     /     the output of the
                            |           \     Previous stage
                 { `$group` }       \
                             |             \
                 { `$project` }       \
                             |
            Output (List of Documents)

# Short Description

Stages and Operations

1. There are plenty of available stages and operations that can choose from.
2. Stages define the different steps and data is funneled through.
3. Each stage receives the output of the last stage as input.
4. Operations can be used inside of stages to transform, limit, or re-calculate data.

Important Stages

1. The most important stages are `$match`, `$group`, `$project`, `$sort` and `$unwind` etc.
2. There are some common behaviors between `find()` filters + `projection` and `$match` + `$project`, but the aggregation stages are more flexible.

Importing person data from JSON file

```bash
mongoimport persons.json -d analytics -c persons --jsonArray
```

```bash
> use analytics
**Output**
switched to db analytics

> db.persons.findOne()
**Output**
{
	"_id" : ObjectId("633672f63d520b88d4dce604"),
	"gender" : "male",
	"name" : {
		"title" : "mr",
		"first" : "victor",
		"last" : "pedersen"
	},
	"location" : {
		"street" : "2156 stenbjergvej",
		"city" : "billum",
		"state" : "nordjylland",
		"postcode" : 56649,
		"coordinates" : {
			"latitude" : "-29.8113",
			"longitude" : "-31.0208"
		},
		"timezone" : {
			"offset" : "+5:30",
			"description" : "Bombay, Calcutta, Madras, New Delhi"
		}
	},
	"email" : "victor.pedersen@example.com",
	"login" : {
		"uuid" : "fbb3c298-2cea-4415-84d1-74233525c325",
		"username" : "smallbutterfly536",
		"password" : "down",
		"salt" : "iW5QrgwW",
		"md5" : "3cc8b8a4d69321a408cd46174e163594",
		"sha1" : "681c0353b34fae08422686eea190e1c09472fc1f",
		"sha256" : "eb5251e929c56dfd19fc597123ed6ec2d0130a2c3c1bf8fc9c2ff8f29830a3b7"
	},
	"dob" : {
		"date" : "1959-02-19T23:56:23Z",
		"age" : 59
	},
	"registered" : {
		"date" : "2004-07-07T22:37:39Z",
		"age" : 14
	},
	"phone" : "23138213",
	"cell" : "30393606",
	"id" : {
		"name" : "CPR",
		"value" : "506102-2208"
	},
	"picture" : {
		"large" : "https://randomuser.me/api/portraits/men/23.jpg",
		"medium" : "https://randomuser.me/api/portraits/med/men/23.jpg",
		"thumbnail" : "https://randomuser.me/api/portraits/thumb/men/23.jpg"
	},
	"nat" : "DK"
}
```

The aggregate method takes an array and it takes an array cause to have to define a series of steps inside the array that should be run on our data. 

> The first step will receive the entire data right from the collection. The next step does something with the data returned by the first step. Aggregate does not go ahead, fetch all the data from the database and give it to the user to do something with that. The first step runs on database and can take advantage of indexes, so if the user filter in the first step or sort there, the user can take advantage of indexes. So users don’t have to fetch all the documents just because the user using aggregate. Aggregate as find executes on the MongoDB server and therefore can take advantage of things like indexes.
> 

```bash
db.persons.aggregate(
	[
		{ $match: {gender: 'female'} }
	]
).pretty()
```

- Output
    
    ```bash
    {
    	"_id" : ObjectId("633672f63d520b88d4dce607"),
    	"gender" : "female",
    	"name" : {
    		"title" : "mrs",
    		"first" : "پریا",
    		"last" : "پارسا"
    	},
    	"location" : {
    		"street" : "2889 اجاره دار",
    		"city" : "بوشهر",
    		"state" : "خراسان جنوبی",
    		"postcode" : 32528,
    		"coordinates" : {
    			"latitude" : "4.6625",
    			"longitude" : "34.1689"
    		},
    		"timezone" : {
    			"offset" : "+3:00",
    			"description" : "Baghdad, Riyadh, Moscow, St. Petersburg"
    		}
    	},
    	"email" : "پریا.پارسا@example.com",
    	"login" : {
    		"uuid" : "75c0d4dd-3a88-42dd-a7ad-5d39b0d8b4b1",
    		"username" : "orangesnake137",
    		"password" : "1031",
    		"salt" : "NRy5mtiy",
    		"md5" : "76aac038463dbbed95cb107811879981",
    		"sha1" : "7013f5d075efcfc35b1cb18298bd7e81db566af2",
    		"sha256" : "6a2792cf9c0386679eb2ca357bcae567907bcfad0d400a56d4c3dd55203d8c61"
    	},
    	"dob" : {
    		"date" : "1962-01-10T05:26:30Z",
    		"age" : 56
    	},
    	"registered" : {
    		"date" : "2015-03-20T08:41:37Z",
    		"age" : 3
    	},
    	"phone" : "053-06884781",
    	"cell" : "0993-557-5092",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/52.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/52.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/52.jpg"
    	},
    	"nat" : "IR"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce608"),
    	"gender" : "female",
    	"name" : {
    		"title" : "miss",
    		"first" : "maeva",
    		"last" : "wilson"
    	},
    	"location" : {
    		"street" : "4962 36th ave",
    		"city" : "jasper",
    		"state" : "northwest territories",
    		"postcode" : "U0A 4J6",
    		"coordinates" : {
    			"latitude" : "-31.6359",
    			"longitude" : "111.3806"
    		},
    		"timezone" : {
    			"offset" : "+9:00",
    			"description" : "Tokyo, Seoul, Osaka, Sapporo, Yakutsk"
    		}
    	},
    	"email" : "maeva.wilson@example.com",
    	"login" : {
    		"uuid" : "8f2d499c-6a7e-4606-8c58-211fdb880c31",
    		"username" : "smallgoose815",
    		"password" : "weston",
    		"salt" : "TyL1q8hK",
    		"md5" : "bcedea61753320688ea27be01982556d",
    		"sha1" : "9e075df851fdaf292170d8e0a92c19ee37fba0f2",
    		"sha256" : "6f022405c6a384de3ab5cfc98cccdd97570e5b412046d05dfb79c23ae37612fa"
    	},
    	"dob" : {
    		"date" : "1962-08-11T20:51:07Z",
    		"age" : 56
    	},
    	"registered" : {
    		"date" : "2016-10-03T10:02:55Z",
    		"age" : 1
    	},
    	"phone" : "727-218-6012",
    	"cell" : "443-382-6538",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/96.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/96.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/96.jpg"
    	},
    	"nat" : "CA"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce60a"),
    	"gender" : "female",
    	"name" : {
    		"title" : "mrs",
    		"first" : "olav",
    		"last" : "oehme"
    	},
    	"location" : {
    		"street" : "gartenstraße 64",
    		"city" : "nastätten",
    		"state" : "mecklenburg-vorpommern",
    		"postcode" : 93640,
    		"coordinates" : {
    			"latitude" : "-52.8348",
    			"longitude" : "-67.5738"
    		},
    		"timezone" : {
    			"offset" : "-3:00",
    			"description" : "Brazil, Buenos Aires, Georgetown"
    		}
    	},
    	"email" : "olav.oehme@example.com",
    	"login" : {
    		"uuid" : "2608c05c-8951-4aee-bcd2-f598c80f32bb",
    		"username" : "heavypeacock371",
    		"password" : "silent",
    		"salt" : "E6R29k0e",
    		"md5" : "587f4392dc06a9fb69a52eae94fd32f2",
    		"sha1" : "82eba68b1fe2407281da5787b9b89505c663b587",
    		"sha256" : "304034638901015d8a7b2fd43530fffc4c7d621ccfab98de935d9c8387198796"
    	},
    	"dob" : {
    		"date" : "1960-11-28T23:07:18Z",
    		"age" : 57
    	},
    	"registered" : {
    		"date" : "2011-05-31T00:24:05Z",
    		"age" : 7
    	},
    	"phone" : "0748-6607336",
    	"cell" : "0176-5787554",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/6.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/6.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/6.jpg"
    	},
    	"nat" : "DE"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce60b"),
    	"gender" : "female",
    	"name" : {
    		"title" : "mrs",
    		"first" : "madeleine",
    		"last" : "till"
    	},
    	"location" : {
    		"street" : "im winkel 166",
    		"city" : "villingen-schwenningen",
    		"state" : "baden-württemberg",
    		"postcode" : 32227,
    		"coordinates" : {
    			"latitude" : "83.3998",
    			"longitude" : "-172.3753"
    		},
    		"timezone" : {
    			"offset" : "+5:30",
    			"description" : "Bombay, Calcutta, Madras, New Delhi"
    		}
    	},
    	"email" : "madeleine.till@example.com",
    	"login" : {
    		"uuid" : "e70766b6-9d4f-419f-85c5-efc3c42db023",
    		"username" : "goldencat450",
    		"password" : "panthers",
    		"salt" : "mHOsDM43",
    		"md5" : "a97539f5d7b6c7302d3f3c1dd1d389b5",
    		"sha1" : "a991dd190a83856b04d41a5e64bc41b5728b6903",
    		"sha256" : "bd123b2b2aae61c6e8d3449bb14c6a032b4860275da781f8ffc73f5500e667bd"
    	},
    	"dob" : {
    		"date" : "1954-05-01T02:34:40Z",
    		"age" : 64
    	},
    	"registered" : {
    		"date" : "2008-06-14T03:14:37Z",
    		"age" : 10
    	},
    	"phone" : "0209-9573743",
    	"cell" : "0173-1226290",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/27.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/27.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/27.jpg"
    	},
    	"nat" : "DE"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce60e"),
    	"gender" : "female",
    	"name" : {
    		"title" : "ms",
    		"first" : "louise",
    		"last" : "graham"
    	},
    	"location" : {
    		"street" : "5108 south street",
    		"city" : "cashel",
    		"state" : "galway",
    		"postcode" : 45802,
    		"coordinates" : {
    			"latitude" : "35.5726",
    			"longitude" : "148.0944"
    		},
    		"timezone" : {
    			"offset" : "+2:00",
    			"description" : "Kaliningrad, South Africa"
    		}
    	},
    	"email" : "louise.graham@example.com",
    	"login" : {
    		"uuid" : "062c592e-c6eb-44c3-beda-fb71e9c13bba",
    		"username" : "whitekoala325",
    		"password" : "harry",
    		"salt" : "OjIRb3hP",
    		"md5" : "fc32be9814936026c62bb21bb4a029cd",
    		"sha1" : "6350de80d1be4af1e7b2a498962f69993082126c",
    		"sha256" : "abc804d7d87f0fd1a058b8db7acdf1d62e0b80412e282c427173c466040b29d1"
    	},
    	"dob" : {
    		"date" : "1971-01-21T20:36:16Z",
    		"age" : 47
    	},
    	"registered" : {
    		"date" : "2002-10-19T17:05:42Z",
    		"age" : 15
    	},
    	"phone" : "011-434-7405",
    	"cell" : "081-010-7792",
    	"id" : {
    		"name" : "PPS",
    		"value" : "9774048T"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/59.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/59.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/59.jpg"
    	},
    	"nat" : "IE"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce60f"),
    	"gender" : "female",
    	"name" : {
    		"title" : "miss",
    		"first" : "mestan",
    		"last" : "kaplangı"
    	},
    	"location" : {
    		"street" : "2575 abanoz sk",
    		"city" : "elazığ",
    		"state" : "kırşehir",
    		"postcode" : 48715,
    		"coordinates" : {
    			"latitude" : "25.1614",
    			"longitude" : "43.9085"
    		},
    		"timezone" : {
    			"offset" : "+4:00",
    			"description" : "Abu Dhabi, Muscat, Baku, Tbilisi"
    		}
    	},
    	"email" : "mestan.kaplangı@example.com",
    	"login" : {
    		"uuid" : "1c449a8b-db88-4754-aae5-b66e0338a479",
    		"username" : "bigelephant762",
    		"password" : "patty",
    		"salt" : "n2sxEnSR",
    		"md5" : "6f6904b481cb51f2e068fad23ceffa73",
    		"sha1" : "276e8c992cf366ff06c868290a164a2727cc1d41",
    		"sha256" : "14a2d569e3ac745afcb07991b61e95e8c85d9e04c1f8725163e466284f4a5900"
    	},
    	"dob" : {
    		"date" : "1951-12-17T20:03:33Z",
    		"age" : 66
    	},
    	"registered" : {
    		"date" : "2006-04-05T12:24:26Z",
    		"age" : 12
    	},
    	"phone" : "(729)-603-0876",
    	"cell" : "(250)-580-1253",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/42.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/42.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/42.jpg"
    	},
    	"nat" : "TR"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce610"),
    	"gender" : "female",
    	"name" : {
    		"title" : "mrs",
    		"first" : "katie",
    		"last" : "welch"
    	},
    	"location" : {
    		"street" : "3356 highfield road",
    		"city" : "dunboyne",
    		"state" : "dublin city",
    		"postcode" : 99090,
    		"coordinates" : {
    			"latitude" : "71.9851",
    			"longitude" : "135.9359"
    		},
    		"timezone" : {
    			"offset" : "-3:00",
    			"description" : "Brazil, Buenos Aires, Georgetown"
    		}
    	},
    	"email" : "katie.welch@example.com",
    	"login" : {
    		"uuid" : "a4262643-0ff9-4c5c-a452-34a4f0dc8d52",
    		"username" : "blackbear565",
    		"password" : "beatles1",
    		"salt" : "2lGmdNiv",
    		"md5" : "f411d7f4bd8ec68c9def4701572509a6",
    		"sha1" : "802e286cef20c0ed2575cce82553a5749570b356",
    		"sha256" : "2688e7f41e9bbf81ea719b916c9a0abc6fb6e75351289d972759e60fb2ed35c6"
    	},
    	"dob" : {
    		"date" : "1990-10-14T05:02:12Z",
    		"age" : 27
    	},
    	"registered" : {
    		"date" : "2007-12-21T20:44:40Z",
    		"age" : 10
    	},
    	"phone" : "011-694-3254",
    	"cell" : "081-184-0215",
    	"id" : {
    		"name" : "PPS",
    		"value" : "4743256T"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/88.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/88.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/88.jpg"
    	},
    	"nat" : "IE"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce611"),
    	"gender" : "female",
    	"name" : {
    		"title" : "miss",
    		"first" : "sandra",
    		"last" : "lorenzo"
    	},
    	"location" : {
    		"street" : "7814 avenida de andalucía",
    		"city" : "pamplona",
    		"state" : "la rioja",
    		"postcode" : 47467,
    		"coordinates" : {
    			"latitude" : "-88.6846",
    			"longitude" : "-83.3326"
    		},
    		"timezone" : {
    			"offset" : "-10:00",
    			"description" : "Hawaii"
    		}
    	},
    	"email" : "sandra.lorenzo@example.com",
    	"login" : {
    		"uuid" : "04986a86-976f-4f3e-b112-244e0aca7cba",
    		"username" : "orangegorilla209",
    		"password" : "viking",
    		"salt" : "v1Igycsr",
    		"md5" : "f57648fcee958b878f636a48ad24839a",
    		"sha1" : "4001ff4031172172aaf46e27b60691be99feed2a",
    		"sha256" : "fd287a853407637110e5df4749cab043391c2b2442aa6e218bde7b5aa5563366"
    	},
    	"dob" : {
    		"date" : "1975-03-23T17:01:45Z",
    		"age" : 43
    	},
    	"registered" : {
    		"date" : "2017-12-13T21:16:35Z",
    		"age" : 0
    	},
    	"phone" : "911-571-659",
    	"cell" : "674-415-588",
    	"id" : {
    		"name" : "DNI",
    		"value" : "87273544-J"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/72.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/72.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/72.jpg"
    	},
    	"nat" : "ES"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce613"),
    	"gender" : "female",
    	"name" : {
    		"title" : "madame",
    		"first" : "andreia",
    		"last" : "arnaud"
    	},
    	"location" : {
    		"street" : "7425 route de genas",
    		"city" : "l'abbaye",
    		"state" : "schwyz",
    		"postcode" : 7441,
    		"coordinates" : {
    			"latitude" : "-67.6434",
    			"longitude" : "59.5703"
    		},
    		"timezone" : {
    			"offset" : "-12:00",
    			"description" : "Eniwetok, Kwajalein"
    		}
    	},
    	"email" : "andreia.arnaud@example.com",
    	"login" : {
    		"uuid" : "6f647090-766c-4708-920c-bf4fcc617d15",
    		"username" : "organicgoose539",
    		"password" : "bestbuy",
    		"salt" : "2g7xpk8Z",
    		"md5" : "e032913bb9e7c0504cff46efa0cf01cd",
    		"sha1" : "ffaa47cdc0891dfcbea3e4076999235c49635576",
    		"sha256" : "6e392bc95c930bb6e828b814a3ba554e0eebcdbd0505346f81995be8ec774b4f"
    	},
    	"dob" : {
    		"date" : "1960-01-31T05:16:10Z",
    		"age" : 58
    	},
    	"registered" : {
    		"date" : "2003-10-19T20:41:13Z",
    		"age" : 14
    	},
    	"phone" : "(563)-009-4755",
    	"cell" : "(842)-068-0366",
    	"id" : {
    		"name" : "AVS",
    		"value" : "756.5206.2620.86"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/3.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/3.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/3.jpg"
    	},
    	"nat" : "CH"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce614"),
    	"gender" : "female",
    	"name" : {
    		"title" : "miss",
    		"first" : "shona",
    		"last" : "kemperman"
    	},
    	"location" : {
    		"street" : "2685 adriaen van ostadelaan",
    		"city" : "giessenlanden",
    		"state" : "noord-brabant",
    		"postcode" : 57798,
    		"coordinates" : {
    			"latitude" : "-14.4912",
    			"longitude" : "-8.5570"
    		},
    		"timezone" : {
    			"offset" : "-12:00",
    			"description" : "Eniwetok, Kwajalein"
    		}
    	},
    	"email" : "shona.kemperman@example.com",
    	"login" : {
    		"uuid" : "45d4a86e-c007-45af-9489-49ff77c4905c",
    		"username" : "bluetiger298",
    		"password" : "xander",
    		"salt" : "ANHBICA9",
    		"md5" : "56560a9b266dac51c04c43a9a845b7af",
    		"sha1" : "419f27be45ebb9635904e61c7e865ce13d963f63",
    		"sha256" : "10e7dab700ebd2b0edfced679f2cd4e4f67d2f65711d7af62e04d282b65a4161"
    	},
    	"dob" : {
    		"date" : "1948-04-23T03:40:22Z",
    		"age" : 70
    	},
    	"registered" : {
    		"date" : "2003-12-29T05:37:33Z",
    		"age" : 14
    	},
    	"phone" : "(132)-806-7647",
    	"cell" : "(766)-268-3084",
    	"id" : {
    		"name" : "BSN",
    		"value" : "99563655"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/14.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/14.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/14.jpg"
    	},
    	"nat" : "NL"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce616"),
    	"gender" : "female",
    	"name" : {
    		"title" : "miss",
    		"first" : "anne",
    		"last" : "ruiz"
    	},
    	"location" : {
    		"street" : "5523 cackson st",
    		"city" : "devonport",
    		"state" : "northern territory",
    		"postcode" : 9797,
    		"coordinates" : {
    			"latitude" : "-84.1572",
    			"longitude" : "78.0207"
    		},
    		"timezone" : {
    			"offset" : "+9:00",
    			"description" : "Tokyo, Seoul, Osaka, Sapporo, Yakutsk"
    		}
    	},
    	"email" : "anne.ruiz@example.com",
    	"login" : {
    		"uuid" : "7ee5ffa7-ea97-40a4-a476-3d4673bba13b",
    		"username" : "happyrabbit405",
    		"password" : "1qaz2wsx",
    		"salt" : "I2cXRgLP",
    		"md5" : "7e660dcb6162f659f0cc658cf0d0722e",
    		"sha1" : "4bf5521bb7e22092df2f3d0c9e09d7530ab95fc6",
    		"sha256" : "b658ea12176845a7e0c344dcc8ca7e11bdda1bb5601b4e13c9425ecf33820702"
    	},
    	"dob" : {
    		"date" : "1982-10-09T12:10:42Z",
    		"age" : 35
    	},
    	"registered" : {
    		"date" : "2016-09-06T20:51:12Z",
    		"age" : 2
    	},
    	"phone" : "06-1601-9518",
    	"cell" : "0479-884-985",
    	"id" : {
    		"name" : "TFN",
    		"value" : "987210251"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/51.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/51.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/51.jpg"
    	},
    	"nat" : "AU"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce617"),
    	"gender" : "female",
    	"name" : {
    		"title" : "mrs",
    		"first" : "gonca",
    		"last" : "alnıaçık"
    	},
    	"location" : {
    		"street" : "7115 istiklal cd",
    		"city" : "sakarya",
    		"state" : "muğla",
    		"postcode" : 61085,
    		"coordinates" : {
    			"latitude" : "88.1818",
    			"longitude" : "130.0105"
    		},
    		"timezone" : {
    			"offset" : "-5:00",
    			"description" : "Eastern Time (US & Canada), Bogota, Lima"
    		}
    	},
    	"email" : "gonca.alnıaçık@example.com",
    	"login" : {
    		"uuid" : "2830a251-208d-45e0-9f66-3872d703287b",
    		"username" : "organicpanda295",
    		"password" : "phillies",
    		"salt" : "CPA0ODBu",
    		"md5" : "2ecc7a3c283961ca2f2fdcb46dd3f4a4",
    		"sha1" : "27cd0890760248faf6d77ebefda8bc12ce7e26f2",
    		"sha256" : "058a0c02aae03686ca24d838faa705e53bd24443bc5397ca560d5f5a25e44fc3"
    	},
    	"dob" : {
    		"date" : "1988-11-08T00:18:59Z",
    		"age" : 29
    	},
    	"registered" : {
    		"date" : "2015-03-18T02:22:49Z",
    		"age" : 3
    	},
    	"phone" : "(754)-546-4300",
    	"cell" : "(246)-176-8953",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/66.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/66.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/66.jpg"
    	},
    	"nat" : "TR"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce618"),
    	"gender" : "female",
    	"name" : {
    		"title" : "miss",
    		"first" : "maeva",
    		"last" : "grewal"
    	},
    	"location" : {
    		"street" : "9148 brock rd",
    		"city" : "shelbourne",
    		"state" : "québec",
    		"postcode" : "O8H 9F7",
    		"coordinates" : {
    			"latitude" : "-13.9479",
    			"longitude" : "-48.3596"
    		},
    		"timezone" : {
    			"offset" : "+11:00",
    			"description" : "Magadan, Solomon Islands, New Caledonia"
    		}
    	},
    	"email" : "maeva.grewal@example.com",
    	"login" : {
    		"uuid" : "38a6bd71-1c0a-4c65-9816-044f8619fc22",
    		"username" : "bluekoala192",
    		"password" : "horton",
    		"salt" : "35WAs1sb",
    		"md5" : "ff8a456c0e851045c4d4c572be3c6a2d",
    		"sha1" : "862813403bb6beeba403b9e6947e8df38b86c8b2",
    		"sha256" : "d9b03703f625a592ee43d93a664e6d9c448c4147f6e77562571fc92b133ab338"
    	},
    	"dob" : {
    		"date" : "1972-04-23T04:31:25Z",
    		"age" : 46
    	},
    	"registered" : {
    		"date" : "2012-01-24T03:04:47Z",
    		"age" : 6
    	},
    	"phone" : "282-026-0240",
    	"cell" : "842-037-3131",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/29.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/29.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/29.jpg"
    	},
    	"nat" : "CA"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce61a"),
    	"gender" : "female",
    	"name" : {
    		"title" : "ms",
    		"first" : "signe",
    		"last" : "rasmussen"
    	},
    	"location" : {
    		"street" : "3635 koldingvej",
    		"city" : "ryslinge",
    		"state" : "sjælland",
    		"postcode" : 44840,
    		"coordinates" : {
    			"latitude" : "18.2563",
    			"longitude" : "14.0142"
    		},
    		"timezone" : {
    			"offset" : "+4:30",
    			"description" : "Kabul"
    		}
    	},
    	"email" : "signe.rasmussen@example.com",
    	"login" : {
    		"uuid" : "687099d9-b4bd-4881-8657-90abd559844f",
    		"username" : "sadpeacock229",
    		"password" : "baggio",
    		"salt" : "J1WBRau1",
    		"md5" : "2b0824d7f90452cc93a879aaf717fa40",
    		"sha1" : "d7fc48d34816bccf8cac0c532b06e37e65974759",
    		"sha256" : "2388d9162c447e163fdaaf94fb99bc5ca42a93de167d82eed59e296f8d1de8dd"
    	},
    	"dob" : {
    		"date" : "1983-05-20T21:26:44Z",
    		"age" : 35
    	},
    	"registered" : {
    		"date" : "2010-09-30T04:08:40Z",
    		"age" : 7
    	},
    	"phone" : "46945447",
    	"cell" : "97293211",
    	"id" : {
    		"name" : "CPR",
    		"value" : "082246-7576"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/5.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/5.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/5.jpg"
    	},
    	"nat" : "DK"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce61b"),
    	"gender" : "female",
    	"name" : {
    		"title" : "mrs",
    		"first" : "aya",
    		"last" : "liland"
    	},
    	"location" : {
    		"street" : "olav nygards veg 1791",
    		"city" : "sigerfjord",
    		"state" : "trøndelag",
    		"postcode" : "7864",
    		"coordinates" : {
    			"latitude" : "-88.4983",
    			"longitude" : "-104.3451"
    		},
    		"timezone" : {
    			"offset" : "-5:00",
    			"description" : "Eastern Time (US & Canada), Bogota, Lima"
    		}
    	},
    	"email" : "aya.liland@example.com",
    	"login" : {
    		"uuid" : "80060b57-a1d1-4e54-afd2-9574109b8a51",
    		"username" : "bigleopard842",
    		"password" : "summers",
    		"salt" : "ZDeNt44y",
    		"md5" : "bff572b29319a86443150126fa98746c",
    		"sha1" : "726f00762aeff28a346f2b8bbeea601742a825f7",
    		"sha256" : "f626dd80e8716f182c9fac453d58b472706f5429b48e8348448b19db5fa20e9b"
    	},
    	"dob" : {
    		"date" : "1973-08-26T00:11:58Z",
    		"age" : 45
    	},
    	"registered" : {
    		"date" : "2013-05-05T05:44:54Z",
    		"age" : 5
    	},
    	"phone" : "32343880",
    	"cell" : "98392867",
    	"id" : {
    		"name" : "FN",
    		"value" : "26087347329"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/34.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/34.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/34.jpg"
    	},
    	"nat" : "NO"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce61c"),
    	"gender" : "female",
    	"name" : {
    		"title" : "mademoiselle",
    		"first" : "delia",
    		"last" : "durand"
    	},
    	"location" : {
    		"street" : "6735 rue abel-hovelacque",
    		"city" : "fontaines-sur-grandson",
    		"state" : "appenzell ausserrhoden",
    		"postcode" : 6137,
    		"coordinates" : {
    			"latitude" : "-65.0877",
    			"longitude" : "-90.4049"
    		},
    		"timezone" : {
    			"offset" : "+2:00",
    			"description" : "Kaliningrad, South Africa"
    		}
    	},
    	"email" : "delia.durand@example.com",
    	"login" : {
    		"uuid" : "6c4ea050-eca4-4809-8024-96a98f655553",
    		"username" : "purplebear663",
    		"password" : "sparky1",
    		"salt" : "rzU1EPXV",
    		"md5" : "a9198f953f34daf7b461b6065734d13b",
    		"sha1" : "43c50f99742937b3ddd3f9ec0863924741b408c9",
    		"sha256" : "63da5442d2a4d2ca02e0b3bb138e28760aa732258e35de102c8fd652c48721ea"
    	},
    	"dob" : {
    		"date" : "1966-08-03T09:22:41Z",
    		"age" : 52
    	},
    	"registered" : {
    		"date" : "2009-05-28T19:09:24Z",
    		"age" : 9
    	},
    	"phone" : "(166)-728-8912",
    	"cell" : "(301)-529-4162",
    	"id" : {
    		"name" : "AVS",
    		"value" : "756.5746.5832.39"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/91.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/91.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/91.jpg"
    	},
    	"nat" : "CH"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce61e"),
    	"gender" : "female",
    	"name" : {
    		"title" : "ms",
    		"first" : "anaëlle",
    		"last" : "adam"
    	},
    	"location" : {
    		"street" : "6952 rue abel-ferry",
    		"city" : "toulouse",
    		"state" : "mayenne",
    		"postcode" : 69995,
    		"coordinates" : {
    			"latitude" : "3.6559",
    			"longitude" : "174.2405"
    		},
    		"timezone" : {
    			"offset" : "+8:00",
    			"description" : "Beijing, Perth, Singapore, Hong Kong"
    		}
    	},
    	"email" : "anaëlle.adam@example.com",
    	"login" : {
    		"uuid" : "1bb3f501-b464-4e4b-a564-09d9b7b82721",
    		"username" : "brownwolf302",
    		"password" : "gaston",
    		"salt" : "eFuVRBvj",
    		"md5" : "aea71691758ed3149dfe6ff09f99a280",
    		"sha1" : "d97cafb941fa4b54f891eecf856de90fed00b43e",
    		"sha256" : "25e7da7cddeca26e733e74a5414616fc565fbb5847f5a9f749565477459cfbf3"
    	},
    	"dob" : {
    		"date" : "1987-10-20T11:33:44Z",
    		"age" : 30
    	},
    	"registered" : {
    		"date" : "2008-01-09T05:32:18Z",
    		"age" : 10
    	},
    	"phone" : "01-38-71-69-99",
    	"cell" : "06-94-15-71-81",
    	"id" : {
    		"name" : "INSEE",
    		"value" : "2NNaN77968794 05"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/82.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/82.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/82.jpg"
    	},
    	"nat" : "FR"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce61f"),
    	"gender" : "female",
    	"name" : {
    		"title" : "miss",
    		"first" : "monica",
    		"last" : "hart"
    	},
    	"location" : {
    		"street" : "2801 e sandy lake rd",
    		"city" : "seattle",
    		"state" : "alabama",
    		"postcode" : 21117,
    		"coordinates" : {
    			"latitude" : "-26.1520",
    			"longitude" : "146.2648"
    		},
    		"timezone" : {
    			"offset" : "-9:00",
    			"description" : "Alaska"
    		}
    	},
    	"email" : "monica.hart@example.com",
    	"login" : {
    		"uuid" : "aa185cb7-5263-4f3d-9d25-ec2affee638c",
    		"username" : "goldenbird983",
    		"password" : "kyle",
    		"salt" : "eV1rmYSw",
    		"md5" : "43813b31306b063b72a680d95bbe7412",
    		"sha1" : "51256ec56c70b2c3381c02ed1d0f23dc69887248",
    		"sha256" : "a539ceaeb6a2055bf41cff3f33193065b3f0452122185f2154f397b2f61cbfe6"
    	},
    	"dob" : {
    		"date" : "1969-10-22T08:07:25Z",
    		"age" : 48
    	},
    	"registered" : {
    		"date" : "2009-04-08T20:54:55Z",
    		"age" : 9
    	},
    	"phone" : "(957)-810-1345",
    	"cell" : "(685)-189-1125",
    	"id" : {
    		"name" : "SSN",
    		"value" : "353-24-6860"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/20.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/20.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/20.jpg"
    	},
    	"nat" : "US"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce622"),
    	"gender" : "female",
    	"name" : {
    		"title" : "ms",
    		"first" : "maya",
    		"last" : "macdonald"
    	},
    	"location" : {
    		"street" : "100 george st",
    		"city" : "winfield",
    		"state" : "yukon",
    		"postcode" : "K4Q 2U2",
    		"coordinates" : {
    			"latitude" : "9.4191",
    			"longitude" : "-102.1065"
    		},
    		"timezone" : {
    			"offset" : "+9:00",
    			"description" : "Tokyo, Seoul, Osaka, Sapporo, Yakutsk"
    		}
    	},
    	"email" : "maya.macdonald@example.com",
    	"login" : {
    		"uuid" : "934e8a54-0d46-4cd7-accd-a0a67f19299b",
    		"username" : "bigfish793",
    		"password" : "kiss",
    		"salt" : "IYteMQWZ",
    		"md5" : "6d975a2ef56a3f129949ee6133dca67e",
    		"sha1" : "22a304b1577abe6b303f016d4069f81c4aac814c",
    		"sha256" : "da09a9dff1d93bc54044e89c18df814f1bc651331708834a310458a96145260c"
    	},
    	"dob" : {
    		"date" : "1953-01-17T16:10:03Z",
    		"age" : 65
    	},
    	"registered" : {
    		"date" : "2009-10-08T16:00:18Z",
    		"age" : 8
    	},
    	"phone" : "343-367-3594",
    	"cell" : "329-394-0526",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/73.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/73.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/73.jpg"
    	},
    	"nat" : "CA"
    }
    {
    	"_id" : ObjectId("633672f63d520b88d4dce624"),
    	"gender" : "female",
    	"name" : {
    		"title" : "miss",
    		"first" : "kristin",
    		"last" : "rivera"
    	},
    	"location" : {
    		"street" : "9566 poplar dr",
    		"city" : "dubbo",
    		"state" : "new south wales",
    		"postcode" : 7009,
    		"coordinates" : {
    			"latitude" : "64.1475",
    			"longitude" : "-79.1137"
    		},
    		"timezone" : {
    			"offset" : "+2:00",
    			"description" : "Kaliningrad, South Africa"
    		}
    	},
    	"email" : "kristin.rivera@example.com",
    	"login" : {
    		"uuid" : "03a1471d-7315-4b52-a38d-4e3b44f8c23e",
    		"username" : "whitefrog819",
    		"password" : "candace",
    		"salt" : "AX2D9bn8",
    		"md5" : "107c3695197b1b36c1cf0f05b8500214",
    		"sha1" : "80232ba76e352fdaa1a81ea3233c52b910709a68",
    		"sha256" : "f1f0ca7f2822df9b21f017a9b17ff62c1040e579a6216465d910b28f9989b695"
    	},
    	"dob" : {
    		"date" : "1971-01-03T18:58:20Z",
    		"age" : 47
    	},
    	"registered" : {
    		"date" : "2002-08-29T03:23:51Z",
    		"age" : 16
    	},
    	"phone" : "03-5559-9965",
    	"cell" : "0434-189-807",
    	"id" : {
    		"name" : "TFN",
    		"value" : "884037090"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/21.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/21.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/21.jpg"
    	},
    	"nat" : "AU"
    }
    Type "it" for more
    ```
    

[Group](Aggregation_Framework/Group.md)

[Project](Aggregation_Framework/Project.md)

- [Turning the Location Into a geoJSON Object ](Aggregation_Framework/Project/Turning_the_Location_Into_a_geoJSON_Object.md)
- [Understanding the ISO Week Year Operator](Aggregation_Framework/Project/Understanding_the_ISO_Week_Year_Operator.md)

# `$group` vs `$project`

`$group` :

1. grouping multiple documents into **one document**.
2. n:1 ⇒ many to one relation.
3. have multiple documents and return one grouped by one or more categories.
4. do things like **sum**, **count**, **average**, **build an array**, and so on

`$project`:

1. get one document and then will return **one document**, that one document we'll just have **changed**.
2. 1:1 ⇒ One-to-one relation.
3. transform a single document, add new fields, and so on.
4. have a one-to-one relation, **include/exclude fields**.

[Array](Aggregation_Framework/Array.md)

[Bucket](Aggregation_Framework/Bucket.md)

[Additional Stages](Aggregation_Framework/Additional_Stages.md)

[Writing Pipeline Results Into a New Collection](Aggregation_Framework/Writing_Pipeline_Results_Into_a_New_Collection.md)

[GeoNear](Aggregation_Framework/GeoNear.md)

# Stages & Operators

- There are plenty of available stages and operators you can choose from.
- Stages define the different steps your data is funneled through.
- Each stage receives the output of the last stage as input.
- Operators can be used inside of stages to transform, limit or re-calculate data.

# Important Stages

- The most important stages are `$match` , `$group` , `$project` , `$sort`  and `$unwind`.
- Whilst there are some common behaviours between find() filters + projection and `$match` + `$project` , the aggregation stages generally are more flexible.