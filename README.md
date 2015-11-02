![Fruit](https://github.com/nodefruit/fruit/raw/master/pres/logo.png)

## Table of Contents

- [Introduction](#introduction)
- [Install](#install)
- [How does it work](#how-does-it-work)
- [Contributing](#contributing)
- [Community](#community)

## Introduction

This is a NodeJS ORM for many databases manipulations. This is just an experimental version, once it's tested and well maintained by its community, you can start using it on production. 
Feel free to [contribute](#contributing) in order to give birth to this awsome project.

## Install

The Fruit package can't be installed on its own, you need to select one (or many) of the adapters available depending on the database type you are using.

#### Available adapters
- [fruit-mongodb](http://npmjs.com/package/fruit-mongodb)
- [fruit-mysql](http://npmjs.com/package/fruit-mysql)
- [fruit-postgresql](http://npmjs.com/package/fruit-postgresql)
- ... [Help us by creating other adapters](#contributing)

To use Fruit with mongodb for example, you can install both fruit and fruit-mongodb.
example :

```bash
$ npm install fruit fruit-mongodb
```

## How does it work

First of all you need to require both the fruit module and the adapter, let's use as example fruit-mongodb

```javascript
  var Fruit   = require('fruit')
    , adapter = require('fruit-mongodb');
```

Then you need to instaciate the fruit object
```javascript
  var fruit = new Fruit(mongodbAdapter);
```

#### Connection

To test the connection to the database, you need to pass options as arguments. Those options are the information needed to get connected to the database. You need to check documentation of the adapter of your choice.

```javascript
  fruit.connect(options)
    .success(successCallBack)
    .error(errorCalBack);
```
You can also specify your options without testing the connection to the database
```javascript
  var fruit = new Fruit(adapter).config(options);
  
  // or you can do this
  
  var fruit = new Fruit(adapter.config(options));
```

#### Inserting data

To insert some data, you can use the `.insert()` method:

```javascript
  function successCallBack (results) {
    console.log(results);
  }

  function errorCallBack (error) {
    console.log(error);
  }
  
  fruit.insert({ name: 'Khalid', age: 26 })
    .success(successCallBack)
    .error(errorCallBack);
```
If the data was successfully inserted, the `results` passed as argument to the `successCallBack` would be like this:

```javascript
  {
      result : {
          success       : true
        , affectedCount : 1
        , count         : 1
      }
    , insertedId : [1] // id of the inserted row ( _id for mongodb )
  }
```

You can also insert many rows at the same time

```javascript
  var collectionName = 'users'
    , data = [
        { name: 'Khalid', age: 26 }
      , { name: 'Ahmed', age: 29 }
    ];
  
  fruit.insert(data)
    .into(collectionName)
    .success(successCallBack)
    .error(errorCallBack);
```

#### Selecting data

To look for data, you may need to call one of the methods `.find()`, `.findOne()`, `.findAll()`

##### `.find()`: 

This method allows you to look for data that fulfill the condition specified

```javascript
  var collectionName  = 'users'
    , condition       = { name: 'Khalid' };
  
  fruit.find(condition)
    .from(collectionName)
    .success(successCallBack)
    .error(errorCallBack);
```

The data found and passed as argument to the success callBack will be an array of models created using [fishbone](http://npmjs.com/package/fishbone). Each model has the columns as attributes and useful methods.

The methods are :
- `.print()`  : It prints the model as JSON on the console using the package [jsome](http://npmjs.com/package/jsome).
- `.save()`   : It updates changes made on the model directly to the database. It returns a promise.
- `.delete()` : It deletes the concerned row from the database. It returns a primise.
- `.toJSON()` : It converts results to JSON.

examples :

If you need to find something to update it or delete it, you may need to use the methods [`.update()`](#update) and `.detele()`

```javascript
  // using .save() method
  fruit.find({ name : 'Khalid' })
    .from('users')
    .success(function (results) {
      results[0].name; // 'Khalid'
      results[0].age = 30;
      results[0].save()
        .success(successCB)
        .error(errorCB)
    });
    
  // using .delete() method
  fruit.find({ name : 'Khalid' })
    .from('users')
    .success(function (results) {
      results[0].name; // 'Khalid'
      if(results[0].age == 30) {
        results[0].delete()
          .success(successCB)
          .error(errorCB)
      }
    });
```

You also can specify an offset and a limit :

```javascript
  var collectionName  = 'users'
    , condition       = { name: 'Khalid' };
  
  fruit.find(condition)
    .from(collectionName)
    .offset(5)
    .limit(10)
    .success(successCallBack)
    .error(errorCallBack);
```

##### `.findOne()`: 

This method is exactly like [`.find()`](#find) but it returns only one model, not an array. The only difference on its usage, is that it can't be combined with offset and limit.

##### `.findAll()`: 

This method, finds all take any condition, it returns all data of a table or collection.

```javascript
  fruit.findAll('users')
    .success(successCallBack)
    .error(errorCallBack);
```

#### Counting data

To count rows fulfilling a condition, you can use the `.count()` method

```javascript
  fruit.count('users')
    .where({ name : 'Khalid' })
    .success(function (count) {
      console.log(count);
    })
```
To count all the rows on a table, you can call it without `.where()` method

```javascript
  fruit.count('users')
    .success(function (count) {
      console.log(count);
    })
```

#### Updating data

There are two methods to update data, [`.update()`](#update) and [`.updateAll()`](#updateall). The difference between them is that [`.update()`](#update) method, updates only one row, and the [`.updateAll()`](#updateall) updates many.
On MySQL when you run an update query without condition, you are updating all the rows. Fruit reduces the damage of day dreaming developers. If you need to update many rows, you actually need to type updateAll.

##### `.update()`:

Updating one row :

```javascript
  fruit.update('users')
    .set({ age : 30 })
    .where({ name : 'Khalid' })
    .success(successCallBack)
    .error(errorCallBack)
```
You can also call update without `.where()` method.

```javascript
  fruit.update('users')
    .set({ age : 30 })
    .success(successCallBack)
    .error(errorCallBack)
```

##### `.updateAll()`:

Updating many rows :

```javascript
  fruit.updateAll('users')
    .set({ age : 30 })
    .where({ name : 'Khalid' })
    .success(successCallBack)
    .error(errorCallBack)
```
You also can use it without `.where()` method.

## Contributing

All contributions are welcome. Let's get this project to the next level.
Significant and valuable contributions will allow you to be part of [Fruit organisation](http://github.com/nodefruit).
See the [contribution guide](http://github.com/nodefruit/fruit/blob/master/CONTRIBUTING.md) for more details

## Community

If you'd like to chat and discuss this project, you can find us here:

- Mailing list: https://groups.google.com/forum/#!forum/nodefruit
- [![Join the chat at https://gitter.im/nodefruit/fruit](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/nodefruit/fruit?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)