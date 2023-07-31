## 最像excel的 DataTable


 ```csharp
 public Form1()
{
    InitializeComponent();
}

private void button1_Click(object sender, EventArgs e)
{
    // 創建 DataTable
    DataTable dataTable = new DataTable();
    dataTable.Columns.Add("Name", typeof(string));
    dataTable.Columns.Add("Age", typeof(int));
    // 其他欄位...

    // 新增資料列
    dataTable.Rows.Add("John", 30);
    dataTable.Rows.Add("Jane", 25);

    //
    dataGridView1.DataSource = dataTable;
    //dataGridView1.Rows[1].Cells["Name"]
    //
    for (int i = 0; i < dataTable.Rows.Count; i++)
    {
        textBox1.Text += dataTable.Rows[i]["Name"]+"\r\n";
    }

}
```

### 指定位置的訪問
```
dataTable.Rows[1]["Name"].ToString()  
```
 