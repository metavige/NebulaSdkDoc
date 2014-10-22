
Office Module
================

The following describes Office Module function and implementation. Office Module currently provides Excel file based on Open XML format (.xlsx). Please follow the instruction steps to code.

## Excel implemenation
-----------------

* Must reference `Quanta.PaaS.Module.Office.dll` and `DocumentFormat.OpenXml.dll`(Open XML SDK 2.0) assemblies first.
* Detailed specification document is QuantaCloudPlatformSDK.chm.
* Use the following namespace

```csharp
using DocumentFormat.OpenXml;
using DocumentFormat.OpenXml.Packaging;
using DocumentFormat.OpenXml.Spreadsheet;
using Quanta.PaaS.Module.Office.Excel;
```

Office Module currently provides the following functions:

* ExcelFileRead - Read Excel file.
* ExcelFileWrite - Write Excel file.
* ExcelSheetRead - Read Excel sheet.
* ExcelSheetWrite - Write Excel sheet.
* ExcelFileStyle - Set Excel format.

## Sample code
-----------------

### Read an Excel file data

```csharp
MemoryStream stream = ExcelFileRead.StreamFromFile(@"c:\template_data.xlsx");
SpreadsheetDocument doc = SpreadsheetDocument.Open(stream, true);
WorksheetPart worksheetPart = ExcelFileRead.GetWorksheetPartByName(doc, "Sheet1");

DataTable dtData = ExcelSheetRead.ReadData(doc, worksheetPart, "A2", "C4");
```

### Write data into an Excel file

```csharp
DataTable dtData = new DataTable();
Int32 rowCount = 100;
Int32 columncount = 20;

for (int i = 0; i < columncount; i++)
{
	dtData.Columns.Add("Column" + i.ToString());
}

Random rnd = new Random();
for (Int32 i = 0; i < rowCount; i++)
{
	DataRow dr = dtData.NewRow();
	for (int y = 0; y < columncount; y++)
	{
		dr["Column" + y.ToString()] = rnd.Next(100000).ToString();
	}
	dtData.Rows.Add(dr);
}
dtData.AcceptChanges();

MemoryStream stream = ExcelFileRead.StreamFromFile(@"c:\test.xlsx");
SpreadsheetDocument doc = SpreadsheetDocument.Open(stream, true);
WorksheetPart worksheetPart = ExcelFileRead.GetWorksheetPartByName(doc, "Sheet1");
ExcelSheetWrite writer = new ExcelSheetWrite(doc, worksheetPart);

//style
ExcelFileStyle style = ExcelFileRead.GetDefaultStyle(doc);
style.IsBold = true;
style.IsItalic = true;
style.IsWrapped = true;
style.SetColor("FF0000");
style.SetBackgroundColor("DDDDDD");
style.SetBorder("00FF00", BorderStyleValues.Medium);
style.SetHorizontalAlignment(HorizontalAlignmentValues.Center);

//Write Data
writer.PasteDataTable(dtData, "A2", style);

//Save File
ExcelFileWrite.Save(doc);
ExcelFileWrite.StreamToFile(@"c:\test_new.xlsx", stream);
```

### Copy format within an area and write data into an Excel file

```csharp
DataTable dtData = new DataTable();
Int32 rowCount = 100;
Int32 columncount = 20;

for (int i = 0; i < columncount; i++)
{
 dtData.Columns.Add("Column" + i.ToString());
}

Random rnd = new Random();
for (Int32 i = 0; i < rowCount; i++)
{
	DataRow dr = dtData.NewRow();
	for (int y = 0; y < columncount; y++)
	{
		dr["Column" + y.ToString()] = rnd.Next(100000).ToString();
	}
	dtData.Rows.Add(dr);
}
dtData.AcceptChanges();

MemoryStream stream = ExcelFileRead.StreamFromFile(@"c:\test.xlsx");
SpreadsheetDocument doc = SpreadsheetDocument.Open(stream, true);
WorksheetPart worksheetPart = ExcelFileRead.GetWorksheetPartByName(doc, "Sheet1");
ExcelSheetWrite writer = new ExcelSheetWrite(doc, worksheetPart);

//Copy (A2:C4) format to A5 and then fill data from A2 (the format will be copied circularly according to the rows of data)
writer.FillDataTable(dtData, "A2", "A2", "C4", "A5");

//Save File
ExcelFileWrite.Save(doc);
ExcelFileWrite.StreamToFile(@"c:\test_new.xlsx", stream);
```
