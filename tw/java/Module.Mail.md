Mail Module
================

Mail Module 提供使用者寄發 E-mail 的功能，使用者可以透過此 module 將郵件資訊傳送給 Nebula，Nebula Platform 將會定期執行發信動作。

## Send Mail
---------------

Mail Module 目前提供 `Send` 方法來寄送

```java
StringBuffer buf = new StringBuffer();

final Message reqModel = new Message();

List<InternetAddress> MailToList = new ArrayList<InternetAddress>();

try {
	MailToList.add(new InternetAddress("xxx@gmail.com"));
} catch (AddressException e) {
	e.printStackTrace();
}

reqModel.setFrom(new InternetAddress("xxx@quantaw.com", "xxx"));
reqModel.setTo(MailToList);
reqModel.setSubject("simple test mail from nebulacontext [Java]");
reqModel.setBody("Hello Java");

AppContext appContext = WebAppContext.getCurrentAppContext();

MailModule mailModule = appContext.getModule(MailModule.class);
boolean returnModel = mailModule.send(reqModel);

```

目前 Message 可使用的屬性，以程式方式說明：

```java
public class Message {

	// 郵件發送者地址
	private InternetAddress from;

	// 郵件寄送地址
	private List<InternetAddress> to;

	// 副本
	private List<InternetAddress> cc;

	// 密件副本
	private List<InternetAddress> bcc;

	// 郵件類型
	private String type;

	// 郵件主旨
	private String subject;

	// 郵件內容
	private String body;

	// 狀態
	private String status;

}

```

## Attachment
----------------

若郵件需要附帶檔案，需要透過 `com.quanta.nebula.module.Attachment` 物件，將要上傳的檔案轉換成 byte[] ，並將檔案名稱與 byte[] 內容放入 `Attachment` 物件中。  

範例程式：

```java
File file = new File(MailModuleTest.class.getResource("test.zip").toURI());

byte[] data = getBytesFromFile(file);

Attachment attachment = new Attachment();
attachment.setFileName("test.zip");
attachment.setData(data);

List<Attachment> attachmentList = new ArrayList<Attachment>();
attachmentList.add(attachment);

AppContext appContext = WebAppContext.getCurrentAppContext();

MailModule mailModule = appContext.getModule(MailModule.class);

boolean returnModel = mailModule.send(reqModel, attachmentList);
```

`com.quanta.nebula.module.Attachment` 的屬性，以程式方式說明:

```java
public class Attachment {

	// 附件檔案名稱
	private String fileName;

	// 附件檔案內容
	private byte[] data;

}
```
