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
db.once('open', create
);



/* ---- METHOD #2 - Callback ---- */
const connOpen = async e => {
  if(e) throw new Error(e)

  await createSchema()
}

mongoose.connect('mongodb://localhost/test', connOptions, connOpen)






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

# ObjectId

<br><br>

## Convert id to ObjectId
```javascript
var mongoose = require('mongoose');
var id = mongoose.Types.ObjectId('4edd40c86762e0fb12000003');
```




















































<br><br>
___________________________________________
___________________________________________
<br><br>


# Schema

<br><br>

## .loadClass() (https://mongoosejs.com/docs/api.html#schema_Schema-loadClass)

```javascript
const md5 = require('md5');
const userSchema = new Schema({ email: String });
class UserClass {
  // `gravatarImage` becomes a virtual
  get gravatarImage() {
    const hash = md5(this.email.toLowerCase());
    return `https://www.gravatar.com/avatar/${hash}`;
  }

  // `getProfileUrl()` becomes a document method
  getProfileUrl() {
    return `https://mysite.com/${this.email}`;
  }

  // `findByEmail()` becomes a static
  static findByEmail(email) {
    return this.findOne({ email });
  }
  
  static async createEntity() {
    const schema = {
        "name": "test"
    }

    const myDoc = new this(schema)
    await myDoc.save()
}
}

// `schema` will now have a `gravatarImage` virtual, a `getProfileUrl()` method,
// and a `findByEmail()` static
userSchema.loadClass(UserClass);


```


<br><br>

## Virtuals
```javascript
const userSchema = mongoose.Schema({
  email: String
});





/* Example #1 */
// Create a virtual property `domain` that's computed from `email`.
userSchema.virtual('domain').get(function() {
  return this.email.slice(this.email.indexOf('@') + 1);
});




/* Example #2 - .loadClass() */
class UserClass {
  getDomain() {
    return this.email.slice(this.email.indexOf('@') + 1);
  }
}

userSchema.loadClass(UserClass);






const User = mongoose.model('User', userSchema);
let doc = await User.create({ email: 'test@gmail.com' });
doc.domain; // 'gmail.com'
```


<br><br>

#### Convert _id to id
```javascript
// Duplicate the ID field.
Schema.virtual('id').get(function(){
    return this._id.toHexString();
});

// Ensure virtual fields are serialised.
Schema.set('toJSON', {
    virtuals: true
});
```









<br><br>
<br><br>



## Document Method
```javascript
const userSchema = new Schema({ email: String });
class UserClass {
  getProfileUrl() {
    return `https://mysite.com/${this.email}`;
  }
}

userSchema.loadClass(UserClass);

const User = mongoose.model('User', userSchema);
let doc = await User.create({ email: 'test@gmail.com' });
doc.getProfileUrl(); // 'https://mysite.com/test@gmail.com'
```




<br><br>
<br><br>



## Model Method
```javascript
const userSchema = new Schema({ email: String });
class UserClass {
  getProfileUrl() {
    return `https://mysite.com/${this.email}`;
  }
}

userSchema.loadClass(UserClass);

const User = mongoose.model('User', userSchema);
User.schema.methods.getProfileUrl(); // 'https://mysite.com/test@gmail.com'
```



<br><br>



## Static (https://mongoosejs.com/docs/api.html#schema_Schema-static)
```javascript
const schema = new Schema(..);



/* Example #1 */
// Equivalent to `schema.statics.findByName = function(name) {}`;
schema.static('findByName', function(name) {
  return this.find({ name: name });
});




/* Example #2 */
class UserClass {
  findByName() {
     return this.find({ name: name });
  }
}

schema.loadClass(UserClass);






const Drink = mongoose.model('Drink', schema);
await Drink.findByName('LaCroix');
```




<br><br>



## Plugins (https://mongoosejs.com/docs/plugins.html)
```javascript
// loadedAt.js
module.exports = function loadedAtPlugin(schema, options) {
  schema.virtual('loadedAt').
    get(function() { return this._loadedAt; }).
    set(function(v) { this._loadedAt = v; });

  // do something after the documents gets saved
  schema.post(['find', 'findOne'], function(docs) {
    if (!Array.isArray(docs)) {
      docs = [docs];
    }
    const now = new Date();
    for (const doc of docs) {
      doc.loadedAt = now;
    }
  });
 
   // do something bevore the documents gets saved
    schema.pre('save', function(next) {
        const author = requestContext.get('request').author;
        this._createdBy = author.sub;
        this._owner = author.sub;
        this._groupOwner = author.group;
        next();
    });
};

