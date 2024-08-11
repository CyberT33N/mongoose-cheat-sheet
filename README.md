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

# Typescript

<br><br>
<br><br>

## Model
- https://dev.to/ghostaram/how-to-create-mongoose-models-using-typescript-7hf
```typescript
import { Model } from 'mongoose

interface IVehicle{
    make: string
    vehicle_model: string
    manufacturer: string
    design: string
    year_sold: number
}


type VehicleModel = Model<IVehicle>

const vehicleSchema = new Schema<IVehicle, VehicleModel>({})

const vehicleSchema: Schema = new Schema<IVehicle, VehicleModel>({
    make: { type: String },
    vehicle_model: { type: String },
    manufacturer: { type: String },
    design: { type: String }
    year_sold: {type: Number }
})

const Vehicle: VehicleModel = model<IVehicle, VehicleModel>('Vehicle', vehicleSchema)
```

<br><br>
<br><br>

## Connection
- https://github.com/Automattic/mongoose/blob/master/types/connection.d.ts

<br><br>

### createConnection
```typescript
import mongoose, { ConnectOptions, Connection } from 'mongoose'

class MongooseUtils {
    // eslint-disable-next-line no-use-before-define
    private static instance: MongooseUtils
    private conn: mongoose.Connection | null
    private connectionString: string

    private constructor() {
        this.conn = null
        this.connectionString = process.env.MONGODB_CONNECTION_STRING
    }

    private async init() {
        if (_.isEmpty(this.conn)) {
            try {
                this.conn = await mongoose.createConnection(this.connectionString).asPromise() as Connection
            } catch (e: any) {
                throw new BaseError('MongooseUtils() - Error while init connection with mongoose', e)
            }
        }
    }
}
```


<br><br>

### connect
```typescript
import mongoose, { ConnectOptions, Connection } from 'mongoose'

class MongooseUtils {
    // eslint-disable-next-line no-use-before-define
    private static instance: MongooseUtils
    private conn: mongoose.Connection

    public async createMConn(
        name: string
    ) {
        this.conn = await mongoose.connect(connectionString, { dbName } as ConnectOptions)
    }
}
```





















<br><br>
___________________________________________
___________________________________________
<br><br>


# Connection String

## Change db name
```typescript
private updateConnectionString() {
    const urlObj = new URL(this.connectionString)
    urlObj.pathname = `/${this.dbName}`
    const urlString = urlObj.toString()
    const newString = urlString
}
```


























<br><br>
___________________________________________
___________________________________________
<br><br>


# Connection

<br><br>


## .connect()
- Will be used for single connection

```javascript
const mongoose = require('mongoose');
// poolSize ist the amount of connections we allow
const connOptions = {useNewUrlParser: true, useUnifiedTopology: true, poolSize: 5}



/* ---- METHOD #1 - Await ---- */
await mongoose.connect(process.env.MONGODB_ADDRESS)



/* ---- METHOD #2 - Listener ---- */
mongoose.connect('mongodb://localhost/test', connOptions);

const db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', create
);



/* ---- METHOD #3 - Callback ---- */
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


<br><br>
<br><br>







## .createConnection()
- will be used for multiple connections
  - https://mongoosejs.com/docs/connections.html#multiple_connections 
```javascript
const mongoose = require('mongoose');
const connOptions = {useNewUrlParser: true, useUnifiedTopology: true}

// Mongose 5
const conn = await mongoose.createConnection(uri, connOptions)

// Mongoose 6
// const conn = await mongoose.createConnection(uri, connOptions).asPromise()

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

### show active connections
```javascript
// Create a mongoose connection using the given address and config
const conn = await mongoose.createConnection(mongooseConnectString, this.mongoOptions).asPromise() 

// Verwende die Admin API
const admin = new mongoose.mongo.Admin(conn.db)

// Erhalte Serverstatus
const status = await admin.serverStatus()
```

<br><br>

