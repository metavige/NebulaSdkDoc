
SqlModule
================
After Version 1.3, Nebula adds an `Quanta.PaaS.Module.Database.ISqlModule`, which is to support ordinary Sql commands.  

SqlModule currently provides the following functions to perform Sql:  

## ExecuteDateSet  
----------------

```csharp
// Read database data and display on screen provie by PaaS SqlModule function
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