// game-schema.js
const loadedAtPlugin = require('./loadedAt');
const gameSchema = new Schema({ ... });
gameSchema.plugin(loadedAtPlugin);

// player-schema.js
const loadedAtPlugin = require('./loadedAt');
const playerSchema = new Schema({ ... });
playerSchema.plugin(loadedAtPlugin);

// You can check if the plugin was correctly loaded by checking the schema of the Model
const Model = mongoose.model(modelName, mongooseSchema, modelName)
console.log('Model.schema.plugins.loadedAtPlugin')
```





<br><br><br><br>



## optimisticConcurrency (https://mongoosejs.com/docs/guide.html#optimisticConcurrency)
- Optimistic concurrency is a strategy to ensure the document you're updating didn't change between when you loaded it using find() or findOne(), and when you update it using save().
```javascript
const Product = mongoose.model('Product', Schema({
  name: String
}, { optimisticConcurrency: true }));

// saves with __v = 0
let product = await new Product({ name: 'apple pie' }).save();

// query a copy of the document for later (__v = 0)
let oldProduct = await Product.findById(product._id);

// increments to __v = 1
product.name = 'mince pie';
product = await product.save();

// throws an error due to __v not matching the DB version
oldProduct.name = 'blueberry pie';
oldProduct = await oldProduct.save();
```


<br><br><br><br>


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


## unique
- We must use .createIndexes() on the model or createIndex in the connection options. Otherwhise there is problem without the unique verification when you not drop the collection before.
```javascript
const uri = 'mongodb://xxx:xxx@127.0.0.1:27017/test_project1?authSource=admin'
const connOptions = {
    useNewUrlParser: true,
    useUnifiedTopology: true
}

const mongoose = require('mongoose')

const mongodbUri = require('mongodb-uri')
const MongoClient = require('mongodb').MongoClient
const client = new MongoClient(uri, connOptions)
let uriObj = mongodbUri.parse(uri)

/**
 *
 * @returns {Promise<void>}
 */
const dropTestDbs = async () => {
    try {
        const conn = await client.connect()
        const dbs = await conn.db().admin().listDatabases()

        for(const db of dbs.databases) {
            const dbName = db.name

            if (dbName.includes('test')) {
                uriObj.database = dbName
                const uri = mongodbUri.format(uriObj)

                const client = new MongoClient(uri, connOptions)
                const conn = await client.connect()

                await conn.db().dropDatabase()
            }
        }
    } catch (e) {
        throw new Error(`Can not drop test databases - Error: ${e}`)
    }
}


const sleep = async timeout => new Promise(resolve => setTimeout(resolve, timeout))

const main = async () => {
    await dropTestDbs()

    await mongoose.connect(uri, connOptions)

    const testSchema = new mongoose.Schema({
        name: {
            type: String,
            unique: true,
            required: true
        }
    }, { collection: 'test' })


    const Model = mongoose.model('test', testSchema)
    Model.createIndexes();
    
    // Method #2
    // Model.db.collection('test').createIndex({name:1}, {unique:true})

    await Model.create({ "name": "test1" })
    
    // will throw error
    await Model.create({ "name": "test1" })
}

main().catch(e => console.log(e))
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


## findByIdAndUpdate
```javascript
const version = 1 || __v
const query = {$set: newDataObject, $inc: {__v: version}}
const doc = await Model.findByIdAndUpdate(statisticsId, query, {new: true})
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

## Get _doc of Model
- If you set virtuals to true they will be included aswell
```javascript
const docs = await ModelEntity.findOne({modelName})
const doc = docs.toJSON({ virtuals: true })
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


<br><br>

## access different collection
```javascript
const layoutBase = await Model.db.collection('CollectionName').findOne()
```




<br><br><br><br>

## Update property of model
```javascript
const schema = {
    "name": "abc"
}
        
const myDoc = new Model(schema)
await myDoc.save()

myDoc.set('name', 'newname')
await myDoc.save()
```




<br><br><br><br>

## .find()
```javascript
const doc = await mongooseModel.find(query)

// return array instead of mongoose documents
const doc = await mongooseModel.find(query).toArray()

// Note that lean is not the same as toJSON as it returns a raw dump from Mongo (meaning no virtuals are included). See this for more info
const doc = await mongooseModel.find(query).lean()
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





<br><br><br><br>

## delete all models
- Since Mongoose is a singleton you can to two times create the same model. For this case you may want to delete the current models
```javascript
for (const model in mongoose.models) {
    delete mongoose.models[model]
}
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
