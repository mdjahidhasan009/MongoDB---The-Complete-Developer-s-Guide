# Schema & Relations

# Relations
| Relation Type | Example                                                                                                                                                                                                                     
| --- |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| One-to-one relationship | **Patient:** Summary of diseases <br/>  <br/>  **Person:**  <=> Car (One car can own by only one person)  <br/>  <br/> Can be done like embedded documents or two in different collections and referencing each other by id. |
| One to many relationship | Question and Answer(Embedded works with no problem). <br/><br/> Book and Author(Reference should use as author data maybe change). <br/><br/> City and Citizens(Two collection with reference will work better).            |
| Many to many relationship | **Customers:** Products(Orders). One customer has many products(via orders), a product belongs to many customers.  <br/><br/> **Book:** Author(A book can be written by multiple author).                                |

<details>
  <summary>Initializing two collections for join</summary>
  Creating authors collection

```scheme
db.authors.insertMany(
  [
    {name: 'Max Scwarz',age: 29, address:{street: 'Main'}},
    {name: 'Manuel Lor',age: 30, address:{street: 'Tree'}}
  ]
)
```
**Output**
```scheme
{
  "acknowledged" : true,
  "insertedIds" : [
      ObjectId("5f145a7c231893e15e9e53fe"),
      ObjectId("5f145a7c231893e15e9e53ff")
  ]
}
```    
<br/>

```scheme
    > db.authors.find().pretty()
```
**Output**

```scheme
    {
    	"_id" : ObjectId("5f145a7c231893e15e9e53fe"),
    	"name" : "Max Scwarz",
    	"age" : 29,
    	"address" : {
    		"street" : "Main"
    	}
    }
    {
    	"_id" : ObjectId("5f145a7c231893e15e9e53ff"),
    	"name" : "Manuel Lor",
    	"age" : 30,
    	"address" : {
    		"street" : "Tree"
    	}
    }
  ```
    
  Creating books collection
    
```scheme
db.books.insertOne(
  {
    name: 'My favorite Book',
    authors:
      [
        ObjectId("5f145a7c231893e15e9e53fe"),
        ObjectId("5f145a7c231893e15e9e53ff")
      ]
  }
)
```
**Output**

```scheme
    {
    	"acknowledged" : true,
    	"insertedId" : ObjectId("5f145b5f231893e15e9e5400")
    }
```
<br/>

```scheme
db.books.find().pretty()
```
  **Output**
```scheme
{
  "_id" : ObjectId("5f145b5f231893e15e9e5400"),
  "name" : "My favorite Book",
  "authors" : [
    ObjectId("5f145a7c231893e15e9e53fe"),
    ObjectId("5f145a7c231893e15e9e53ff")
  ]
}
  ```
</details>
    

# Lookup function

| from | Collection name(From which collection/foreign collection) |
| --- | --- |
| localField | Current documents key in which the _id's(reference for other collection) of other collection are stored. |
| foreignField | Field we are targeting in foreign collection. |
| as | Will be created this key automatically(by the given name) with the targeted document from another(targeted) collection. |

```scheme
> db.books.aggregate([
  {
    $lookup:
      {
        from: 'authors',
        localField:'authors',
        foreignField:"_id", 
        as:'creators'
  }
}
]).pretty()
```
**Output**
```scheme
{
  "_id" : ObjectId("5f145b5f231893e15e9e5400"),
  "name" : "My favorite Book",
  "authors" : [
    ObjectId("5f145a7c231893e15e9e53fe"),
    ObjectId("5f145a7c231893e15e9e53ff")
  ],
  "creators" : [
    {
      "_id" : ObjectId("5f145a7c231893e15e9e53fe"),
      "name" : "Max Scwarz",
      "age" : 29,
      "address" : {
          "street" : "Main"
      }
    },
    {
      "_id" : ObjectId("5f145a7c231893e15e9e53ff"),
      "name" : "Manuel Lor",
      "age" : 30,
      "address" : {
          "street" : "Tree"
      }
    }
  ]
}
```

# Schema Validation

| Validation Level | Validation Mode                                                                              |
|------------------|----------------------------------------------------------------------------------------------|
| validationLevel ⇒ Which document get validated? | strict ⇒ All inserts & updates. <br/> moderate ⇒ All inserts & updates to correct documents. |
| validationAction ⇒ What happens if validation fails? | error ⇒ Throw error and deny insert/update <br/> warn ⇒ Log warning but proceed.             |

Adding validation(should be set before inserting)

```scheme
> db.createCollection("posts", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["title", "text", "creator", "comments"],
      properties : {
        title: {
          bsonType : "string",
          description: "must be a string and is required."
        },
        text: {
          bsonType : "string",
          description: "must be a string and is required."
        },
        creator: {
          bsonType : "objectId",
          description: "must be a objectId and is required."
        },
        comments: {
          bsonType : "array",
          description: "must be an array and is required.",
          items: {
            bsonType: "object",
            required: ["text", "author"],
            properties: {
              text: {
                bsonType : "string",
                description: "must be a string and is required."
              },
              author: {
                bsonType : "objectId",
                description: "must be an objectId and is required."
              },
            }
          }
        }
        }
      }
    }
});
```

### Modifying existing validator

`collMod`(collectionModifier) ⇒ Name of the collection whose validation rule will be updated.
Note: like as validator as previous. 

```scheme
db.runCommand({
  collMod: "posts",
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["title", "text", "creator", "comments"],
      properties: {
        title: {
          bsonType: "string",
          description: "must be a string and is required.",
        },
        text: {
          bsonType: "string",
          description: "must be a string and is required.",
        },
        creator: {
          bsonType: "objectId",
          description: "must be a objectId and is required.",
        },
        comments: {
          bsonType: "array",
          description: "must be an array and is required.",
          items: {
            bsonType: "object",
            required: ["text", "author"],
            properties: {
              text: {
                bsonType: "string",
                description: "must be a string and is required.",
              },
              author: {
                bsonType: "objectId",
                description: "must be an objectId and is required.",
              },
            },
          },
        },
      },
    },
  },
  validationAction: "warn",
});
```

# Data Types

- Text
- Boolean
    - true
    - false
- Number
    - Integer(int32)
    - NumberLong(int64)
    - NumberDecimal
- ObjectId
- Time
    - ISODate
    - Timestamp
- Embedded Document
- Array