Log Module
================

Log Module provides users function to record Error Log.  
Error Log data would be saved in Nebula Cloud Platform database.
Currently users can query Log records through the [Developer Area](http://www.quanta-camp.com/Developer/).  

Please follow the instructions below if you want to use the Logging Module functions:

## Error Log
----------------

You need to use `ExceptionHandler` if you want to record Error Log.  

### Examples

Use try-catch to handle errors  

```java
try {

	List<Object> l = new ArrayList<Object>();
	l.get(9);

} catch (Exception e) {

	ExceptionHandler.getInstance().Handle(e);
}
```
