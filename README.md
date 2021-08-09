# Mongoose Cheat Sheet
Mongoose Cheat Sheet with the most needed stuff..




<br><br>


# Guides
- Lets get started (https://mongoosejs.com/docs/index.html)
- Beginner Guide (https://www.youtube.com/playlist?list=PLGquJ_T_JBMQ1C0Pp41sykceli8G1UGtg)
























<br><br>
___________________________________________
___________________________________________
<br><br>


# Connection

<br><br>


## .connect()

```javascript
const mongoose = require('mongoose');
// poolSize ist the amount of connections we allow
const connOptions = {useNewUrlParser: true, useUnifiedTopology: true, poolSize: 5}



/* ---- METHOD #1 - Listener ---- */
mongoose.connect('mongodb://localhost/test', connOptions);

const db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', createSchema);



/* ---- METHOD #2 - Callback ---- */
mongoose.connect('mongodb://localhost/test', connOptions, connOpen)

const connOpen = async e => {
  if(e) throw new Error(e)

  await createSchema()
}







const createSchema = async () => {
  // With Mongoose, everything is derived from a Schema. Let's get a reference to it and define our kittens.
  const kittySchema = new mongoose.Schema({
    name: String
  });
  
  // So far so good. We've got a schema with one property, name, which will be a String. The next step is compiling our schema into a Model.
  const Kitten = mongoose.model('Kitten', kittySchema);
  
  // A model is a class with which we construct documents. In this case, each document will be a kitten with properties and behaviors as declared in our schema. Let's create a kitten document representing the little guy we just met on the sidewalk outside:
  const silence = new Kitten({ name: 'Silence' });
  console.log(silence.name); // 'Silence'
}

```


<br><br><br><br>







## .createConnection()
```javascript
const mongoose = require('mongoose');
const connOptions = {useNewUrlParser: true, useUnifiedTopology: true}

const conn = await mongoose.createConnection(uri, connOptions)

const testSchema = new mongoose.Schema({
    name: String
}, { collection : 'YourCollectionName' })

const testModel = conn.model('test', testSchema)

const myDoc = new testModel({
    name: "abc"
})

const resSave = await myDoc.save()

const res = await testModel.findOne({ "name": "abc" }).exec()
```


<br><br>

#### Drop Database
```javascript
const conn = mongoose.createConnection('mongodb://localhost:27017/mydb');
// Deletes the entire 'mydb' database
await conn.dropDatabase();
```









































































<br><br>
___________________________________________
___________________________________________
<br><br>


# Schema

<br><br>

## create new schema with specific collection name
- For default mongoose will add the letter s to your collection and write everything lowercase. To force your own collection name you can do this:
```javascript
var dataSchema = new Schema({..}, { collection: 'data' })

// or 

var collectionName = 'actor'
var M = mongoose.model('Actor', schema, collectionName);
```
<br><br>


## timestamps (https://mongoosejs.com/docs/guide.html#timestamps)
- The timestamps option tells mongoose to assign createdAt and updatedAt fields to your schema. The type assigned is Date. By default, the names of the fields are createdAt and updatedAt. Customize the field names by setting timestamps.createdAt and timestamps.updatedAt.
```javascript
var mySchema = new mongoose.Schema({name: String}, {timestamps: true});
```


<br><br>


## optimisticConcurrency (https://mongoosejs.com/docs/guide.html#optimisticConcurrency)
- Optimistic concurrency is a strategy to ensure the document you're updating didn't change between when you loaded it using find() or findOne(), and when you update it using save(). For example, suppose you have a House model that contains a list of photos, and a status that represents whether this house shows up in searches. Suppose that a house that has status 'APPROVED' must have at least two photos

```javascript
const House = mongoose.model('House', Schema({
  status: String,
  photos: [String]
}, { optimisticConcurrency: true }));
```





















































<br><br>
___________________________________________
___________________________________________
<br><br>


# Document

<br><br>

## create new document
```javascript
const Tank = mongoose.model('Tank', { size: 'string' });
const small = new Tank({ size: 'small' });
await small.save();
```














































<br><br>
___________________________________________
___________________________________________
<br><br>


# Model
```javascript
const testSchema = new mongoose.Schema({
    name: String
}, { collection : 'YourCollectionName' })

const testModel = conn.model('test', testSchema)

const myDoc = new testModel({
    name: "abc"
})

const resSave = await myDoc.save()
```

<br><br>

## create model without schema
- The option strict can be set to false to not force to use the schema when creating an model
```javascript
const EmployeeSchema = new Mongoose.Schema({}, {strict:false });
Mongoose.model(modelName, EmployeeSchema);
```

<br><br>

## allow special characters for properties
- Allows forbidden characters like . or $
```javascript
await model.save({ checkKeys: false })
```



<br><br><br><br>

## find()
```javascript
const doc = await mongooseModel.find(query)
```

<br><br>

## .findeOne()
```javascript
const conn = await mongoose.createConnection(uri)

const testSchema = new mongoose.Schema({
    name: String
}, { collection : 'YourCollectionName' })

const testModel = conn.model('test', testSchema)

const res = await testModel.findOne({ "name": name })
```



<br><br><br><br>


#### .findOneAndUpdate()
```javascript
const res = await mongooseModel.findOneAndUpdate(query, { name: 'test' })
```




<br><br><br><br>


#### .create()
```javascript
const User = mongoose.model('User', mongoose.Schema({
  email: String
}));

const doc = await User.create({ email: 'bill@microsoft.com' });
```






























































<br><br>
___________________________________________
___________________________________________
<br><br>


# Collection

<br><br>

## drop collection
```javascript
await mongoose.dropCollection(collection)
```


<br><br>

## check if collection exist
```javascript
// check all collection in db
const res = await conn.db.listCollections().toArray()

// check for specific collection in db
const res = await conn.db.listCollections({name: 'ModelAdditional'}).toArray()
```


<br><br>

## get all documents from collection
```javascript
const model = await conn.model(modelName, schema, collectionName)
const documents = (await model.find({})).map(doc => doc._doc)
```
