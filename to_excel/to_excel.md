## DataTable to Excel

[參考資料](https://xyz.cinc.biz/2013/10/csharp-create-excel.html)  
參考資料 使用原生工具
1. 參考管理員 > COM > Microsoft Excel 版本 Object Library
2. 參考管理員 > 組件 > System.Drawing

```csharp
//// 設定儲存檔名，不用設定副檔名，系統自動判斷 excel 版本，產生 .xls 或 .xlsx 副檔名
//string pathFile = @"D:\test";

Excel.Application excelApp;
Excel._Workbook wBook;
Excel._Worksheet wSheet;
Excel.Range wRange;

// 開啟一個新的應用程式
excelApp = new Excel.Application();

// 讓Excel文件可見
excelApp.Visible = true;

// 停用警告訊息
excelApp.DisplayAlerts = false;

// 加入新的活頁簿
excelApp.Workbooks.Add(Type.Missing);

// 引用第一個活頁簿
wBook = excelApp.Workbooks[1];

// 設定活頁簿焦點
wBook.Activate();


// 引用第一個工作表
wSheet = (Excel._Worksheet)wBook.Worksheets[1];

// 命名工作表的名稱
wSheet.Name = "工作表測試";

// 設定工作表焦點
wSheet.Activate();

// 設定第1列資料
excelApp.Cells[1, 1] = "名稱";
excelApp.Cells[1, 2] = "數量";
// 設定第1列顏色
wRange = wSheet.Range[wSheet.Cells[1, 1], wSheet.Cells[1, 2]];
wRange.Select();
wRange.Font.Color = ColorTranslator.ToOle(Color.White);
wRange.Interior.Color = ColorTranslator.ToOle(Color.DimGray);

// 設定第2列資料
excelApp.Cells[2, 1] = "AA";
excelApp.Cells[2, 2] = "10";

// 設定第3列資料
excelApp.Cells[3, 1] = "BB";
excelApp.Cells[3, 2] = "20";


// 設定總和公式 =SUM(B2:B4)
excelApp.Cells[5, 2].Formula = string.Format("=SUM(B{0}:B{1})", 2, 4);

// 設定第5列顏色
wRange = wSheet.Range[wSheet.Cells[5, 1], wSheet.Cells[5, 2]];
wRange.Select();
wRange.Font.Color = ColorTranslator.ToOle(Color.Red);
wRange.Interior.Color = ColorTranslator.ToOle(Color.Yellow);

// 自動調整欄寬
wRange = wSheet.Range[wSheet.Cells[1, 1], wSheet.Cells[5, 2]];
wRange.Select();
wRange.Columns.AutoFit();   
```
其中有幾個特別的地方  
1. `讓Excel文件可見` 要看需求調整
2. `excelApp.Cells[2, 1]`  這種index定義不是從 `[0,0]` 開始
3. `設定總和公式` 這部分還沒試成功  很奇怪
4. 修改顏色跟 `System.Drawing` 有關  記得要加


---
### DataTable to Excel  

目前是土法練鋼 一個個cell自行塞值
```csharp
//set col
for (int i = 0; i < dt.Columns.Count; i++)
{
    excelApp.Cells[1, i + 1] = dt.Columns[i].ColumnName;
}

//set row
for (int i = 0; i < dt.Rows.Count; i++)
{
    for (int j = 0; j < 4; j++)
    {
        excelApp.Cells[i + 2, j + 1] = dt.Rows[i][j];
    }
    
    //Thread.Sleep(500);
}
```

---
