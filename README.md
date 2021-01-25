# IndexedDBasyncEZ
Async implementation of IndexedDB (IDBDatabase) using promises. Comes with a simple schema implementation that makes defining database and store structure much easier. Use IndexedDB with no upgrades, requests, or transactions and while staying out of callback hell! Great for prototyping or small projects where you need some persistence and localStorage just won't do.

## Overview
* Exposes two classes **Schema** and **LocalDatabase**.
* You can use **Schema** to version and define the structure of your database.
* You can use **LocalDatabase** instances to represent named databases and access the main IndexedDB functionality. 
* Just make sure to initialize your database instances with a schema before you try to use them!


# How to Use
## Install
Very simple, just get a copy of the LocalDatabase.js file and add it to your project directory. Then import the classes in the file where you want to use them like below:

```javascript
import { LocalDatabase, Schema } from '<path to LocalDatabase.js>';
```

## Create a Schema
First instantiate a new **Schema** object and pass in the version that this schema represents. You can then make calls to **setStore** on the schema instance to define new stores. See the examples below:

```javascript
// Create a new schema with a version of 1
const schema = new Schema(1);

// Define a store called "Files"
// keyPath option is used to store the file by its "name" property
// autoincrement is not currently supported
schema.setStore('Files', {keyPath:'name'});

// Maybe define a store for dom elements?
// Store them by id
schema.setStore('Elements', {keyPath:'id'});

// Store definitions are like handles and can be accessed with .stores.<StoreName>
// Use these handles later to access the stores they correspond to in the database
// This is a handle for the recently defined Files store
schema.stores.Files
```

* It's important NOT to add or remove any calls to **setStore** after you have initialized a database with a schema. Once you have initialized a database with a schema it will ignore any schemas with the same version. In this case change the version, database name, or delete the old database using developer tools in your browser.

## Create a Database
Make sure you have created a schema and versioned it appropiately as described above. Then instantiate a new **LocalDatabase** object either in an **async** function or somehwere that can be referenced from one. Make sure to pass a name, this will uniqely identify this database. Instantiating a database with the same name as one that already exists will link to that database instead of creating it. This will cause problems, as mentioned before, if your schema has changed but the version stays the same. Just call **initialize** on the database instance and pass in the schema. The database is now ready for use. See the examples below:

```javascript
// Creates db called "Files&Elements" in IndexedDB if it doesn't exist
const FilesAndElementsDB = new LocalDatabase('Files&Elements');

async function start(){
  // Pass in the schema object
  // This will create all the stores and structure for the database ONLY...
  // ...if the version number is new or a db of that name and version doesn't exist
  // Link to db with that name and version if it does
  await FilesAndElements.initialize(schema);
  // You don't necesarilly have to use async/await
  // All db functions return a promise, feel free to use .then
  // It's ill-advised to not use one of them...
  // Especially if you are going to try and access the db right after initialization!
}
```
