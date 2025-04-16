## using Dapper  and  auto make SQL_str
前面說到  SQL_str 要自己湊  還是很煩很蠢
```csharp
string sql = @"INSERT INTO PAM4_FinalTest (Lot, SN, Channel, TestTime, DDMVolt, DDMTemp) 
            VALUES (@Lot, @SN, @Channel, @TestTime, @DDMVolt, @DDMTemp)";
//只是col多  看起來就像個災難
```
目前看到最好的寫法 就是  
自己寫小工具產生 SQL（自動反射）  
這方法的目的是：你寫一個小工具，自動把一個 class 轉成 insert 語法，避免手刻  

### 前提是你必須要有一個針對 DBtable的 class 如下
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
### 這已算是  最低限定的作法了 反正 這個類別 也能幫助型別確認

這是自動湊 SQL_str的 方法
```csharp
public static string GenerateInsertSQL<T>(string tableName)
        {
            var props = typeof(T).GetProperties();  // 取得類別 T 的所有 public 屬性

            var columns = string.Join(", ", props.Select(p => p.Name));
            // ↑ 把所有屬性名稱，用逗號+空格 ", " 串接起來
            //   例如屬性有 Lot, SN, Channel，結果就是 "Lot, SN, Channel"

            var values = string.Join(", ", props.Select(p => "@" + p.Name));
            // ↑ 每個屬性前加上 @ 符號，變成 SQL 用的參數名稱
            //   結果會是 "@Lot, @SN, @Channel"

            return $"INSERT INTO {tableName} ({columns}) VALUES ({values})";
            // ↑ 組成完整的 INSERT 指令
        }
```

### 實際使用
```csharp
private void btn_insert_Click(object sender, EventArgs e)
{
    string connStr = "uid=xxx;pwd=xxx;database=FormericaOE;server=dataserver";

    var data = new Pam4FinalTestRecord
    {
        //以下為key
        Lot = txt_lot.Text,
        SN = txt_sn.Text,
        Channel = int.Parse(txt_ch.Text),
        TestTime = DateTime.Now,

        //以下為測試 數值填入
        DDMVolt = (float)3.6,
        //DDMTemp = 25
    };

    string sql = GenerateInsertSQL<Pam4FinalTestRecord>("PAM4_FinalTest");

    using (var conn = new SqlConnection(connStr))
    {
        conn.Open();
        conn.Execute(sql, data);    //這是dapper功能
        MessageBox.Show("Insert 成功");
    }
}
```