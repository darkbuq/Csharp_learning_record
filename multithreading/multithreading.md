
# C#   多執行緒 配合委派 刷新UI
### 理解如下    
1 增加一個額外的執行緒   讓部分程式碼在這個執行緒 上跑  
2 這額外的執行緒   跑的內容如果有關UI刷新  
就做一個函數來刷新UI   +  一個委派    讓委派跑函數刷新UI 

---
### 第一種寫法  純粹寫 不偷加 其他技巧
```csharp
private void btn_start_Click(object sender, EventArgs e)
{
    thr = new Thread(ShowMessage);//裡面有個迴圈  在背景執行緒跑
    thr.Start();
}

private delegate void delegate1(int sMessage);//宣告一個委派
int i = 0;
private void ShowMessage() //這個函數 在另外的執行緒上跑
{
    delegate1 dd1 = new delegate1(refreshUI);//生成一個委派
    while (true)
    {
        this.Invoke(dd1, i);

        Thread.Sleep(1000);
        i++;
    }
}

private void refreshUI(int numm)
{
    textBox2.Text = numm.ToString();
}

private void btn_stop_Click(object sender, EventArgs e)
{
    thr.Abort();
}
```
---
### 但是   
如果背景的執行緒  有很多部位  需要刷新UI  
那就要做很多   刷新UI的函數出來

`每個不同時間段 刷新的UI`  
1. `不同的宣告委派` 
2. 再來 `new委派`  
3. 還需對映 `不同的函數刷新UI`

---  
### 多執行緒 委派到主執行緒 多部位刷新UI  如何寫.....
#### 我問chatGPT的
#### 以下是一些可能的方法，可以幫助組織和管理刷新 UI 的程式碼：

>區分不同的刷新類型：  
>根據不同的刷新類型，可以將相關的刷新 UI 的程式碼分組成不同的函數。  
>例如，可以有一個函數專門處理`TextBox`的刷新，另一個函數專門處理`label`的刷新

#### 不同的刷新類型  那就需要宣告  各種的委派

>根據不同的刷新類型將刷新 UI 的程式碼分組成不同的函數  
>可以使用委派（Delegate）來達到這個目的  
>委派可以用來封裝一個特定類型的方法，並允許將方法作為參數傳遞、存儲和調用。


例如  
>假設你有一個委派類型 `TextBox_Delegate` 和 `label_Delegate`  
>分別用於處理文本框和進度條的刷新。你可以定義這些委派如下：

```csharp
//宣告 各種可能要用的委派   
public delegate void TextBox_Delegate(string text);
public delegate void label_Delegate(Color color);

//然後，你可以定義對應的刷新函數，並將它們分配給相應的委派：

// 分配刷新函數給委派   下面 兩種寫法都行
TextBox_Delegate Delegate1 = new TextBox_Delegate(Update_TextBox);
label_Delegate Delegate2 = Update_label;

public void Update_TextBox(string text)
{
    txt_note.Text=text;
}

public void Update_label(Color color)
{
    label_5.BackColor = color;
}
```
### 第二種寫法  new委派時 有新的寫法 更直覺
```csharp
TextBox_Delegate Delegate1 = new TextBox_Delegate(Update_TextBox);
label_Delegate Delegate2 = Update_label; //第二種寫法
```

---
### 第三種  使用了 匿名  

```csharp
private void button1_Click(object sender, EventArgs e)
{
    Thread thr = new Thread(realtime_RSSI);
    thr.Start();
}

private void realtime_RSSI()
{
    while (true)
    {
        string readStr = "A1F1A2F2A3F3A4F4A5F5A6F6A7F7A8F8";
        List<string> cut8 = new List<string>();
        for (int i = 0; i < 8; i++)
        {
            cut8.Add(readStr.Substring(i * 4, 4));
        }

        //不用寫 宣告委派   用C#內建現成的
        //不用寫 刷新函數  要怎樣刷新 直接寫在後面 直接用
        Action<List<string>> dgv_Update_Delegate3 = delegate (List<string> read_Hex)
        {
            for (int i = 0; i < 8; i++)
            {
                string revert_str = read_Hex[i].Substring(2, 2) + read_Hex[i].Substring(0, 2);
                double mV = (float)Convert.ToInt32(revert_str, 16) * 2500 / 4096;
                double uA = mV / 2.4 * 4;

                dgv_Rx_RSSI.Rows[i].Cells[1].Value = mV.ToString("0.00");
                dgv_Rx_RSSI.Rows[i].Cells[2].Value = uA.ToString("0.00");
            }
        };
        this.Invoke(dgv_Update_Delegate3, cut8);

        Thread.Sleep(1000);
    }
}
```
---
### Action<int> 是一個 C# 原生 現成 委派 (delegate) 
#### 這種東西有很多嗎
C# 中有許多內建的委派類型，用於表示不同類型的方法  
以下是一些常見的委派類型：

1. `Action`: 表示不返回任何值的方法。可以帶有零到多個參數。
2. `Func`: 表示返回值的方法。最後一個類型參數表示返回值的類型，前面的參數表示方法的參數。
3. `Predicate`: 表示返回布林值的方法，通常用於進行條件判斷。
4. `EventHandler`: 用於處理事件的方法，接受兩個參數，第一個是事件的發送者，第二個是事件的參數。
5. `Comparison`: 用於比較兩個對象的方法，返回一個表示比較結果的整數值。

除了這些內建的委派類型，你還可以自定義自己的委派類型，以滿足特定的需求。委派類型可以提高代碼的可讀性和靈活性，並促進代碼的重用性和模塊化設計。