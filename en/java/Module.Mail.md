Mail Module
================

Mail Module provides users functions send E-mail. E-mail contents are delivered to NEBULA through this module, and NEBULA platform would periodically send them.  

## Send Mail
---------------

Module currently provides `Send` function to send mails  

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

The current avaiable Message properties are described below:  

```java
public class Message {

	// Sender's email address
	private InternetAddress from;

	// Recipients' email addresses
	private List<InternetAddress> to;

	// CC
	private List<InternetAddress> cc;

	// BCC
	private List<InternetAddress> bcc;

	// Mail Type
	private String type;

	// Mail Subject
	private String subject;

	// Mail Body
	private String body;

	// Status
	private String status;

}

```

## Attachment
----------------

If you need to attach a file in an email, you need to convert the file to byte[] through `com.quanta.nebula.module.Attachment` object. Put the file name and the byte[] content into the `Attachment` object.

Sample Codes:  

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

`com.quanta.nebula.module.Attachment` properties, described below:  

```java
public class Attachment {

	// Attached file name
	private String fileName;

	// Attached file content
	private byte[] data;

}
```
