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

<details><summary>Click to expand..</summary>
    
# Guides
- https://mongoosejs.com/docs/typescript.html

<br><br>

# Examples projects
- https://github.com/cybert33n/modelmanager


<br><br>
<br><br>

# Schema
- https://dev.to/ghostaram/how-to-create-mongoose-models-using-typescript-7hf
```typescript
interface IVehicle {
    name: string;
    brand: string;
    year: number;
}

const vehicleSchema = new mongoose.Schema<IVehicle>({
    name: { type: String, required: true },
    brand: { type: String, required: true },
    year: { type: Number, required: true }
});
```

<br><br>

## Create Interface from document schema
- Can be achieved by using `mongoose.ObtainDocumentType`
  - This will convert your js document type definiton into a interface which can be used to create your model or mongoose schmea
```typescript
import mongoose from 'mongoose'

const schema = {
    name: { 
        type: String,
        required: true,
        unique: true,
        index: true
    },
    decimals: { type: BigInt, required: true }
}

type TMongooseSchema = mongoose.ObtainDocumentType<typeof schema>

const vehicleSchema = new mongoose.Schema<TMongooseSchema>(schema)
```

<br><br>

## Store document schema in interface
- Can be achieved by using `mongoose.SchemaDefinition`
```typescript
import mongoose from 'mongoose'

const schema = {
    name: { 
        type: String,
        required: true,
        unique: true,
        index: true
    },
    decimals: { type: BigInt, required: true }
}

type TMongooseSchema = mongoose.ObtainDocumentType<typeof schema>

/**
 * Interface representing the details of a Mongoose model.
 * @template TSchema - The type of the document.
 */
export interface IModelCore<TMongooseSchema> {
    /** The name of the model. */
    modelName: string
    /** The name of the database where the model is stored. */
    dbName: string
    /** The schema used for the model. */
    schema: mongoose.SchemaDefinition<TMongooseSchema>
}
```






<br><br>
<br><br>
<br><br>
<br><br>

# Connection
- https://github.com/Automattic/mongoose/blob/master/types/connection.d.ts

<br><br>

## createConnection
- Can be achieved by using `mongoose.Connection`
```typescript
import mongoose } from 'mongoose'

class MongooseUtils {
    // MongoDB connection object
    private conn: mongoose.Connection | null = null

  /**
     * Initializes the MongoDB connection.
     * @throws BaseError if the connection fails.
     * @returns {void} A Promise that resolves when the connection is established.
     */
    private async init(): Promise<void> {
        console.log('[ModelManager] - Attempting to connect to MongoDB...')

        this.updateConnectionString()

        try {
            this.conn = await mongoose.createConnection(this.connectionString).asPromise()
        } catch (e) {
            throw new BaseError(
                '[ModelManager] - Error while initializing connection with MongoDB',
                e as Error
            )
        }
    }
}
```


<br><br>

## connect
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
<br><br>
<br><br>
<br><br>



# Model
- https://mongoosejs.com/docs/typescript.html#creating-your-first-document
```typescript
import { Schema, model, connect } from 'mongoose';

// 1. Create an interface representing a document in MongoDB.
interface IUser {
  name: string;
  email: string;
  avatar?: string;
}

// 2. Create a Schema corresponding to the document interface.
const userSchema = new Schema<IUser>({
  name: { type: String, required: true },
  email: { type: String, required: true },
  avatar: String
});

// 3. Create a Model.
const User = model<IUser>('User', userSchema);

run().catch(err => console.log(err));

async function run() {
  // 4. Connect to MongoDB
  await connect('mongodb://127.0.0.1:27017/test');

  const user = new User({
    name: 'Bill',
    email: 'bill@initech.com',
    avatar: 'https://i.imgur.com/dM7Thhn.png'
  });
  await user.save();

  console.log(user.email); // 'bill@initech.com'
}
```

<br><br>

## Store mongoose model in interface
```typescript
/**
 * Interface representing a Mongoose model along with additional metadata.
 * @template TSchema - The type of the document.
 */
export interface IModel<TSchema>  {
    /** The Mongoose Model instance. */
    Model: mongoose.Model<TSchema>
}

interface IVehicle {
    name: string;
    brand: string;
    year: number;
}

const vehicleSchema = new mongoose.Schema<IVehicle>({
    name: { type: String, required: true },
    brand: { type: String, required: true },
    year: { type: Number, required: true }
});

const Model = model<vehicleSchema>('Vehicle', vehicleSchema);

const obj: IModel<IVehicle> = { Model }
```











<br><br>
<br><br>

