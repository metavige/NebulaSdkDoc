
Nebula.Data.Mongo
================

Nebula.Data.Mongo 協助提供連接 MongoDB 資料庫，開發者再使用官方 SDK 來操作資料庫。目前支援的官方 SDK 為 1.8.2。

## MongoAdapter  
----------------

```csharp
// 利用 PaaS 的 MongoAdapter 提供的功能，連接到 MongoDB，使用 10gen 所提供的 MongoDB SDK 來操作資料庫

// 使用NEBULA提供的連接 Adapter 取得資料庫物件
var db = MongoAdapter.GetDatabase("Northwind");  

// Code
```

## 官方 API 參考文件
-----------------

[10gen MongoDB - CSharp API教學](http://docs.mongodb.org/ecosystem/tutorial/use-linq-queries-with-csharp-driver/)
