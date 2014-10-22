Mail Module
================

Mail Module provides user a method to send E-mail. User sends necessary mail information to Nebula through this module, and Nebula Platform would routinely perform to send E-mails.

## SendMail
----------------

Mail Module provides `SendMail` method  

```csharp
using Quanta.PaaS.Module.Mail;
using Quanta.PaaS.Module.Kernel.Contract.Mail;

public void SendMailJob()
{
    MailContext objMail = new MailContext();
    objMail.MailData.MailType = "XXXX";
    objMail.MailData.IsProduction = "Y";
    objMail.MailData.MailFrom = "edward.lee@quantatw.com";
    objMail.MailData.MailTo = "edward.lee@quantatw.com";
    objMail.MailData.MailCc = "";
    objMail.MailData.MailBcc = "";
    objMail.MailData.MailSubject = "Subject";
    objMail.MailData.MailBody ="Body";
    objMail.MailData.Remark = "";

    string strErrorMsg;

    var mailModule = AppContext.Current.GetModule<IMailModule>();

    bool result = mailModule.SendMailJob(objMail, out strErrorMsg);
    if (result == false)
    {
        //Print Error Message
    }
}

```

The usable properties of `MailContext` and `MailData` can be described by the code below:

```csharp
namespace Quanta.PaaS.Module.Kernel.Contract.Mail
{
    public class MailContext
    {
        /// <summary>
        /// Mail content
        /// </summary>
        public MailData MailData { get; }

        /// <summary>
        /// Attachment list
        /// </summary>
        public IList<MailAttach> Attachments { get; set; }
    }

    /// <summary>
    /// Mail content information
    /// </summary>
    public class MailData
    {
        /// <summary>
        /// Sender's E-mail address
        /// </summary>
        public string MailFrom { get; set; }

        /// <summary>
        /// Sender's display name
        /// </summary>
        public string MailFromName { get; set; }

        /// <summary>
        /// Receipent E-mails
        /// </summary>
        public string MailTo { get; set; }

        /// <summary>
        /// Mail CC
        /// </summary>
        public string MailCc { get; set; }

        /// <summary>
        /// Hidden Mail CC
        /// </summary>
        public string MailBcc { get; set; }

        /// <summary>
        /// Mail Subject
        /// </summary>
        public string MailSubject { get; set; }

        /// <summary>
        /// Mail Content
        /// </summary>
        public string MailBody { get; set; }

        /// <summary>
        /// Attachment
        /// </summary>
        public string Attachments { get; set; }

        /// <summary>
        /// Whetever it is Production
        /// </summary>
        public string IsProduction { get; set; }

        /// <summary>
        /// Mail Type
        /// </summary>
        public string MailType { get; set; }

        /// <summary>
        /// Remark
        /// </summary>
        public string Remark { get; set; }
    }

}

```

## Attachment
----------------

If an E-mail needs to have attach a file, it can use `Quanta.PaaS.Module.Mail.MailAttach` object; which change the file content to byte[] and save its filename and byte[] to `MailAttach` object.

Sample code：

```csharp
using System.IO;
using Quanta.PaaS.Module.Mail;
using Quanta.PaaS.Module.Kernel.Contract.Mail;

FileStream fileStream = new FileStream(@"c:\Test.doc", FileMode.Open);
Stream file1 = fileStream;
byte[] productFile1 = new byte[file1.Length];
file1.Read(productFile1, 0, (int)file1.Length);

MailAttach objMailAttachData = new MailAttach();
objMailAttachData.AttachFileName = "Test.doc";
objMailAttachData.AttachFile = productFile1;

MailContext mailContext = new MailContext();
mailContext.Attachments.Add(objMailAttachData);
```

`Quanta.PaaS.Mail.MailAttach` properties can be described by the code below:

```csharp
namespace Quanta.PaaS.Module.Kernel.Contract.Mail
{
    public class MailAttach
    {
        /// <summary>
        /// Attachment filename
        /// </summary>
        public string AttachFileName { get; set; }

        /// <summary>
        /// Attachment content
        /// </summary>
        public byte[] AttachFile { get; set; }
    }
}
```
