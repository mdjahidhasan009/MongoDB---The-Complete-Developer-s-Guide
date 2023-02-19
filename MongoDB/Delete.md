# Delete
<details>
<summary>Initialization</summary>

```schema
    use user
    switched to db user
  ```
</details>

## Deleting one document

```bash
db.users.deleteOne({name: 'Chris'})
{ "acknowledged": true, "deletedCount": 1 }
```

Delete with matched query

```bash
> db.users.deleteOne({totalAge: {$gt: 30}, isSporty: true})
> db.users.deleteOne({totalAge: {$exists: false}, isSporty: true})
```

## Delete many

Delete all documents which `totalAge` greater than 30 and `isSporty` is `true`

```bash
> db.users.deleteMany({totalAge: {$gt: 30}, isSporty: true})
```

Delete all document which does not have `totalAge` element and `isSporty` is `true`(for `null` `$exists` will be true)

```bash
> db.users.deleteMany({totalAge: {$exists: false}, isSporty: true})
```

## Deleting all entries in a collection

`{}` ⇒ this is simply a filter that matches every document in the collection.

```bash
db.users.deleteMany({})
```

## To delete the entire collection

```bash
db.users.drop()
```

## To delete the entire database

```bash
db.dropDataBase()
```