### Close connections after apps crash
```javascript
/**
 * Cleans up resources and exits the process.
 * @async
 * @function cleanUp
 * @returns {Promise<void>}
 */
const cleanUp = async() => {
    const mt = new MongoMTConnection()
    await mt.destroy()
    await mongoose.connection.close()
    process.exit(1)
}

process.on('uncaughtException', async e => {
    console.error((new Date).toUTCString() + ' uncaughtException:', e.message)
    console.error(e.stack)
    await cleanUp()
})

;['exit', 'SIGINT', 'SIGUSR1', 'SIGUSR2', 'SIGTERM'].forEach((eventType) => {
    process.on(eventType, cleanUp)
})
```






<br><br>
<br><br>
<br><br>
<br><br>


#### Drop Database
```javascript
const conn = mongoose.createConnection('mongodb://localhost:27017/mydb');
// Deletes the entire 'mydb' database
await conn.dropDatabase();
```






<br><br>
<br><br>




#### readyState
- Check if connection is still valid
```javascript
const conn = mongoose.createConnection('mongodb://localhost:27017/mydb');
if (conn.readyState === conn.states.disconnected) { /* .. */ }
```

<br><br>
<br><br>


#### retry options
- With Mongoose 6 you do not need useNewUrlParser & useUnifiedTopology anymore. It will be set by default.
```javascript
connectTimeoutMS=30000
socketTimeoutMS=30000
useNewUrlParser=true
useUnifiedTopology=true
keepAlive=1
```










































<br><br>
___________________________________________
___________________________________________
<br><br>





# Collection

<br><br>
<br><br>


## .createCollection()
```javascript
await Model.createCollection()

// [options.autoCreate=false] «Boolean» Set to true to make Mongoose automatically call createCollection() on every model created on this connection.
```

<br><br>


## get all indexes from collection of Model
```javascript
const indexes = await Model.collection.getIndexes({full: true})
```




<br><br>
___________________________________________
___________________________________________
<br><br>


# Shell

<br><br>


