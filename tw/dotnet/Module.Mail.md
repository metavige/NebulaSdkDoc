Mail Module
================

Mail Module 提供使用者寄發 E-mail 的功能，使用者可以透過此 module 將郵件資訊傳送給 Nebula，Nebula Platform 將會定期執行發信動作。  

## SendMail
----------------

Mail Module 目前提供 `SendMailJob` 方法來寄送

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

目前 `MailContext` 與 `MailData` 可使用的屬性，以程式方式說明：

```csharp
namespace Quanta.PaaS.Module.Kernel.Contract.Mail
{
    public class MailContext
    {
        /// <summary>
        /// 郵件內容。
        /// </summary>
        public MailData MailData { get; }

        /// <summary>
        /// 附件清單。
        /// </summary>
        public IList<MailAttach> Attachments { get; set; }
    }

    /// <summary>
    /// 郵件內容資料。
    /// </summary>
    public class MailData
    {
        /// <summary>
        /// 郵件發送者地址。
        /// </summary>
        public string MailFrom { get; set; }

        /// <summary>
        /// 郵件發送者顯示名稱
        /// </summary>
        public string MailFromName { get; set; }

        /// <summary>
        /// 郵件寄送地址。
        /// </summary>
        public string MailTo { get; set; }

        /// <summary>
        /// 副本。
        /// </summary>
        public string MailCc { get; set; }

        /// <summary>
        /// 密件副本。
        /// </summary>
        public string MailBcc { get; set; }

        /// <summary>
        /// 郵件主旨。
        /// </summary>
        public string MailSubject { get; set; }

        /// <summary>
        /// 郵件內容
        /// </summary>
        public string MailBody { get; set; }

        /// <summary>
        /// 附件資訊。
        /// </summary>
        public string Attachments { get; set; }

        /// <summary>
        /// 是否為產品。
        /// </summary>
        public string IsProduction { get; set; }

        /// <summary>
        /// 郵件類型
        /// </summary>
        public string MailType { get; set; }

        /// <summary>
        /// 備註。
        /// </summary>
        public string Remark { get; set; }
    }

}

```

## Attachment
----------------

若郵件需要附帶檔案，需要透過 `Quanta.PaaS.Module.Mail.MailAttach` 物件，將要上傳的檔案轉換成 byte[] ，並將檔案名稱與 byte[] 內容放入 `MailAttach` 物件中。  

範例程式：

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

`Quanta.PaaS.Mail.MailAttach` 的屬性，以程式方式說明:

```csharp
namespace Quanta.PaaS.Module.Kernel.Contract.Mail
{
    public class MailAttach
    {
        /// <summary>
        /// 附件檔案名稱。
        /// </summary>
        public string AttachFileName { get; set; }

        /// <summary>
        /// 附件檔案內容。
        /// </summary>
        public byte[] AttachFile { get; set; }
    }
}
```