# Document
```typescript
import { HydratedDocument } from 'mongoose';

const user: HydratedDocument<IUser> = new User({
  name: 'Bill',
  email: 'bill@initech.com',
  avatar: 'https://i.imgur.com/dM7Thhn.png'
});
```


<br><br>
<br><br>


# Instance method
- To define an instance method in TypeScript, create a new interface representing your instance methods. You need to pass that interface as the 3rd generic parameter to the Schema constructor and as the 3rd generic parameter to Model as shown below.
- https://mongoosejs.com/docs/typescript/statics-and-methods.html
```typescript
import { Model, Schema, model } from 'mongoose';

interface IUser {
  firstName: string;
  lastName: string;
}

// Put all user instance methods in this interface:
interface IUserMethods {
  fullName(): string;
}

// Create a new Model type that knows about IUserMethods...
type UserModel = Model<IUser, {}, IUserMethods>;

// And a schema that knows about IUserMethods
const schema = new Schema<IUser, UserModel, IUserMethods>({
  firstName: { type: String, required: true },
  lastName: { type: String, required: true }
});
schema.method('fullName', function fullName() {
  return this.firstName + ' ' + this.lastName;
});

const User = model<IUser, UserModel>('User', schema);

const user = new User({ firstName: 'Jean-Luc', lastName: 'Picard' });
const fullName: string = user.fullName(); // 'Jean-Luc Picard'
```



<br><br>
<br><br>


# Satics
- Mongoose models do not have an explicit generic parameter for statics. If your model has statics, we recommend creating an interface that extends Mongoose's Model interface as shown below.
- https://mongoosejs.com/docs/typescript/statics-and-methods.html
```typescript
import { Model, Schema, model } from 'mongoose';

interface IUser {
  name: string;
}

interface UserModel extends Model<IUser> {
  myStaticMethod(): number;
}

const schema = new Schema<IUser, UserModel>({ name: String });
schema.static('myStaticMethod', function myStaticMethod() {
  return 42;
});

const User = model<IUser, UserModel>('User', schema);

const answer: number = User.myStaticMethod(); // 42
```






<br><br>
<br><br>


## Create Statics and instance method
- Mongoose models do not have an explicit generic parameter for statics. If your model has statics, we recommend creating an interface that extends Mongoose's Model interface as shown below.
- https://mongoosejs.com/docs/typescript/statics-and-methods.html
```typescript
import { Model, Schema, HydratedDocument, model } from 'mongoose';

interface IUser {
  firstName: string;
  lastName: string;
}

interface IUserMethods {
  fullName(): string;
}

interface UserModel extends Model<IUser, {}, IUserMethods> {
  createWithFullName(name: string): Promise<HydratedDocument<IUser, IUserMethods>>;
}

const schema = new Schema<IUser, UserModel, IUserMethods>({
  firstName: { type: String, required: true },
  lastName: { type: String, required: true }
});
schema.static('createWithFullName', function createWithFullName(name: string) {
  const [firstName, lastName] = name.split(' ');
  return this.create({ firstName, lastName });
});
schema.method('fullName', function fullName(): string {
  return this.firstName + ' ' + this.lastName;
});

const User = model<IUser, UserModel>('User', schema);

User.createWithFullName('Jean-Luc Picard').then(doc => {
  console.log(doc.firstName); // 'Jean-Luc'
  doc.fullName(); // 'Jean-Luc Picard'
});
```



<br><br>
<br><br>


# Query Helper
- Mongoose models do not have an explicit generic parameter for statics. If your model has statics, we recommend creating an interface that extends Mongoose's Model interface as shown below.
- https://mongoosejs.com/docs/typescript/statics-and-methods.html

- The following is an example of how query helpers work in JavaScript.
```javascript
ProjectSchema.query.byName = function(name) {
  return this.find({ name: name });
};
const Project = mongoose.model('Project', ProjectSchema);

// Works. Any Project query, whether it be `find()`, `findOne()`,
// `findOneAndUpdate()`, `delete()`, etc. now has a `byName()` helper
Project.find().where('stars').gt(1000).byName('mongoose');
```

<br><br>


## Manually Typed Query Helpers 
- In TypeScript, you can define query helpers using a separate query helpers interface. Mongoose's Model takes 3 generic parameters:

    The DocType
    a TQueryHelpers type
    a TMethods type

