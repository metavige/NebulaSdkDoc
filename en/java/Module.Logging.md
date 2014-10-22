Log Module
================

Log Module 提供使用者記錄 Error Log 的功能  
Error Log 資料將會記錄在 Nebula CloudPlatform 的資料庫中。  
目前使用者可以透過 [Quanta CAMP 網站](http://www.quanta-camp.com/) 上 [開發者專區](http://www.quanta-camp.com/Developer/) 的紀錄查詢功能來查詢 Log 紀錄。  
若需要使用 Logging Module 功能，請依照以下步驟說明進行操作  

## Error Log
----------------

若要記錄Error Log, 需透過 `ExceptionHandler` 來記錄。

### 使用範例

使用 try-catch 自行處理錯誤

```java
try {

	List<Object> l = new ArrayList<Object>();
	l.get(9);

} catch (Exception e) {

	ExceptionHandler.getInstance().Handle(e);
}
```
