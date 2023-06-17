# Mongodb Guide

- Database: It holds different collections(tables) in it. e.g. my_project_db
- Collection: A collection is like a table in SQL. It contains documents(rows). e.g. Users collection
- Document: A document is one row in SQL. It contains key value pairs. e.g { name: "paras", age: 2 }

### Start mongo server

```bash
systemctl start mongod
systemctl status mongod
```

### Start mongo shell

```bash
mongo
```

```bash
# clear screen
> cls
```

### Exit shell

```bash
> exit
```

## Basics

#### Show databases

```bash
> show dbs
```

#### Switching to a db

```bash
> use mydb # it will switch but won't show in dbs until data is added
```

#### Using db

```bash
> db.mydb.insertOne({ ... })
```

### Basic CRUD

- Find

NOTE: find method returns a cursor to the collection

```bash
# find all
db.mydb.find().pretty()

# find one
db.mydb.findOne({ _id: 123 })

# find many
db.mydb.find({ name: true })

# find greater than x
db.mydb.find({ age: { $gt: 20 }})
```

- Insert

```bash
# insert one
db.mydb.insert({ name: "xyz", age: 20 })

db.mydb.insertMany([
    { name: "abc", age: 20 },
    { name: "mno", age: 21}
])
```

- Update

```bash
# update one
db.mydb.updateOne({ _id: 123 }, {
    $set: { name: "xyz" }
})

# update many
db.mydb.updateMany({ age: 20 }, {
    $set: { name: "common" }
})
```

- Replace

```bash
# replace one
db.mydb.replaceOne({ name: "xyz" }, { name: "xyz", age: 12 })

# replace many
db.mydb.replaceMany({ age: 12 }, { name: "xyz", age: 20 })
```

- Delete

```bash
# delete all
db.mydb.deleteMany({})

# delete one
db.mydb.deleteOne({ _id: 123 })

# delete many
db.mydb.deleteMany({ age: 20 })
```

### Get required fields only (Projections)

```bash
# return name field for all the data
> db.mydb.find({}, { name: 1 })

# return name field but exclude id
> db.mydb.find({}, {name: 1, _id: 0})
```

### Nested/Embedded Documents

```json
{
   "name": "xyz",
   "age": 20,
   "school": {
      "name": "abc",
      "place": "mno"
   }
}
```

```bash
# query nested data
# get document document where school name is "abc"
> db.mydb.find({ "school.name": "abc" })
```

## Working with GeoJSON Data

```bash
# adding a location
> db.places.insertOne({
    name: "California Academy of Science",
    location: {
        type: "Point",
        coordinates: [-122.4724356, 37.7672544] # long, lat
    }
})

# create geospatial index 
> db.places.createIndex({location: "2dsphere"})

# querying nearby places from db
> db.places.find({
    location: {
        $near: {
            $geometry: {
                type: "Point",
                coordinates: [-122.471114, 37.771104]
            },
            $maxDistance: 400, #meters
            $minDistance: 10, #meters
        }
    }
})
```

## Aggregation Framework

Aggregation is an alternative of find method.

Aggregation framework is all about building a pipeline of steps that runs on the data retrieved from collection and gives output that you needed

Every stage in pipeline receives the output of the previous stage.

Pipeline is nothing but a series of steps.

pointers:

- $match: find items
- $group

**Usage :**

```js
db.persons.aggregate([
   { $match: {gender: "female"}},
   { $group: {_id: { state: "$location.state" }, totalPersons: { $sum: 1 } } } 
]).pretty();

/*
{_id: {state: "tayside"}, totalPersons: 4}
*/
```

- $group needs _id key. In id key you define the field to group the docs by. 
- Next key after id defines the field name in which you want your result to store. e.g. sum
- in that field we can define the operation