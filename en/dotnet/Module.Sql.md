
SqlModule
================

Nebula 1.3 版之後，新增加一個 `Quanta.PaaS.Module.Database.ISqlModule`，主要是支援一般的 Sql 指令執行。  

目前 SqlModule 僅提供以下方法執行 Sql

## ExecuteDateSet  
----------------

```csharp
// 利用 PaaS 的 SqlModule 提供的功能，讀取資料庫的資料，顯示在畫面上
var sqlModule = AppContext.Current.GetModule<ISqlModule>();
var ds = sqlModule.ExecuteDataSet(
	@"Select * from Customers where CustomerID like @customerId",
	new SqlParam { Name = "customerId", DbType = DbType.String, Value = "test%" }
);

GridView1.DataSource = ds;
GridView1.DataBind();
```

## ExecuteScalar
----------------

```csharp
var sqlModule = AppContext.Current.GetModule<ISqlModule>();
var ds = sqlModule.ExecuteScalar(
	@"Select count(*) from Customers where CustomerID like @customerId",
	new SqlParam { Name = "customerId", DbType = DbType.String, Value = "test%" }
);

Console.WriteLine("Customer Count: {0}", ds.getInt(1));
```

## ExecuteNonQuery  
----------------

```csharp
var sqlModule = AppContext.Current.GetModule<ISqlModule>();
int effectedRows = sqlModule.ExecuteNonQuery(
    @"Update Customer Set CustomerName = @customName Where CustomerId like @customerId ",
    new SqlParam { Name = "customName", DbType = DbType.String, Value = "test" },
    new SqlParam { Name = "customerId", DbType = DbType.String, Value = "test%" }
);
```


## ExecuteBatchNoQuery
----------------

```csharp
var sqlModule = AppContext.Current.GetModule<ISqlModule>();

int[] effectedRows = sqlModule.ExecuteBatchNoQuery(
    @"Insert Into Customers(CustomerId, CustomerName) values(@customerId, @customerName)",

    new SqlParam[][] {
        new SqlParam[] {
            new SqlParam { Name = "customerId", DbType = DbType.String, Value = "id1" },
            new SqlParam { Name = "customerName", DbType = DbType.String, Value = "name1" }
        },
        new SqlParam[] {
            new SqlParam { Name = "customerId", DbType = DbType.String, Value = "id2" },
            new SqlParam { Name = "customerName", DbType = DbType.String, Value = "name2" }
        }
    }
);
```
