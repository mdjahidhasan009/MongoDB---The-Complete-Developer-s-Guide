# Index and others

# Will add

- COLLSCAN vs IXSCAN
- plan ⇒ winningPlan and rejectionPlan

- Reference image for COLLSCAN vs IXSCAN
    
    ![index.PNG](Index_and_Others/index.png)
    

COLLSCAN vs IXSCAN

COLLSCAN  ⇒ Scan all documents then filter. (No index)
IXSCAN ⇒ Scan in the already sorted document.

Different Types of Indexes
Using and Optimizing Indexes
Indexes are order lists of values
Its point-related index is just like a pointer indexes
Indexes are updated with every insert

### What and Why

1. Indexes allow the retrieval of data more efficiently (if used correctly) because queries only have to look at a subset of all documents.
2. Can use single-field, compound, multi-key(array), and text indexes.
3. Indexes don't come for free, they will slow down writing. In inserting have to redo all indexes again.
---------- Queries & Sorting -----------
4. Indexes can be used for both queries and efficient sorting.
5. Compound indexes can be used as a whole or in a 'left-to-right' (prefix) manner (e.g only consider the 'name' of the 'name-age' compound index)
- Importing data
    
    The JSON file is the persons.json have to write this command before starting mongo server using `mongo`
    
    ```bash
    mongoimport persons.json -d contactData -c contacts --jsonArray
    **Output** 
    2022-09-24T12:29:31.550+0600	connected to: localhost
    2022-09-24T12:29:31.912+0600	imported 5000 documents
    ```
    
    Checking the datebase
    
    ```bash
    mongo
    > show dbs
    **Output**
    admin                         0.000GB
    config                        0.000GB
    contactData                   0.008GB
    local                         0.001GB
    //there were many more databases in my machine just not including those here
    
    > use contactData
    **Output**
    switched to db contactData
    
    > show collections
    **Output**
    contacts
    
    > db.contacts.findOne({})
    **Output**
    {
    	"_id" : ObjectId("632e94b79d41938c381fe6bc"),
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
    

# Adding a Single Field Index

```bash
> use contactData
> db.contacts.find({'dob.age': {$gt: 60}}).pretty()
```

- Output
    
    ```bash
    {
    	"_id" : ObjectId("632e94b79d41938c381fe6c3"),
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
    	"_id" : ObjectId("632e94b79d41938c381fe6c5"),
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
    	"_id" : ObjectId("632e94b79d41938c381fe6c8"),
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
    	"_id" : ObjectId("632e94b79d41938c381fe6d5"),
    	"gender" : "male",
    	"name" : {
    		"title" : "mr",
    		"first" : "alfred",
    		"last" : "jensen"
    	},
    	"location" : {
    		"street" : "9046 østervænget",
    		"city" : "fredeikssund",
    		"state" : "midtjylland",
    		"postcode" : 41217,
    		"coordinates" : {
    			"latitude" : "-84.5153",
    			"longitude" : "149.2542"
    		},
    		"timezone" : {
    			"offset" : "0:00",
    			"description" : "Western Europe Time, London, Lisbon, Casablanca"
    		}
    	},
    	"email" : "alfred.jensen@example.com",
    	"login" : {
    		"uuid" : "7c98d741-ac05-4ab8-80a4-931da8f10cdb",
    		"username" : "browngorilla386",
    		"password" : "good",
    		"salt" : "8MYPa5RU",
    		"md5" : "bc1309ff26cb3244aad66d14f887a6c1",
    		"sha1" : "4b6d42a013e69e36dc7922d0fa520769e4929036",
    		"sha256" : "c50182dcb91599580f722fadc1caee4ae78725a48668e24f057fb8a1d9b1e200"
    	},
    	"dob" : {
    		"date" : "1957-06-28T13:29:32Z",
    		"age" : 61
    	},
    	"registered" : {
    		"date" : "2018-04-26T14:45:03Z",
    		"age" : 0
    	},
    	"phone" : "63143258",
    	"cell" : "79862806",
    	"id" : {
    		"name" : "CPR",
    		"value" : "813996-3008"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/men/94.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/men/94.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/men/94.jpg"
    	},
    	"nat" : "DK"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe6da"),
    	"gender" : "male",
    	"name" : {
    		"title" : "mr",
    		"first" : "lincoln",
    		"last" : "lewis"
    	},
    	"location" : {
    		"street" : "8384 high street",
    		"city" : "whangarei",
    		"state" : "hawke's bay",
    		"postcode" : 14855,
    		"coordinates" : {
    			"latitude" : "-2.2081",
    			"longitude" : "62.5954"
    		},
    		"timezone" : {
    			"offset" : "-6:00",
    			"description" : "Central Time (US & Canada), Mexico City"
    		}
    	},
    	"email" : "lincoln.lewis@example.com",
    	"login" : {
    		"uuid" : "997a6fa7-75b9-4b3d-8ab8-686a641046a2",
    		"username" : "ticklishtiger804",
    		"password" : "markie",
    		"salt" : "NfePDtuT",
    		"md5" : "04203f9f47bfac62440df395e683f028",
    		"sha1" : "395540221f6e6308e58580f180876f22124c9a0c",
    		"sha256" : "70a223d2a27f4a12ec4a99209bba528db9a0d712f416bc3ef73a2aabbbae45b2"
    	},
    	"dob" : {
    		"date" : "1948-08-16T11:51:26Z",
    		"age" : 70
    	},
    	"registered" : {
    		"date" : "2016-10-14T19:57:42Z",
    		"age" : 1
    	},
    	"phone" : "(161)-779-6350",
    	"cell" : "(660)-055-2689",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/men/75.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/men/75.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/men/75.jpg"
    	},
    	"nat" : "NZ"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe6de"),
    	"gender" : "female",
    	"name" : {
    		"title" : "mrs",
    		"first" : "isobel",
    		"last" : "lewis"
    	},
    	"location" : {
    		"street" : "2999 fraser street",
    		"city" : "nelson",
    		"state" : "southland",
    		"postcode" : 31241,
    		"coordinates" : {
    			"latitude" : "-12.0354",
    			"longitude" : "-36.1723"
    		},
    		"timezone" : {
    			"offset" : "-11:00",
    			"description" : "Midway Island, Samoa"
    		}
    	},
    	"email" : "isobel.lewis@example.com",
    	"login" : {
    		"uuid" : "93f6e608-5923-4794-8091-da1710600554",
    		"username" : "blackduck464",
    		"password" : "airwolf",
    		"salt" : "hLH8bhVE",
    		"md5" : "1a09c2b46f6102adfa8cd260f30e5486",
    		"sha1" : "b5ce104132ad30d64543fcdef30fd9086121a951",
    		"sha256" : "904004a3bdb59ad4e8bcfa2c01cf9a3081cba8957da16b483dc9f8b2ae91f467"
    	},
    	"dob" : {
    		"date" : "1951-04-18T03:58:19Z",
    		"age" : 67
    	},
    	"registered" : {
    		"date" : "2016-02-08T13:45:26Z",
    		"age" : 2
    	},
    	"phone" : "(105)-076-9099",
    	"cell" : "(134)-115-7068",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/37.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/37.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/37.jpg"
    	},
    	"nat" : "NZ"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe6e0"),
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
    	"_id" : ObjectId("632e94b79d41938c381fe6e5"),
    	"gender" : "male",
    	"name" : {
    		"title" : "mr",
    		"first" : "julio",
    		"last" : "martin"
    	},
    	"location" : {
    		"street" : "8525 parker rd",
    		"city" : "warrnambool",
    		"state" : "victoria",
    		"postcode" : 5104,
    		"coordinates" : {
    			"latitude" : "61.8618",
    			"longitude" : "-68.8352"
    		},
    		"timezone" : {
    			"offset" : "+10:00",
    			"description" : "Eastern Australia, Guam, Vladivostok"
    		}
    	},
    	"email" : "julio.martin@example.com",
    	"login" : {
    		"uuid" : "4037ef36-5f9c-4a0e-bc63-653ffa636538",
    		"username" : "browngoose330",
    		"password" : "apollo13",
    		"salt" : "M9mwvgR5",
    		"md5" : "da8b1289c97d104b3219fbbdec1b2155",
    		"sha1" : "6f32c3670d8be73387177b8b39854927445aaefb",
    		"sha256" : "e8af77373c33d226850c85fcbf0253254c122cbf02c8943568e85c627ce098ad"
    	},
    	"dob" : {
    		"date" : "1956-02-23T05:47:13Z",
    		"age" : 62
    	},
    	"registered" : {
    		"date" : "2015-10-13T17:34:35Z",
    		"age" : 2
    	},
    	"phone" : "02-3129-9559",
    	"cell" : "0474-284-226",
    	"id" : {
    		"name" : "TFN",
    		"value" : "349776453"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/men/97.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/men/97.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/men/97.jpg"
    	},
    	"nat" : "AU"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe6e6"),
    	"gender" : "female",
    	"name" : {
    		"title" : "miss",
    		"first" : "elsie",
    		"last" : "sullivan"
    	},
    	"location" : {
    		"street" : "8882 central st",
    		"city" : "devonport",
    		"state" : "tasmania",
    		"postcode" : 1802,
    		"coordinates" : {
    			"latitude" : "2.9175",
    			"longitude" : "-18.8868"
    		},
    		"timezone" : {
    			"offset" : "-4:00",
    			"description" : "Atlantic Time (Canada), Caracas, La Paz"
    		}
    	},
    	"email" : "elsie.sullivan@example.com",
    	"login" : {
    		"uuid" : "e6e361fc-7412-434b-92fb-0f417588980a",
    		"username" : "brownelephant930",
    		"password" : "walnut",
    		"salt" : "QpIFbS9F",
    		"md5" : "715eda0767aca6941b47e955766b76e9",
    		"sha1" : "002b8e35bf0ee92d9a9f5e837cfee9ffdaf34aff",
    		"sha256" : "d24b1a77c54fe92ddb989bb74464e7601506b527efb45ee6972f5f9de0285562"
    	},
    	"dob" : {
    		"date" : "1946-05-15T06:22:30Z",
    		"age" : 72
    	},
    	"registered" : {
    		"date" : "2009-04-18T21:54:52Z",
    		"age" : 9
    	},
    	"phone" : "09-7910-0569",
    	"cell" : "0486-471-663",
    	"id" : {
    		"name" : "TFN",
    		"value" : "721909596"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/69.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/69.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/69.jpg"
    	},
    	"nat" : "AU"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe6ea"),
    	"gender" : "male",
    	"name" : {
    		"title" : "mr",
    		"first" : "vilho",
    		"last" : "saarela"
    	},
    	"location" : {
    		"street" : "1153 satakennankatu",
    		"city" : "rusko",
    		"state" : "northern savonia",
    		"postcode" : 83170,
    		"coordinates" : {
    			"latitude" : "62.4539",
    			"longitude" : "-67.0065"
    		},
    		"timezone" : {
    			"offset" : "+8:00",
    			"description" : "Beijing, Perth, Singapore, Hong Kong"
    		}
    	},
    	"email" : "vilho.saarela@example.com",
    	"login" : {
    		"uuid" : "56ef4449-8595-4374-91fa-a481dbd50456",
    		"username" : "bigkoala821",
    		"password" : "1005",
    		"salt" : "ag0xkbII",
    		"md5" : "cfcbbec97d47dcc95ac85d9f10e27ae9",
    		"sha1" : "586e3806cb0eebadf6180a38f94c05764f12db66",
    		"sha256" : "39672b300f4e00e437d4122f99b9342e515b4d06ecba8b346c78ccec2680bf3d"
    	},
    	"dob" : {
    		"date" : "1954-02-20T14:31:35Z",
    		"age" : 64
    	},
    	"registered" : {
    		"date" : "2010-03-07T16:57:22Z",
    		"age" : 8
    	},
    	"phone" : "06-484-237",
    	"cell" : "047-504-75-70",
    	"id" : {
    		"name" : "HETU",
    		"value" : "NaNNA423undefined"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/men/90.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/men/90.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/men/90.jpg"
    	},
    	"nat" : "FI"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe6f1"),
    	"gender" : "female",
    	"name" : {
    		"title" : "mrs",
    		"first" : "anaya",
    		"last" : "sangolt"
    	},
    	"location" : {
    		"street" : "oluf onsums vei 3798",
    		"city" : "elverum",
    		"state" : "oslo",
    		"postcode" : "5228",
    		"coordinates" : {
    			"latitude" : "-43.9431",
    			"longitude" : "-62.8704"
    		},
    		"timezone" : {
    			"offset" : "+10:00",
    			"description" : "Eastern Australia, Guam, Vladivostok"
    		}
    	},
    	"email" : "anaya.sangolt@example.com",
    	"login" : {
    		"uuid" : "25ee1329-8199-4f98-b502-cc9bd08ff68d",
    		"username" : "angrypanda603",
    		"password" : "expert",
    		"salt" : "LTnKy0B0",
    		"md5" : "6180c7a97a0511bad9d4109d8d97db6a",
    		"sha1" : "3f8af5c93cf5cb41803cbe941f9e0f606eeb9577",
    		"sha256" : "79fdb76f8cd50bb64b181e70b5470d898abf4e4bf10980ba84c9d22888442d75"
    	},
    	"dob" : {
    		"date" : "1957-02-09T02:45:42Z",
    		"age" : 61
    	},
    	"registered" : {
    		"date" : "2015-02-26T12:41:18Z",
    		"age" : 3
    	},
    	"phone" : "64808750",
    	"cell" : "92457213",
    	"id" : {
    		"name" : "FN",
    		"value" : "09025740707"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/84.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/84.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/84.jpg"
    	},
    	"nat" : "NO"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe6f2"),
    	"gender" : "male",
    	"name" : {
    		"title" : "mr",
    		"first" : "nicklas",
    		"last" : "sørensen"
    	},
    	"location" : {
    		"street" : "1605 hobrovej",
    		"city" : "bælum",
    		"state" : "hovedstaden",
    		"postcode" : 80673,
    		"coordinates" : {
    			"latitude" : "42.1130",
    			"longitude" : "-98.0795"
    		},
    		"timezone" : {
    			"offset" : "-3:30",
    			"description" : "Newfoundland"
    		}
    	},
    	"email" : "nicklas.sørensen@example.com",
    	"login" : {
    		"uuid" : "709394ed-53d1-440b-8c48-fa5266cd7f03",
    		"username" : "sadtiger625",
    		"password" : "1945",
    		"salt" : "pNv8JeeQ",
    		"md5" : "fd4570facdcf9b3875d15d3bda0ba47c",
    		"sha1" : "c40d710cde3254113b2b8a31a2b281b46adf6a23",
    		"sha256" : "6be9c1df666445692c41b688afc8a19f77cfa517a1cffb963bb007fd318afd72"
    	},
    	"dob" : {
    		"date" : "1955-09-14T19:31:39Z",
    		"age" : 62
    	},
    	"registered" : {
    		"date" : "2009-09-29T10:07:04Z",
    		"age" : 8
    	},
    	"phone" : "63595350",
    	"cell" : "10534596",
    	"id" : {
    		"name" : "CPR",
    		"value" : "517431-3080"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/men/47.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/men/47.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/men/47.jpg"
    	},
    	"nat" : "DK"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe6f5"),
    	"gender" : "male",
    	"name" : {
    		"title" : "mr",
    		"first" : "mathias",
    		"last" : "sørensen"
    	},
    	"location" : {
    		"street" : "5362 egebjergvej",
    		"city" : "bælum",
    		"state" : "syddanmark",
    		"postcode" : 13205,
    		"coordinates" : {
    			"latitude" : "20.0541",
    			"longitude" : "97.0354"
    		},
    		"timezone" : {
    			"offset" : "+5:00",
    			"description" : "Ekaterinburg, Islamabad, Karachi, Tashkent"
    		}
    	},
    	"email" : "mathias.sørensen@example.com",
    	"login" : {
    		"uuid" : "fbefdfde-1214-4d2f-a439-7178e0bf9b00",
    		"username" : "smallduck705",
    		"password" : "twelve",
    		"salt" : "MXHBaNmT",
    		"md5" : "446e6cc7b84b6074f90209475623bf27",
    		"sha1" : "87be1829460454afecf246dd0bb8aed3ca1b2616",
    		"sha256" : "f43e3db16504a319ff6777c486061246071e3cda802a3973ae4288fe6b690600"
    	},
    	"dob" : {
    		"date" : "1949-01-16T22:29:04Z",
    		"age" : 69
    	},
    	"registered" : {
    		"date" : "2009-02-14T03:29:37Z",
    		"age" : 9
    	},
    	"phone" : "52827060",
    	"cell" : "43207994",
    	"id" : {
    		"name" : "CPR",
    		"value" : "056794-1171"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/men/6.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/men/6.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/men/6.jpg"
    	},
    	"nat" : "DK"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe6f8"),
    	"gender" : "male",
    	"name" : {
    		"title" : "mr",
    		"first" : "adrian",
    		"last" : "fowler"
    	},
    	"location" : {
    		"street" : "464 paddock way",
    		"city" : "mckinney",
    		"state" : "arkansas",
    		"postcode" : 96516,
    		"coordinates" : {
    			"latitude" : "-8.1114",
    			"longitude" : "-122.1404"
    		},
    		"timezone" : {
    			"offset" : "-3:00",
    			"description" : "Brazil, Buenos Aires, Georgetown"
    		}
    	},
    	"email" : "adrian.fowler@example.com",
    	"login" : {
    		"uuid" : "7bd604e6-3514-4e90-9e51-ac9d91011e8b",
    		"username" : "happykoala413",
    		"password" : "style",
    		"salt" : "5nuBIntI",
    		"md5" : "270bc63dd7bd31333a61aa2c84ca954b",
    		"sha1" : "20093fa7eb42d2078bccde278b14e6179fce9132",
    		"sha256" : "7f0e507286732b09e037569fa296fe6bad9cc15cc0824aeebde1fbaeabea1bb0"
    	},
    	"dob" : {
    		"date" : "1951-02-08T01:01:01Z",
    		"age" : 67
    	},
    	"registered" : {
    		"date" : "2014-11-26T01:38:11Z",
    		"age" : 3
    	},
    	"phone" : "(074)-196-1098",
    	"cell" : "(212)-963-8756",
    	"id" : {
    		"name" : "SSN",
    		"value" : "865-93-7479"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/men/14.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/men/14.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/men/14.jpg"
    	},
    	"nat" : "US"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe704"),
    	"gender" : "female",
    	"name" : {
    		"title" : "ms",
    		"first" : "kelya",
    		"last" : "philippe"
    	},
    	"location" : {
    		"street" : "3688 quai chauveau",
    		"city" : "avignon",
    		"state" : "aisne",
    		"postcode" : 47002,
    		"coordinates" : {
    			"latitude" : "2.4082",
    			"longitude" : "153.9632"
    		},
    		"timezone" : {
    			"offset" : "+4:00",
    			"description" : "Abu Dhabi, Muscat, Baku, Tbilisi"
    		}
    	},
    	"email" : "kelya.philippe@example.com",
    	"login" : {
    		"uuid" : "511bf094-e04a-415b-aa38-80bf8f34e06b",
    		"username" : "purplebird756",
    		"password" : "chichi",
    		"salt" : "UWpBe3rC",
    		"md5" : "b98a85e6d3174c702cd8519b152ec8c2",
    		"sha1" : "bf8f0d11a1cbc07bd166098bd72ce57210c91686",
    		"sha256" : "a7a3aae9af098534cab95b10ad67a1e7594e61fe58062d236f1ad7c76acc189c"
    	},
    	"dob" : {
    		"date" : "1950-08-05T15:04:26Z",
    		"age" : 68
    	},
    	"registered" : {
    		"date" : "2016-07-08T13:22:38Z",
    		"age" : 2
    	},
    	"phone" : "01-92-61-93-34",
    	"cell" : "06-06-75-63-58",
    	"id" : {
    		"name" : "INSEE",
    		"value" : "2NNaN90767214 43"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/20.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/20.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/20.jpg"
    	},
    	"nat" : "FR"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe707"),
    	"gender" : "female",
    	"name" : {
    		"title" : "ms",
    		"first" : "thaïs",
    		"last" : "bertrand"
    	},
    	"location" : {
    		"street" : "1758 place de l'abbé-basset",
    		"city" : "saint-denis",
    		"state" : "aisne",
    		"postcode" : 74709,
    		"coordinates" : {
    			"latitude" : "-16.5203",
    			"longitude" : "-83.0052"
    		},
    		"timezone" : {
    			"offset" : "+9:30",
    			"description" : "Adelaide, Darwin"
    		}
    	},
    	"email" : "thaïs.bertrand@example.com",
    	"login" : {
    		"uuid" : "9bc6f5a4-e795-4b78-901b-63326a15f9fa",
    		"username" : "biggorilla218",
    		"password" : "clowns",
    		"salt" : "8voY9kyp",
    		"md5" : "c96f7ee9891d31d4306b771b0fa30fcd",
    		"sha1" : "b734887c1221fa11994d76a9fe6d3e68b17ec6c2",
    		"sha256" : "e200355fc00257f7b095650ac40fe12c7be54bedc2b22a1615f153a4e751de0a"
    	},
    	"dob" : {
    		"date" : "1956-11-24T08:35:17Z",
    		"age" : 61
    	},
    	"registered" : {
    		"date" : "2016-09-17T19:24:28Z",
    		"age" : 1
    	},
    	"phone" : "05-86-91-79-08",
    	"cell" : "06-55-69-88-07",
    	"id" : {
    		"name" : "INSEE",
    		"value" : "2NNaN43827612 27"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/77.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/77.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/77.jpg"
    	},
    	"nat" : "FR"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe709"),
    	"gender" : "female",
    	"name" : {
    		"title" : "miss",
    		"first" : "eline",
    		"last" : "dupont"
    	},
    	"location" : {
    		"street" : "7383 rue saint-georges",
    		"city" : "amiens",
    		"state" : "hauts-de-seine",
    		"postcode" : 28598,
    		"coordinates" : {
    			"latitude" : "-25.0642",
    			"longitude" : "-96.7924"
    		},
    		"timezone" : {
    			"offset" : "+7:00",
    			"description" : "Bangkok, Hanoi, Jakarta"
    		}
    	},
    	"email" : "eline.dupont@example.com",
    	"login" : {
    		"uuid" : "2af7be85-99ef-4f47-b93b-fdb2e21f1e46",
    		"username" : "smallsnake535",
    		"password" : "nana",
    		"salt" : "olh0oYLJ",
    		"md5" : "0858a203f9ae9f7dc6127e207a45352a",
    		"sha1" : "0e24b4e342a4dc4b0bf8d8f68dfae8e267cb5c30",
    		"sha256" : "4633638e6f20c81c59e68df0d2d8e91a5e9dd58c34596cf9757089d6c1dcb016"
    	},
    	"dob" : {
    		"date" : "1949-11-07T21:23:10Z",
    		"age" : 68
    	},
    	"registered" : {
    		"date" : "2003-07-08T07:24:55Z",
    		"age" : 15
    	},
    	"phone" : "02-79-81-50-69",
    	"cell" : "06-69-00-52-24",
    	"id" : {
    		"name" : "INSEE",
    		"value" : "2NNaN10525095 32"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/46.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/46.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/46.jpg"
    	},
    	"nat" : "FR"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe70b"),
    	"gender" : "male",
    	"name" : {
    		"title" : "mr",
    		"first" : "larry",
    		"last" : "johnson"
    	},
    	"location" : {
    		"street" : "6979 bruce st",
    		"city" : "baltimore",
    		"state" : "georgia",
    		"postcode" : 12056,
    		"coordinates" : {
    			"latitude" : "11.6793",
    			"longitude" : "-132.8939"
    		},
    		"timezone" : {
    			"offset" : "-2:00",
    			"description" : "Mid-Atlantic"
    		}
    	},
    	"email" : "larry.johnson@example.com",
    	"login" : {
    		"uuid" : "7bf0acde-e341-4d0d-a363-fdef780bf9b0",
    		"username" : "silverzebra945",
    		"password" : "1950",
    		"salt" : "foM4IIBQ",
    		"md5" : "c8e14075894554f8b0b13b4aa8134698",
    		"sha1" : "316e9f642e954573f34367a13de3b4e67fe05d84",
    		"sha256" : "61492f37eb55beac254f318ddb4ac58d622e0807e622ec9ff1442d0f54028d48"
    	},
    	"dob" : {
    		"date" : "1954-03-14T16:37:25Z",
    		"age" : 64
    	},
    	"registered" : {
    		"date" : "2010-11-22T05:06:33Z",
    		"age" : 7
    	},
    	"phone" : "(964)-129-3195",
    	"cell" : "(829)-410-6409",
    	"id" : {
    		"name" : "SSN",
    		"value" : "828-99-1439"
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/men/90.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/men/90.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/men/90.jpg"
    	},
    	"nat" : "US"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe70f"),
    	"gender" : "female",
    	"name" : {
    		"title" : "mrs",
    		"first" : "lea",
    		"last" : "margaret"
    	},
    	"location" : {
    		"street" : "6686 3rd st",
    		"city" : "grand falls",
    		"state" : "yukon",
    		"postcode" : "U3N 8B7",
    		"coordinates" : {
    			"latitude" : "40.0719",
    			"longitude" : "21.3568"
    		},
    		"timezone" : {
    			"offset" : "-8:00",
    			"description" : "Pacific Time (US & Canada)"
    		}
    	},
    	"email" : "lea.margaret@example.com",
    	"login" : {
    		"uuid" : "3cda7d0d-c80c-4378-8325-4268be540d41",
    		"username" : "lazywolf219",
    		"password" : "critter",
    		"salt" : "Ck0aaFNY",
    		"md5" : "bf8c5cee4ca4a63802887119b1122007",
    		"sha1" : "fbd3998de447f88d0b08c315d391957f15a59af4",
    		"sha256" : "df5d27621e2b03951b1e41e5b75e79a0aa4ab48e480ff762819cba0a131de2d9"
    	},
    	"dob" : {
    		"date" : "1947-01-20T21:07:58Z",
    		"age" : 71
    	},
    	"registered" : {
    		"date" : "2007-09-27T13:27:21Z",
    		"age" : 10
    	},
    	"phone" : "327-903-0126",
    	"cell" : "442-382-8891",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/women/64.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/women/64.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/women/64.jpg"
    	},
    	"nat" : "CA"
    }
    {
    	"_id" : ObjectId("632e94b79d41938c381fe711"),
    	"gender" : "male",
    	"name" : {
    		"title" : "mr",
    		"first" : "konstantin",
    		"last" : "stoffel"
    	},
    	"location" : {
    		"street" : "tannenweg 166",
    		"city" : "zeitz",
    		"state" : "nordrhein-westfalen",
    		"postcode" : 93291,
    		"coordinates" : {
    			"latitude" : "-25.1841",
    			"longitude" : "-2.7274"
    		},
    		"timezone" : {
    			"offset" : "+5:30",
    			"description" : "Bombay, Calcutta, Madras, New Delhi"
    		}
    	},
    	"email" : "konstantin.stoffel@example.com",
    	"login" : {
    		"uuid" : "8eefbdbb-7188-44ba-b9e6-9cc5fb81c9d1",
    		"username" : "biggorilla448",
    		"password" : "pinnacle",
    		"salt" : "Hkx7yfnV",
    		"md5" : "3d9ed2ce4d21f918d61600237a845824",
    		"sha1" : "2f7c182ff53f0aa2da6fbfa0149f8fffb0829a9b",
    		"sha256" : "c164f01c02a8affc1dc6bc401a36325fb0d5d3159ea3c6fbfd8e4fea40213718"
    	},
    	"dob" : {
    		"date" : "1950-05-20T10:13:54Z",
    		"age" : 68
    	},
    	"registered" : {
    		"date" : "2006-08-31T13:53:42Z",
    		"age" : 12
    	},
    	"phone" : "0278-2305509",
    	"cell" : "0177-7970518",
    	"id" : {
    		"name" : "",
    		"value" : null
    	},
    	"picture" : {
    		"large" : "https://randomuser.me/api/portraits/men/1.jpg",
    		"medium" : "https://randomuser.me/api/portraits/med/men/1.jpg",
    		"thumbnail" : "https://randomuser.me/api/portraits/thumb/men/1.jpg"
    	},
    	"nat" : "DE"
    }
    Type "it" for more
    >
    ```
    

```bash
> db.contacts.find({"dob.age":{$gt: 60}}.count()
**Output**
3206
```

Analyze database with `explain()` method

```cpp
db.contacts.explain().find({'dob.age': {$gt: 60}})
```

- Output
    
    ```bash
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "contactData.contacts",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"dob.age" : {
    				"$gt" : 60
    			}
    		},
    		"winningPlan" : {
    			"stage" : "COLLSCAN",
    			"filter" : {
    				"dob.age" : {
    					"$gt" : 60
    				}
    			},
    			"direction" : "forward"
    		},
    		"rejectedPlans" : [ ]
    	},
    	"serverInfo" : {
    		"host" : "<your_computer_name>",
    		"port" : 27017,
    		"version" : "3.6.8",
    		"gitVersion" : "8e540c0b6db93ce994cc548f000900bdc740f80a"
    	},
    	"ok" : 1
    }
    ```
    

Here also have different types of plans  

1. winningPlan
2. rejectedPlans

Getting the detailed query

```cpp
db.contacts.explain("executionStats").find({'dob.age': {$gt: 60}})
```

- Output
    
    ```bash
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "contactData.contacts",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"dob.age" : {
    				"$gt" : 60
    			}
    		},
    		"winningPlan" : {
    			"stage" : "COLLSCAN",
    			"filter" : {
    				"dob.age" : {
    					"$gt" : 60
    				}
    			},
    			"direction" : "forward"
    		},
    		"rejectedPlans" : [ ]
    	},
    	"executionStats" : {
    		"executionSuccess" : true,
    		"nReturned" : 3206,
    		"executionTimeMillis" : 25,
    		"totalKeysExamined" : 0,
    		"totalDocsExamined" : 13000,
    		"executionStages" : {
    			"stage" : "COLLSCAN",
    			"filter" : {
    				"dob.age" : {
    					"$gt" : 60
    				}
    			},
    			"nReturned" : 3206,
    			"executionTimeMillisEstimate" : 10,
    			"works" : 13002,
    			"advanced" : 3206,
    			"needTime" : 9795,
    			"needYield" : 0,
    			"saveState" : 101,
    			"restoreState" : 101,
    			"isEOF" : 1,
    			"invalidates" : 0,
    			"direction" : "forward",
    			"docsExamined" : 13000
    		}
    	},
    	"serverInfo" : {
    		"host" : "jahid-HP-ProBook-6470b",
    		"port" : 27017,
    		"version" : "3.6.8",
    		"gitVersion" : "8e540c0b6db93ce994cc548f000900bdc740f80a"
    	},
    	"ok" : 1
    }
    ```
    

Creating index, an index is defined as a document. Can **create** an **index** of **top-level fields** also **embedded fields**.
Here one basically means I want to sort the data in ascending order 1 or descending order -1.

```cpp
> db.contacts.createIndex({'dob.age': 1})
**Output**
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```

If change the query then it works like before
Without index every different query ha different values

```cpp
db.contacts.explain("executionStats").find({'dob.age': {$gt: 60}})
```

- Output
    
    ```bash
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "contactData.contacts",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"dob.age" : {
    				"$gt" : 60
    			}
    		},
    		"winningPlan" : {
    			"stage" : "FETCH",
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"keyPattern" : {
    					"dob.age" : 1
    				},
    				"indexName" : "dob.age_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"dob.age" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"dob.age" : [
    						"(60.0, inf.0]"
    					]
    				}
    			}
    		},
    		"rejectedPlans" : [ ]
    	},
    	"executionStats" : {
    		"executionSuccess" : true,
    		"nReturned" : 3206,
    		"executionTimeMillis" : 12,
    		"totalKeysExamined" : 3206,
    		"totalDocsExamined" : 3206,
    		"executionStages" : {
    			"stage" : "FETCH",
    			"nReturned" : 3206,
    			"executionTimeMillisEstimate" : 0,
    			"works" : 3207,
    			"advanced" : 3206,
    			"needTime" : 0,
    			"needYield" : 0,
    			"saveState" : 25,
    			"restoreState" : 25,
    			"isEOF" : 1,
    			"invalidates" : 0,
    			"docsExamined" : 3206,
    			"alreadyHasObj" : 0,
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"nReturned" : 3206,
    				"executionTimeMillisEstimate" : 0,
    				"works" : 3207,
    				"advanced" : 3206,
    				"needTime" : 0,
    				"needYield" : 0,
    				"saveState" : 25,
    				"restoreState" : 25,
    				"isEOF" : 1,
    				"invalidates" : 0,
    				"keyPattern" : {
    					"dob.age" : 1
    				},
    				"indexName" : "dob.age_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"dob.age" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"dob.age" : [
    						"(60.0, inf.0]"
    					]
    				},
    				"keysExamined" : 3206,
    				"seeks" : 1,
    				"dupsTested" : 0,
    				"dupsDropped" : 0,
    				"seenInvalidated" : 0
    			}
    		}
    	},
    	"serverInfo" : {
    		"host" : "jahid-HP-ProBook-6470b",
    		"port" : 27017,
    		"version" : "3.6.8",
    		"gitVersion" : "8e540c0b6db93ce994cc548f000900bdc740f80a"
    	},
    	"ok" : 1
    }
    ```
    

`executionTimeMillis` was 25 before indexing after indexing it became 12.

# Dropping the index

```cpp
> db.contacts.dropIndex({'dob.age': 1})
**Output**
{ "nIndexesWas" : 2, "ok" : 1 }
```

# Understanding Index Restrictions

`{'dob.age': {$gt: 20} }` is true for all document and returns it takes 22 milliseconds for query if have an index for `dob.age` embedded document.

- When have index `"executionTimeMillis" : 22`
    
    ```bash
    > db.contacts.explain("executionStats").find({'dob.age': {$gt: 20}})
    **Output**
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "contactData.contacts",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"dob.age" : {
    				"$gt" : 20
    			}
    		},
    		"winningPlan" : {
    			"stage" : "FETCH",
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"keyPattern" : {
    					"dob.age" : 1
    				},
    				"indexName" : "dob.age_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"dob.age" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"dob.age" : [
    						"(20.0, inf.0]"
    					]
    				}
    			}
    		},
    		"rejectedPlans" : [ ]
    	},
    	"executionStats" : {
    		"executionSuccess" : true,
    		"nReturned" : 13000,
    		"executionTimeMillis" : 22,
    		"totalKeysExamined" : 13000,
    		"totalDocsExamined" : 13000,
    		"executionStages" : {
    			"stage" : "FETCH",
    			"nReturned" : 13000,
    			"executionTimeMillisEstimate" : 20,
    			"works" : 13001,
    			"advanced" : 13000,
    			"needTime" : 0,
    			"needYield" : 0,
    			"saveState" : 101,
    			"restoreState" : 101,
    			"isEOF" : 1,
    			"invalidates" : 0,
    			"docsExamined" : 13000,
    			"alreadyHasObj" : 0,
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"nReturned" : 13000,
    				"executionTimeMillisEstimate" : 20,
    				"works" : 13001,
    				"advanced" : 13000,
    				"needTime" : 0,
    				"needYield" : 0,
    				"saveState" : 101,
    				"restoreState" : 101,
    				"isEOF" : 1,
    				"invalidates" : 0,
    				"keyPattern" : {
    					"dob.age" : 1
    				},
    				"indexName" : "dob.age_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"dob.age" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"dob.age" : [
    						"(20.0, inf.0]"
    					]
    				},
    				"keysExamined" : 13000,
    				"seeks" : 1,
    				"dupsTested" : 0,
    				"dupsDropped" : 0,
    				"seenInvalidated" : 0
    			}
    		}
    	},
    	"serverInfo" : {
    		"host" : "jahid-HP-ProBook-6470b",
    		"port" : 27017,
    		"version" : "3.6.8",
    		"gitVersion" : "8e540c0b6db93ce994cc548f000900bdc740f80a"
    	},
    	"ok" : 1
    }
    ```
    
- After deleting index `"executionTimeMillis" : 16`
    
    ```bash
    > db.contacts.dropIndex({"dob.age": 1 })
    **Output**
    { "nIndexesWas" : 2, "ok" : 1 }
    
    > db.contacts.explain("executionStats").find({'dob.age': {$gt: 20}})
    **Output**
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "contactData.contacts",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"dob.age" : {
    				"$gt" : 20
    			}
    		},
    		"winningPlan" : {
    			"stage" : "COLLSCAN",
    			"filter" : {
    				"dob.age" : {
    					"$gt" : 20
    				}
    			},
    			"direction" : "forward"
    		},
    		"rejectedPlans" : [ ]
    	},
    	"executionStats" : {
    		"executionSuccess" : true,
    		"nReturned" : 13000,
    		"executionTimeMillis" : 16,
    		"totalKeysExamined" : 0,
    		"totalDocsExamined" : 13000,
    		"executionStages" : {
    			"stage" : "COLLSCAN",
    			"filter" : {
    				"dob.age" : {
    					"$gt" : 20
    				}
    			},
    			"nReturned" : 13000,
    			"executionTimeMillisEstimate" : 10,
    			"works" : 13002,
    			"advanced" : 13000,
    			"needTime" : 1,
    			"needYield" : 0,
    			"saveState" : 101,
    			"restoreState" : 101,
    			"isEOF" : 1,
    			"invalidates" : 0,
    			"direction" : "forward",
    			"docsExamined" : 13000
    		}
    	},
    	"serverInfo" : {
    		"host" : "jahid-HP-ProBook-6470b",
    		"port" : 27017,
    		"version" : "3.6.8",
    		"gitVersion" : "8e540c0b6db93ce994cc548f000900bdc740f80a"
    	},
    	"ok" : 1
    }
    ```
    

If we have to **retrieve** a **large number/majority of documents** nearly 70 to 80% of the whole document then the **index** can affect the query to be **slower**, cause for using a query we have to add an extra step. 

For retrieving **20-30%** or **lower** can use the index, the query is **faster**.

> My understanding(not 100% sure)
As it first gets a document pointer/address of the document and then it checks that document with the given condition. Then if there is no index then it can fetch/get the document directly.
> 

[Compound index with text](Index_and_Others/Compound_Index_with_Text.md)

[Using indexes for sorting](Index_and_Others/Using_Indexes_for_Sorting.md)

[Configuring Indexes](Index_and_Others/Configuring_Indexes.md)

[Time-To-Live(TTL) index](Index_and_Others/Time-To-Live(TTL)_Index.md)

[Query Diagnosis and Query Planning](Index_and_Others/Query_Diagnosis_and_Query_Planning.md)

[How MongoDB rejects a plan](Index_and_Others/How_MongoDB_rejects_a_plan.md)

[Multi Key Indexes](Index_and_Others/Multi_Key_Indexes.md)

[Text indexes(Special Type of Multi-Key index), Sorting using Text indexes, Combine Text index, Exclude while find in Text-Index](Index_and_Others/Text_Indexes(Special_Type_of_Multi-Key_index),_Sor.md)

# Setting the Default Language Using Weights

Initially was. Stop word(will be ignored) for English “is”, “a”. Language determined how the word will be stemmed,  and how prefixes will be removed.

```bash
> db.products.getIndexes()
[
	{
		"v" : 2,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_",
		"ns" : "test.products"
	},
	{
		"v" : 2,
		"key" : {
			"_fts" : "text",
			"_ftsx" : 1
		},
		"name" : "title_text_description_text",
		"ns" : "test.products",
		"weights" : {
			"description" : 1,
			"title" : 1
		},
		**"default_language" : "english",**
		"language_override" : "language",
		"textIndexVersion" : 3
	}
]
```

First dropping the previous text index

```cpp
> db.products.dropIndex('title_text_description_text')
**Output**
{ "nIndexesWas" : 2, "ok" : 1 }
```

Pass some config. We can set some preselected languages, not any language. 

```cpp
> db.products.createIndex(
		{title:'text',description: 'text'},
		{default_language: 'german', weights: {title: 1, description: 10}}
)
**Output**
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```

Can also work without weights, but without weights score value can be changed.

```cpp
> db.products.createIndex({title:'text',description: 'text'},{default_language: 'english'})
> db.products.find({$text: {$search: '', $language: 'german'}}).pretty()
```

`caseSensitive` default is false

```cpp
db.products.find({$text: {$search: '', $caseSensitive: true}}).pretty()
db.products.createIndex({title:'text',description: 'text'},{default_language: 'english', weights: {title: 1, description: 10}})
```

Execute query

```cpp
> db.products.find({$text: {$search: 'red',}}).pretty()
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a4"),
	"title" : "Red T-Shirt",
	"description" : "This T-Shirt is red and it is pretty awesome"
}

