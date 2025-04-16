## using Dapper
當資料表欄位很多（像這段程式碼中就有 40 多個欄位），又要做大量插入的時候
Dapper 就非常適合，可以用物件對應欄位，自動幫你 map，程式會簡潔很多

---

```csharp
public class Pam4FinalTestRecord//目前只示範 key塞入  忽略過多欄位
{
    //以下為key
    public string Lot { get; set; }
    public string SN { get; set; }
    public int Channel { get; set; }
    public DateTime TestTime { get; set; }

    //以下為測試 數值填入
    public float? DDMVolt { get; set; }//float? 就是沒賦值時  會自動null
    public float DDMTemp { get; set; }//float 就是沒賦值時  會自動0
}
```
這種打法   在VS2019  有沒有快速鍵可以用  
不然  set  get很多

使用 prop 快速片段（code snippet） 在 class 裡輸入：
```csharp
prop + Tab + Tab
```
---
### 整體主程式 如何使用
```csharp
private void btn_insert_Click(object sender, EventArgs e)
{
    string connStr = "uid=sa;pwd=dsc;database=FormericaOE;server=dataserver";

    var data = new Pam4FinalTestRecord
    {
        //以下為key
        Lot = txt_lot.Text,
        SN = txt_sn.Text,
        Channel = int.Parse(txt_ch.Text),
        TestTime = DateTime.Now,

        //以下為測試 數值填入
        DDMVolt = (float)3.6

        //DDMTemp = 25 
        //故意沒賦值 如果宣告為float? 會自動null  
        //如果宣告為 float 會自動填0  
    };

    string sql = @"INSERT INTO PAM4_FinalTest (Lot, SN, Channel, TestTime, DDMVolt, DDMTemp) 
            VALUES (@Lot, @SN, @Channel, @TestTime, @DDMVolt, @DDMTemp)";
    //使用dapper 仍需要自行處理這部分

    using (var conn = new SqlConnection(connStr))
    {
        conn.Open();
        conn.Execute(sql, data);    //這是dapper功能
        MessageBox.Show("Insert 成功");
    }
}
```
---
### 使用dapper 如何select 
```csharp
private void btn_query_Click(object sender, EventArgs e)
{
    string connStr = "uid=xxx;pwd=xxx;database=FormericaOE;server=dataserver";
    
    string sql = "SELECT * FROM PAM4_FinalTest WHERE SN = @SN";

    using (var conn = new SqlConnection(connStr))
    {
        conn.Open();

        var result = conn.Query<Pam4FinalTestRecord>(sql, new { SN = txt_sn.Text }).ToList();
        //Query<T>() 的意思是：「我預期從資料庫回傳的結果，要對應到哪一個 C# 類別。」
        //這裡的 T 是 Pam4FinalTestRecord，
        //所以 Dapper 會自動幫你把每一列資料，轉成一個 Pam4FinalTestRecord 物件。

        //`new { SN = txt_sn.Text }` 這是一個「匿名物件」，用來提供 SQL 中的參數值
        //意思是：讓 @SN 對應到 txt_sn.Text 的內容

        //Query<>() 回傳的其實是一個 IEnumerable<T>（類似資料流）
        //可以用 .ToList() 把它轉成 List<T>，比較方便用索引、Count 等功能

        if (result.Count > 0)
        {
            var record = result[0];
            MessageBox.Show($"Lot: {record.Lot}, Channel: {record.Channel}, Volt: {record.DDMVolt}");
        }
        else
        {
            MessageBox.Show("找不到資料");
        }

        dgv_result.DataSource = result;
        //DataGridView 支援直接綁定 List<T>，會自動把每個屬性變成欄位
    }
}
```