The 2nd generic parameter, TQueryHelpers, should be an interface that contains a function signature for each of your query helpers. Below is an example of creating a ProjectModel with a byName query helper.
```typescript
import { HydratedDocument, Model, QueryWithHelpers, Schema, model, connect } from 'mongoose';

interface Project {
  name?: string;
  stars?: number;
}

interface ProjectQueryHelpers {
  byName(name: string): QueryWithHelpers<
    HydratedDocument<Project>[],
    HydratedDocument<Project>,
    ProjectQueryHelpers
  >
}

type ProjectModelType = Model<Project, ProjectQueryHelpers>;

const ProjectSchema = new Schema<
  Project,
  Model<Project, ProjectQueryHelpers>,
  {},
  ProjectQueryHelpers
>({
  name: String,
  stars: Number
});

ProjectSchema.query.byName = function byName(
  this: QueryWithHelpers<any, HydratedDocument<Project>, ProjectQueryHelpers>,
  name: string
) {
  return this.find({ name: name });
};

// 2nd param to `model()` is the Model class to return.
const ProjectModel = model<Project, ProjectModelType>('Project', ProjectSchema);

run().catch(err => console.log(err));

async function run(): Promise<void> {
  await connect('mongodb://127.0.0.1:27017/test');

  // Equivalent to `ProjectModel.find({ stars: { $gt: 1000 }, name: 'mongoose' })`
  await ProjectModel.find().where('stars').gt(1000).byName('mongoose');
}
```




<br><br>



## Auto Typed Query Helpers 
- Mongoose does support auto typed Query Helpers that it are supplied in schema options. Query Helpers functions can be defined as following:
```typescript
import { Schema, model } from 'mongoose';

const ProjectSchema = new Schema({
  name: String,
  stars: Number
}, {
  query: {
    byName(name: string) {
      return this.find({ name });
    }
  }
});

const ProjectModel = model('Project', ProjectSchema);

// Equivalent to `ProjectModel.find({ stars: { $gt: 1000 }, name: 'mongoose' })`
await ProjectModel.find().where('stars').gt(1000).byName('mongoose');
```















<br><br>
<br><br>



# Populate
- Mongoose does support auto typed Query Helpers that it are supplied in schema options. Query Helpers functions can be defined as following:
```typescript
import { Schema, model, Document, Types } from 'mongoose';

// `Parent` represents the object as it is stored in MongoDB
interface Parent {
  child?: Types.ObjectId,
  name?: string
}
const ParentModel = model<Parent>('Parent', new Schema({
  child: { type: Schema.Types.ObjectId, ref: 'Child' },
  name: String
}));

interface Child {
  name: string;
}
const childSchema: Schema = new Schema({ name: String });
const ChildModel = model<Child>('Child', childSchema);

// Populate with `Paths` generic `{ child: Child }` to override `child` path
ParentModel.findOne({}).populate<{ child: Child }>('child').orFail().then(doc => {
  // Works
  const t: string = doc.child.name;
});
```

- An alternative approach is to define a PopulatedParent interface and use Pick<> to pull the properties you're populating.
```typescript
import { Schema, model, Document, Types } from 'mongoose';

// `Parent` represents the object as it is stored in MongoDB
interface Parent {
  child?: Types.ObjectId,
  name?: string
}
interface Child {
  name: string;
}
interface PopulatedParent {
  child: Child | null;
}
const ParentModel = model<Parent>('Parent', new Schema({
  child: { type: Schema.Types.ObjectId, ref: 'Child' },
  name: String
}));
const childSchema: Schema = new Schema({ name: String });
const ChildModel = model<Child>('Child', childSchema);

// Populate with `Paths` generic `{ child: Child }` to override `child` path
ParentModel.findOne({}).populate<Pick<PopulatedParent, 'child'>>('child').orFail().then(doc => {
  // Works
  const t: string = doc.child.name;
});
```

<br><br>
- Mongoose also exports a PopulatedDoc type that helps you define populated documents in your document interface:
  
## PopulatedDoc
```typescript
import { Schema, model, Document, PopulatedDoc } from 'mongoose';

// `child` is either an ObjectId or a populated document
interface Parent {
  child?: PopulatedDoc<Document<ObjectId> & Child>,
  name?: string
}
const ParentModel = model<Parent>('Parent', new Schema({
  child: { type: 'ObjectId', ref: 'Child' },
  name: String
}));

interface Child {
  name?: string;
}
const childSchema: Schema = new Schema({ name: String });
const ChildModel = model<Child>('Child', childSchema);

ParentModel.findOne({}).populate('child').orFail().then((doc: Parent) => {
  const child = doc.child;
  if (child == null || child instanceof ObjectId) {
    throw new Error('should be populated');
  } else {
    // Works
    doc.child.name.trim();
  }
});
```
- However, we recommend using the .populate<{ child: Child }> syntax from the first section instead of PopulatedDoc. Here's two reasons why:

    You still need to add an extra check to check if child instanceof ObjectId. Otherwise, the TypeScript compiler will fail with Property name does not exist on type ObjectId. So using PopulatedDoc<> means you need an extra check everywhere you use doc.child.
    In the Parent interface, child is a hydrated document, which makes it slow difficult for Mongoose to infer the type of child when you use lean() or toObject().