> db.products.find({$text: {$search: 'red'}},{score: {$meta: 'textScore'}}).pretty()
{
	"_id" : ObjectId("5f2adb2fbcaaeedce48e55a4"),
	"title" : "Red T-Shirt",
	"description" : "This T-Shirt is red and it is pretty awesome",
	"score" : 6.666666666666667
}
```

# Building Indexes

1. Foreground:
a) Collection is locked during index creation.
b) Faster
2. Background
a) Collection is accessible during index creation. ⇒ Used in production as do not lock database as doing so the application will get stack.
b) Slower

In the previous, discussed Foreground index(basically access from core db)
Now create an index that basically a Background index
First, discuss why the Background index needs

Adding 1000000 documents by running the given js file in the course.

```bash
mongo credit-rating.js
```

```cpp
> use credit
switched to db credit

> show collections
ratings
> db.ratings.find().count()
1000000
```

```cpp
db.ratings.findOne()
**Output**
{
	"_id" : ObjectId("6330962499f2d1746027d80d"),
	"person_id" : 1,
	"score" : 74.32324129715587,
	"age" : 21
}
```

Create an index with the age
Here time is important cause documents size 100000.

```cpp
db.ratings.createIndex({age: 1})
**Output**
{
	"createdCollectionAutomatically" : false,
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"ok" : 1
}
```

When creating an index into large-scale documents or even complex documents, the db or documents are locked for a few seconds or a couple of minutes.
Especially text indexes also need more time
So this is not an alternative production database

After creating an index if want to insert a new document into a large-scale document then it also takes a long time.

```cpp
db.ratings.insertOne({person_id: 'a39djd', score: 55.2211, age: 90})
**Output**
{
	"acknowledged" : true,
	"insertedId" : ObjectId("633098d70ef94fe2e10b55ba")
}
```

Let examine the query

```cpp
db.ratings.explain('executionStats').find({age: {$gt: 80}})
```

- Output `"executionTimeMillis" : 218`
    
    ```bash
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "credit.ratings",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"age" : {
    				"$gt" : 80
    			}
    		},
    		"winningPlan" : {
    			"stage" : "FETCH",
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"keyPattern" : {
    					"age" : 1
    				},
    				"indexName" : "age_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"age" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"age" : [
    						"(80.0, inf.0]"
    					]
    				}
    			}
    		},
    		"rejectedPlans" : [ ]
    	},
    	"executionStats" : {
    		"executionSuccess" : true,
    		"nReturned" : 99991,
    		"executionTimeMillis" : 218,
    		"totalKeysExamined" : 99991,
    		"totalDocsExamined" : 99991,
    		"executionStages" : {
    			"stage" : "FETCH",
    			"nReturned" : 99991,
    			"executionTimeMillisEstimate" : 180,
    			"works" : 99992,
    			"advanced" : 99991,
    			"needTime" : 0,
    			"needYield" : 0,
    			"saveState" : 781,
    			"restoreState" : 781,
    			"isEOF" : 1,
    			"invalidates" : 0,
    			"docsExamined" : 99991,
    			"alreadyHasObj" : 0,
    			"inputStage" : {
    				"stage" : "IXSCAN",
    				"nReturned" : 99991,
    				"executionTimeMillisEstimate" : 100,
    				"works" : 99992,
    				"advanced" : 99991,
    				"needTime" : 0,
    				"needYield" : 0,
    				"saveState" : 781,
    				"restoreState" : 781,
    				"isEOF" : 1,
    				"invalidates" : 0,
    				"keyPattern" : {
    					"age" : 1
    				},
    				"indexName" : "age_1",
    				"isMultiKey" : false,
    				"multiKeyPaths" : {
    					"age" : [ ]
    				},
    				"isUnique" : false,
    				"isSparse" : false,
    				"isPartial" : false,
    				"indexVersion" : 2,
    				"direction" : "forward",
    				"indexBounds" : {
    					"age" : [
    						"(80.0, inf.0]"
    					]
    				},
    				"keysExamined" : 99991,
    				"seeks" : 1,
    				"dupsTested" : 0,
    				"dupsDropped" : 0,
    				"seenInvalidated" : 0
    			}
    		}
    	},
    	"serverInfo" : {
    		"host" : "jahid-HP-ProBook-6470b",
    		"port" : 27017,
    		"version" : "3.6.8",
    		"gitVersion" : "8e540c0b6db93ce994cc548f000900bdc740f80a"
    	},
    	"ok" : 1
    }
    ```
    

Let's drop the index

```cpp
db.ratings.dropIndex({age: 1})
**Output**
{ "nIndexesWas" : 2, "ok" : 1 }
```

Let execute the previous query

```cpp
db.ratings.explain('executionStats').find({age: {$gt: 80}})
"executionTimeMillis" : 367
```

- Output `"executionTimeMillis" : 379`
    
    ```bash
    {
    	"queryPlanner" : {
    		"plannerVersion" : 1,
    		"namespace" : "credit.ratings",
    		"indexFilterSet" : false,
    		"parsedQuery" : {
    			"age" : {
    				"$gt" : 80
    			}
    		},
    		"winningPlan" : {
    			"stage" : "COLLSCAN",
    			"filter" : {
    				"age" : {
    					"$gt" : 80
    				}
    			},
    			"direction" : "forward"
    		},
    		"rejectedPlans" : [ ]
    	},
    	"executionStats" : {
    		"executionSuccess" : true,
    		"nReturned" : 99991,
    		"executionTimeMillis" : 379,
    		"totalKeysExamined" : 0,
    		"totalDocsExamined" : 1000001,
    		"executionStages" : {
    			"stage" : "COLLSCAN",
    			"filter" : {
    				"age" : {
    					"$gt" : 80
    				}
    			},
    			"nReturned" : 99991,
    			"executionTimeMillisEstimate" : 300,
    			"works" : 1000003,
    			"advanced" : 99991,
    			"needTime" : 900011,
    			"needYield" : 0,
    			"saveState" : 7812,
    			"restoreState" : 7812,
    			"isEOF" : 1,
    			"invalidates" : 0,
    			"direction" : "forward",
    			"docsExamined" : 1000001
    		}
    	},
    	"serverInfo" : {
    		"host" : "jahid-HP-ProBook-6470b",
    		"port" : 27017,
    		"version" : "3.6.8",
    		"gitVersion" : "8e540c0b6db93ce994cc548f000900bdc740f80a"
    	},
    	"ok" : 1
    }
    ```
    

Let's create a Background index
In Background index, it takes a second argument
background default is false
So we have to set the background to true
and it's created immediately

```cpp
db.ratings.createIndex({age: 1}, {background: true})
```

It happened in the background without locking the collection