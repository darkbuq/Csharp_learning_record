## using Dapper
當資料表欄位很多（像你這段程式碼中就有 40 多個欄位），又要做大量插入的時候
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