## How to run mongo shell commands in Mongoose?
```javascript
mongoose.connection.db.admin().command({ isMaster: 1 }, (err, result) => {
  console.log('Result: ', result);
})

// or

mongoose.connection.executeDbAdminCommand({ isMaster: 1 }, (err, result) => {
  console.log('Result: ', result);
})
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

## Prevent update on specific field 
-  https://mongoosejs.com/docs/api/schematype.html#schematype_SchemaType-immutable
-  Notice that you can not use immutable with strict:false as schema options

```javascript
const CustomerSchema = new mongoose.Schema({
    name : {
        type : String,
        required : true,
        trim : true
    },
    email : {
        type : String,
        required : true,
        trim : true,
        immutable: true // ADD THIS PROPERTY HERE
    },
    balance : {
        type : Number ,
        default : 0
    }
}


// You can also use a plugin for this and delete the field before updating..

```






<br><br>

## Schema Options
- https://mongoosejs.com/docs/guide.html#options



```
Valid options:

autoIndex
autoCreate
bufferCommands
bufferTimeoutMS
capped
collection
discriminatorKey
id
_id
minimize
read
writeConcern
shardKey
strict
strictQuery
toJSON
toObject
typeKey
useNestedStrict
validateBeforeSave
versionKey
optimisticConcurrency
collation
timeseries
selectPopulatedPaths
skipVersioning
timestamps
storeSubdocValidationError
```

```javascript
const options = {
    optimisticConcurrency: true, // https://mongoosejs.com/docs/guide.html#optimisticConcurrency
    validateBeforeSave: true, // https://mongoosejs.com/docs/guide.html#validateBeforeSave
    toObject: { virtuals: true }, // https://mongoosejs.com/docs/api.html#document_Document-toObject
    toJSON: { virtuals: true },
    id: false
}

// Example #1
const CustomerSchema = new mongoose.Schema({..}, options)


// Example #2
const schema = new Schema({..})
schema.set(options, value)
```







<br><br>

## Allow additional fields that are not described in schema
```javascript
let carSchema = new mongoose.Schema({
    url:  String,
    unique: {type: String, index: { unique: true }},
    number: String,
    title: String,
    price: String,
}, { strict: false });
```







<br><br>

## Disable type casting
- https://stackoverflow.com/questions/24153711/mongoose-not-enforcing-type-with-model-save
- http://thecodebarbarian.com/whats-new-in-mongoose-54-global-schematype-configuration
- https://mongoosejs.com/docs/tutorials/custom-casting.html

```javascript
/*
Before running validators, Mongoose attempts to coerce values to the correct type. This process is called casting the document. If casting fails for a given path, the error.errors object will contain a CastError object.

Casting runs before validation, and validation does not run if casting fails.

- String:
  - false -> "false"

- Number:
  - "123" -> 123
  - "false" -> 0
*/


method #1 - global
mongoose.Schema.Types.Boolean.cast(false);
mongoose.Schema.Types.Date.cast(false);
mongoose.Schema.Types.Decimal128.cast(false);
mongoose.Schema.Types.Number.cast(false);
mongoose.Schema.Types.ObjectId.cast(false);
mongoose.Schema.Types.String.cast(false);



// method #2 - Disable for path
age: {
  type: Number, 
  cast: false // Disable casting just for this path
}


// method #3 - Disable for path with custom function
age: {
  type: Number,
  cast: v => { return typeof v === 'number' && !isNaN(v) ? Number(v) : v; } // Override casting just for this path
}
```






<br><br>

## Set index
```javascript
// method #1
const Schema = mongoose.Schema;
let user = new Schema({
    email: {
        type: String,
        required: true,
        index: true       //---Index----
});

module.exports = mongoose.model('User', user);





// method #2
const Schema = mongoose.Schema;
let user = new Schema({
    email: {
        type: String,
        required: true
});
user.index({ email: 1 });    //---Index----

module.exports = mongoose.model('User', user);
```






<br><br>

## Custom validation
- https://mongoosejs.com/docs/validation.html#custom-validators

```javascript
const userSchema = new Schema({
  phone: {
    type: String,
    validate: {
      validator: function(v) {
        return /\d{3}-\d{3}-\d{4}/.test(v);
      },
      message: props => `${props.value} is not a valid phone number!`
    },
    required: [true, 'User phone number required']
  }
});

const User = db.model('user', userSchema);
const user = new User();
let error;

user.phone = '555.0123';
error = user.validateSync();
assert.equal(error.errors['phone'].message,
  '555.0123 is not a valid phone number!');

user.phone = '';
error = user.validateSync();
assert.equal(error.errors['phone'].message,
  'User phone number required');

user.phone = '201-555-0123';
// Validation succeeds! Phone number is defined
// and fits `DDD-DDD-DDDD`
error = user.validateSync();
assert.equal(error, null);
```




<br><br>

## How to allow null als value for required properties

```javascript
const testSchema = new mongoose.Schema({
   type: String,
   name: {
        type:String,
        unique: true,
        required: function () {
            const res = this.name === null ? false : true
            return res
        }
    }
})
```

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
- Notice that when you want to create a new document inside of a static it is not enough to delete the _id and use this.create(). You must define a new _id 
```javascript
const schema = new Schema(..);



/* Example #1a */
// Equivalent to `schema.statics.findByName = function(name) {}`;
schema.static('findByName', function(name) {
  return this.find({ name: name });
});

/* Example #1b - Create new document */
schema.static('clone', function(id) {
    let toClone = await this.findOne({ _id: id }).select('-_id')

    const clone = Object.assign({}, toClone._doc)
    
    clone.name += '_copy'
    clone._id = mongoose.Types.ObjectId()
    
    await this.create(clone)
    return clone
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
<br><br>



## Plugins (https://mongoosejs.com/docs/plugins.html)
```javascript
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
}

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


<br><br>
<br><br>


### pre Hook
- Do something before the document gets saved
```javascript
module.exports = function loadedAtPlugin(schema, options) {
  schema.pre('save', function(next) {
      try {
          // Method #1
          const authour = this.author

          // Method #2 - Maybe not working
          const author = requestContext.get('request').author;

          // Method #3
          const author = this.get('author')
          
          // You can access aswell other collections
          const collection = this.db.collection('collectionName')
          //  const collection = this.model.db.collection('collectionName')
          //  const collection = this.model.db('anyDB').collection('collectionName')

          // You can access aswell other models
          const model = this.model('modelName')

          // Get payload
          const payload = this._update.$set

          // Change value of property
          this.set('_createdBy', author.sub)

          // This will mostly not work 
          this._createdBy = author.sub;
          this._owner = author.sub;
          this._groupOwner = author.group;
          
          next();
      } catch (e) {
          next(e)
      }
  });
  
  // Get query and search document for original state
  // because you only have access to the body that update the doc and not to all properties of the updated doc from the db we must search it
  schema.pre(['findOneAndUpdate'], async function(next) {
      try {
          const query = this.getQuery()

          // method 1
          const doc =  await this.findOne(query)

          // method 2 - recommended
          const docToUpdate = await this.model.findOne(this.getQuery())

          // method 3
           const schema = this;
           const { newUpdate } = schema.getUpdate();
           const queryConditions = schema._conditions
      
           if(newUpdate){
             //some mutation magic
             await schema.updateOne(queryConditions, {newUpdate:"modified data"}); 
           }

          // method 4
          // const id = this.getQuery().$and[0]._id // maybe this works too this.getQuery()._id

          next()
      } catch (e) {
          next(e)
      }
  })

  // change field before updating
  schema.pre(['findOneAndUpdate', 'updateOne', 'update', 'updateMany'], async function(next) {
      try {
        if (htmlFragment) {
            // method #1
            this.set('htmlFragment', 'new name')
            
            // method #2
            const { htmlFragment } = this.getUpdate();

            // method #3
            this._update.htmlFragment = 'new name'
        }
        
        // delete field to make it immutable. So the original value will be choosen
        if (htmlFragment) {
            delete this._update.$set.htmlFragment
        }
        next()
      } catch (e) {
          next(e)
      }
   })
}
```

<br><br>
<br><br>

### post Hook 
- Do something after the documents was saved
```javascript
// loadedAt.js
module.exports = function loadedAtPlugin(schema, options) {
   // do something after the documents gets saved - method #2
  schema.post("save", async function (doc, next) {
    try {
      let data = await doc
        .model("User")
        .findOneAndUpdate({ _id: doc._id }, { exampleIDField: "some ID you want to pass" });
    } catch (error) {
      console.log("get -> error", error);
      next(error);
    }
  });
}
```



<br><br>
<br><br>

### work with pre & post hook together
- You can set properties in this in the pre hook and work later with it in the post hook
```javascript
yourSchema.pre(['findOneAndUpdate'], async function(next) {
  // Dein pre-hook-Code hier
  // Du kannst auf das zu aktualisierende Dokument mit this._update zugreifen

  // Beispiel: Speichere das ursprüngliche Dokument vor dem Update
  const originalDocument = await this.model.findOne(this.getQuery());

  // Speichere das ursprüngliche Dokument im Hook-Objekt, um darauf im post-hook zugreifen zu können
  this._originalDocument = originalDocument;

  next();
});

yourSchema.post(['findOneAndUpdate'], async function(result, next) {
  // Dein post-hook-Code hier
  // Du kannst auf das aktualisierte Dokument mit this._update zugreifen

  // Beispiel: Zugriff auf das ursprüngliche Dokument vor dem Update
  const originalDocument = this._originalDocument;
  console.log('Vor dem Update:', originalDocument);

  next();
});
```




<br><br>
<br><br>



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
const Tank = mongoose.model('Tank', { size: 'string', 'schedule': 'object' })
const doc = new Tank({ size: 'small' })

doc.size = 'big'

await doc.save()

// If you want to change nested properties you have to use this otherwhise doc.save() will not work and child properties of objects not get updated.. 
doc.schedule.mode = 'activated'
doc.markModified('schedule')
await doc.save()
```

<br><br>

## validate document
```javascript
const schema = new mongoose.Schema({
  age: Number
});

const Model = mongoose.model('Test', schema);

const doc = new Model({ age: 'wrong type' });
const err = doc.validateSync();
```

<br><br>

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

## Get random document
- If you set virtuals to true they will be included aswell
```javascript
Model.aggregate([{ $sample: { size: 1 } }])
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
const EmployeeSchema = new mongoose.Schema({}, {strict:false });
const Model = mongoose.model(modelName, EmployeeSchema);
await Model.create(data)

// If you got any problems while creating data with method from above then try this
const instance = new Model()
const data = instance._doc
instance._doc = {...data, ...yourDataHere}

await instance.save()
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
const data = { name: 'test' }

const options = {
    new: true, // <-- Will return the updated doc
    
    // If you use a custom validator you must enable those two options 
    runValidators: true, // <-- enables custom validator
    context: 'query' // <-- enables this context to custom validator
}

const res = await Model.findOneAndUpdate(query, data, options)
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
# With model
const model = await conn.model(modelName, schema, collectionName)
const documents = (await model.find({})).map(doc => doc._doc)

# Without model
const documents = await conn.db.collection('Apple').find().toArray()
```













































<br><br>
___________________________________________
___________________________________________
<br><br>


# Migration

<br><br>

## Mongoose 5 to 6

<br><br>

### asPromise()
- https://mongoosejs.com/docs/migrating_to_6.html#the-aspromise-method-for-connections
```javascript
const conn = await mongoose.createConnection(uri).asPromise()
```

<br><br>

### Duplicate Query Execution
. https://mongoosejs.com/docs/migrating_to_6.html#duplicate-query-execution
```javascript
// Results in 'Query was already executed' error, because technically this `find()` query executes twice.
await Model.find({}, function(err, result) {});

const q = Model.find();
await q;
await q.clone(); // Can `clone()` the query to allow executing the query again
```


<br><br>

### Defaults
- Mongoose Options useNewUrlParser, keepAlive & useUnifiedTopology are now default
- The context option for queries has been removed. Now Mongoose always uses context = 'query'
















































<br><br>
___________________________________________
___________________________________________
<br><br>


# Helper

## Singleton MongooseUtils
```typscript
// ==== DEPENDENCIES ====
import _ from 'lodash'
import mongoose, { ConnectOptions, Connection } from 'mongoose'
import { BaseError } from 'error-manager-helper'

class MongooseUtils {
    // eslint-disable-next-line no-use-before-define
    private static instance: MongooseUtils

    private conn: mongoose.Connection | null
    private connectionString: string

    dbName: string

    private constructor() {
        this.dbName = ''
        this.conn = null
        this.connectionString = process.env.MONGODB_CONNECTION_STRING
    }
  
    public static async getInstance(dbName: string): Promise<MongooseUtils>{
        if (this.instance) {
            this.instance.dbName = dbName
            return this.instance
        }

        // Create instance
        this.instance = new MongooseUtils()

        // ==== ARGS ====
        this.instance.dbName = dbName

        // ==== INITIALIZATION ====
        await this.instance.init()

        return this.instance
    }

    private async getConnection(
        dbName: string
    ): Promise<mongoose.Connection> {
        if (!this.conn || this.conn.name !== dbName) {
            await this.init()
        }

        return this.conn!
    }

    private async init() {
        if (_.isEmpty(this.conn)) {
            try {
                this.updateConnectionString()
                this.conn = await mongoose.createConnection(this.connectionString).asPromise() as Connection
            } catch (e: any) {
                throw new BaseError('MongooseUtils() - Error while init connection with mongoose', e)
            }
        }
    }

    private updateConnectionString() {
        const urlObj = new URL(this.connectionString)
        urlObj.pathname = `/${this.dbName}`
        const urlString = urlObj.toString()
        this.connectionString = urlString
    }

    static createSchema(schema: object, name: string) {
        const mongooseSchema = new mongoose.Schema(schema, {
            collection: name
        })

        return mongooseSchema
    }

    public async createModel(
        schema: object,
        name: string,
        dbName: string
    ) {
        const mongooseSchema = MongooseUtils.createSchema(schema, name)

        const conn = await this.getConnection(dbName)
        const Model = conn.model(name, mongooseSchema, name)
        return Model
    }
}

export default MongooseUtils
```