<br><br>
<br><br>


## Sub Documents
- Subdocuments are tricky in TypeScript. By default, Mongoose treats object properties in document interfaces as nested properties rather than subdocuments.
```typescript
// Setup
import { Schema, Types, model, Model } from 'mongoose';

// Subdocument definition
interface Names {
  _id: Types.ObjectId;
  firstName: string;
}

// Document definition
interface User {
  names: Names;
}

// Models and schemas
type UserModelType = Model<User>;
const userSchema = new Schema<User, UserModelType>({
  names: new Schema<Names>({ firstName: String })
});
const UserModel = model<User, UserModelType>('User', userSchema);

// Create a new document:
const doc = new UserModel({ names: { _id: '0'.repeat(24), firstName: 'foo' } });

// "Property 'ownerDocument' does not exist on type 'Names'."
// Means that `doc.names` is not a subdocument!
doc.names.ownerDocument();
```



Mongoose provides a mechanism to override types in the hydrated document. Define a separate THydratedDocumentType and pass it as the 5th generic param to mongoose.Model<>. THydratedDocumentType controls what type Mongoose uses for "hydrated documents", that is, what await UserModel.findOne(), UserModel.hydrate(), and new UserModel() return.
```typescript
// Define property overrides for hydrated documents
type THydratedUserDocument = {
  names?: mongoose.Types.Subdocument<Names>
}
type UserModelType = mongoose.Model<User, {}, {}, {}, THydratedUserDocument>;

const userSchema = new mongoose.Schema<User, UserModelType>({
  names: new mongoose.Schema<Names>({ firstName: String })
});
const UserModel = mongoose.model<User, UserModelType>('User', userSchema);

const doc = new UserModel({ names: { _id: '0'.repeat(24), firstName: 'foo' } });
doc.names!.ownerDocument(); // Works, `names` is a subdocument!
```

<br><br>
<br><br>


##  Subdocument Arrays
- You can also override arrays to properly type subdocument arrays using TMethodsAndOverrides:
```typescript
// Subdocument definition
interface Names {
  _id: Types.ObjectId;
  firstName: string;
}
// Document definition
interface User {
  names: Names[];
}

// TMethodsAndOverrides
type THydratedUserDocument = {
  names?: Types.DocumentArray<Names>
}
type UserModelType = Model<User, {}, {}, {}, THydratedUserDocument>;

// Create model
const UserModel = model<User, UserModelType>('User', new Schema<User, UserModelType>({
  names: [new Schema<Names>({ firstName: String })]
}));

const doc = new UserModel({});
doc.names[0].ownerDocument(); // Works!
```




















</details>






























































<br><br>
___________________________________________
___________________________________________
<br><br>


# Connection

<details><summary>Click to expand..</summary>
    
<br><br>


## Connection String
    
<br><br>

### Change db name
```typescript
private updateConnectionString() {
    const urlObj = new URL(this.connectionString)
    urlObj.pathname = `/${this.dbName}`
    const urlString = urlObj.toString()
    const newString = urlString
}
```

    
<br><br>
<br><br>

## .connect()
- Will be used for single connection

<details><summary>Click to expand..</summary>

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
</details>



<br><br>
<br><br>







## .createConnection()
- will be used for multiple connections
  - https://mongoosejs.com/docs/connections.html#multiple_connections
 
<details><summary>Click to expand..</summary>
    
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

</details>




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


</details>








































<br><br>
<br><br>
___________________________________________
___________________________________________
<br><br>
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
<br><br>
___________________________________________
___________________________________________
<br><br>
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
<br><br>
___________________________________________
___________________________________________
<br><br>
<br><br>

# Schema

<details><summary>Click to expand..</summary>



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






<details>

























<br><br>
<br><br>
__________________________________________
__________________________________________

<br><br>
<br><br>

## Plugins (https://mongoosejs.com/docs/plugins.html)
<details><summary>Click to expand..</summary>
    
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

<details>
















































































































<br><br>
___________________________________________
___________________________________________
<br><br>


# Document

<details><summary>Click to expand..</summary>
    
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


</details>











































<br><br>
___________________________________________
___________________________________________
<br><br>


# Model
<details><summary>Click to expand..</summary>
    
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


</details>























































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
<br><br>
___________________________________________
___________________________________________
<br><br>
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


# Dependencies

<br><br>

## ModelManager
- https://github.com/cybert33n/modelmanager

