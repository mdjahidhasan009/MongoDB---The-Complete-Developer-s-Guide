# Security

**Not enough taught in this course and not all commands worked on my machine.**

# Security Check List

1. Authentication and Authorization
2. Transport Encryption  
    1. Data sending from my app to server should be encrypted. 
3. Encryption at Rest
    1. Data in the database also should be encrypted as if anyone get the data could not read those.
4. Auditing
5. Server & Network Config and Setup
6. Backups & Software Updates

> First 3 are important checklist in teams of security
> 

# Authentication & Authorization

Authentication : Identifies valid users of the database. Such as you are employee therefore may access the office.
Authorization : Identifies what these users may actually do in the database. Such as you are employed as an account and therefore may access the office and process orders.

# Role Based Access Control

## MongoDB Server

(Auth Enabled)

Shop database :
    1. Products Collection
    2. Customers Collection
Blog database
    1. Posts Collection
    2. Authors Collection
Admin database

Why Rules
**Different Types of Database users**

Administrator

1. Needs to be able to manage the database config, create users etc.
2. Does not need to be able to insert or fetch data.

Developers / Your App

1. Needs to be able to insert, Updates, delete or fetch data(CRUD).
2. Does not need to be able to create users or manage the database config.

Data Scientist

1. Needs to be able to fetch data.
2. Does not need to be able to create users, manage the database config or insert, edit or delete data.

# Creating $ Editing Users

createUser(), UpdateUser() --> ('name': 'Max') --> (Roles and Privileges) --> Database (e.g admin) --> Access is not limited to authentication database

To enable authentication for MongoDB database.

> After running this command we will able to access the database meaning MongoDB will ask for a username and password for the first time as at the first time we do not add a username and password yet and have to add those. After adding those will ask for a username and password every time while accessing db.
> 

```bash
sudo mongod --auth
```

This does not work

```cpp
mongo -u max -p 123456
```

Creating a user. This is a granted user

```cpp
> use admin
switched to db admin

> db.createUser( { user: 'Jahid', pwd: 'Jahid', roles: ['userAdminAnyDatabase'] } )
**Output**
{ ok: 1 }
```

Let authenticate as an admin

```cpp
> db.auth('Jahid', 'Jahid')
**Output**
{ ok: 1 }
```

# Built-in-Roles

1. Database User 
    1.  read 
    2. readWrite
2. Database Admin 
    1.  dbAdmin 
    2. userAdmin
    3. dbOwner
3. All Database Roles 
    1.  readAnyDatabase
    2.  readWriteAnyDatabase
    3.  userAdminAnyDatabase
    4.  dbAdminAnyDatabase
4. Cluster Admin 
    1.  clusterManager 
    2. clusterMonitor 
    3. hostManager 
    4. clusterAdmin
5. Backup/Restore  
    1. backup
    2.  restore
6. Superuser  
    1. dbOwner (admin)
    2. userAdmin(admin)
    3. userAdminAnyDatabase
    4. root

// to logout

# Assigning Roles to Users Database

Let's go to the database

```cpp
mongo --help
```

Adding user for a particular database 

```cpp
mongo -u Jahid -p jahid123 --authenticationDatabase admin
// now logged in
```

Assigning roles to the sub-admin(access permission to any single database)

```cpp
> use shop
switched to db shop

db.createUser({user: 'jahid', pwd: 'jahid123', roles: ['readWrite']})
```

Let authenticate the user

```cpp
mongo -u jahid -p jahid123 --authenticationDatabase shop
```

```cpp
> use shop
db.products.insertOne({name: 'book'})
{
"acknowledged" : true,
"insertedId" : ObjectId("5f3f747a8f6d2a27804cd909")
}
```

# Updating Extending Roles to Other Database

Every time when try to log in then have to switch the db

Here basically readWrite replace

```cpp
db.updateUser('jahid', {roles: ['readWrite', {role: 'readWrite', db: 'blog'}]})
```

So first have to be an admin

```cpp
> db.logout()

> use admin
switched to db admin

db.auth('jahid', 'jahid123')
```

```cpp
> use shop
db.updateUser('jahid', {roles: ['readWrite', {role: 'readWrite', db: 'blog'}]})
```

Can see which database permission have

```cpp
db.getUser('jahid')
```

```cpp
> db.getUser('jahid')
{
	"_id" : "shop.Mijanur1",
	"userId" : UUID("b0abc10a-dba1-4609-aec7-f9714c046505"),
	"user" : "Mijanur1",
	"db" : "shop",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "shop"
		},
		{
			"role" : "readWrite",
			"db" : "blog"
		}
	],
	"mechanisms" : [
		"SCRAM-SHA-1",
		"SCRAM-SHA-256"
	]
}
```

Now first login the database

```cpp
mongo -u jahid -p jahid123 --authenticationDatabase shop
```

```cpp
> use blog
switched to db blog
> db.posts.insertOne({title: 'This works'})
{
	"acknowledged" : true,
	"insertedId" : ObjectId("5f3f7ad50d7fd790369f97ea")
}
```

# Transport Encryption

Client(app <=> MongoDb Driver --- Encrypted--)--> MongoDb Server

In this part have an issue for the MongoDb version 4.0 with my tutorial

There are couple of commands

1. openssl genrsa -out mongodb-test-ca.key 4096
2. openssl req -new -x509 -days 1826 -key mongodb-test-ca.key -out mongodb-test-ca.crt -config openssl-test-ca.cnf
3. openssl genrsa -out mongodb-test-ia.key 4096
sudo mongod --tlsMode requireTLS --tlsCertificateKeyFile test-ca.pem
4. openssl x509 -sha256 -req -days 730 -in mongodb-test-ia.csr -CA mongodb-test-ca.crt -CAkey mongodb-test-ca.key -set_serial 01 -out mongodb-test-ia.crt -extfile openssl-test-ca.cnf -extensions v3_ca
5. cat mongodb-test-ca.crt mongodb-test-ia.crt > test-ca.pem

To execute the database(command does not work properly)
sudo mongod --tlsMode requireTLS --tlsCertificateKeyFile test-ca.pem
mongo --tls --tlsCAFile test-ca.pem --host localhost
-------------------Encryption at Rest-------------------

Can encrypt full storage. this is for enterprise MongoDb

Common Name -->  have to use web server address but if work into a localhost just write localhost

or getting an error

>> Storage << (Encrypted)

```cpp
{
  email: 'test1@test.com',
  password: 'ad50rr4fdf84bfsdbsd44' <--- Encrypted/Hashed
}

{
  email: 'test2@test.com',
  password: 'a5ymb7fdf47fbryfvsfff'
}
```

# Users & Roles

1. MongoDb uses a Role Based Access Control approach.
2. You create users on database and then log in with credentials(against those database).
3. Users have no rights by default, need to add roles to allow certain operations.
4. Permission that are granted by roles ('Privileges') are only granted for the database the user was added to unless explicitly grant access to other database.
5. Can use 'AnyDatabase' roles for cross-database access.

# Encryption

1. Can encrypt data during transportation and at rest.
2. During transportation, can use TLS/SSL to encrypt data.
3. For production, should use SSL certificates issues by a certificate authority (NOT self-signed certificates)
4. For encryption at rest, can encrypt both the files that holdes data (made simple with "MongoDb Enterprise") and the values inside documents.