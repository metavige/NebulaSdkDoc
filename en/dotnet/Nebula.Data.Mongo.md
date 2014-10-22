
Nebula.Data.Mongo
================

Nebula.Data.Mongo provides MongoDB database connection function; developer then uses the offical SDK to perform database operations. The current version of offical MongoDB SDK supported by CAMP is 1.8.2.  

## MongoAdapter  
----------------

```csharp
// Connect to MongoDB database by MongoAdapter functions provided by PaaS, then use the offical 10gen MongoDB SDK to do operations.

// Use Adapter function provided by NEBULA to get the database object
var db = MongoAdapter.GetDatabase("Northwind");  

// Code
```

## Offical reference documents
-----------------

[10gen MongoDB - CSharp API tutorial](http://docs.mongodb.org/ecosystem/tutorial/use-linq-queries-with-csharp-driver/)
