
# C#   多執行緒的寫法   我初步學會了  
我的理解如下    
1 增加一個額外的執行緒   讓部分程式碼在這個執行緒 上跑  
2 這額外的執行緒   跑的內容如果有關UI刷新  就做一個函數來刷新UI   +  一個委派    讓委派跑函數刷新UI  

```csharp
private void btn_start_Click(object sender, EventArgs e)
{
    thr = new Thread(ShowMessage);//裡面有個迴圈  在背景執行緒跑
    thr.Start();
}

int i = 0;
private void ShowMessage() //這個函數 在另外的執行緒上跑
{
    DelShowMessage del = new DelShowMessage(AddMessage);//生成一個委派
    while (true)
    {
        //UI控制全部移去委派做
        //textBox2.Text = i.ToString();
        //textBox1.Text = DateTime.Now + Environment.NewLine;
        //UI控制全部移去委派做

        this.Invoke(del, i);
        //this.Invoke(printNumber, i);

        Thread.Sleep(1000);
        i++;
    }
}

private delegate void DelShowMessage(int sMessage);//宣告一個委派
private void AddMessage(int ii)
{
    textBox2.Text = ii.ToString();
    textBox1.Text += DateTime.Now + Environment.NewLine;
}
```

---  
# 但是   
如果我要放在背景的執行緒  
有很多部位  需要刷新UI  
那我不就要做很多   刷新UI的函數出來

這真的是高手的做法嗎  

---  

如果在背景執行緒中有多個地方需要刷新 UI，確實可以將相關的刷新 UI 的程式碼分成多個函數  
這樣可以更好地組織和管理程式碼，使其更具可讀性和可維護性。

以下是一些可能的方法，可以幫助組織和管理刷新 UI 的程式碼：

1. 區分不同的刷新類型：根據不同的刷新類型，可以將相關的刷新 UI 的程式碼分組成不同的函數。例如，可以有一個函數專門處理文本框的刷新，另一個函數專門處理進度條的刷新，以此類推。

2. 使用參數化函數：如果刷新 UI 的程式碼之間存在共用的邏輯，可以考慮使用參數化的函數。這樣可以通過參數來指定需要刷新的 UI 元素和相關的資料，從而減少重複的程式碼。

3. 封裝刷新邏輯：可以將刷新 UI 的程式碼封裝在獨立的類別或方法中，並根據需要調用相應的刷新函數。這樣可以將刷新邏輯集中管理，並提供統一的接口。

雖然有多個刷新 UI 的函數可能會增加一些程式碼的數量，但它有助於提高代碼的可讀性和可維護性。這樣可以更清晰地理解每個刷新操作的目的和邏輯，並且可以更容易地進行修改和擴展。

最終的設計取決於你的具體需求和項目的複雜性。有時，一個統一的刷新函數可能已足夠應對需求；而在其他情況下，多個刷新函數可以更好地組織和管理程式碼。


區分不同的刷新類型：根據不同的刷新類型  
可以將相關的刷新 UI 的程式碼分組成不同的函數  
例如，可以有一個函數專門處理文本框的刷新  
另一個函數專門處理進度條的刷新，以此類推

這一段    不同的刷新類型  那就需要宣告  各種的委派   沒錯吧


對的，如果要根據不同的刷新類型將刷新 UI 的程式碼分組成不同的函數，可以使用委派（Delegate）來達到這個目的。委派可以用來封裝一個特定類型的方法，並允許將方法作為參數傳遞、存儲和調用。

在這種情況下，你可以定義多個不同的委派類型，每個類型對應一個特定的刷新類型。然後，你可以定義對應的刷新函數，並將它們分配給相應的委派。

例如  
假設你有一個委派類型 `TextBoxUpdateDelegate` 和 `ProgressBarUpdateDelegate`  
分別用於處理文本框和進度條的刷新。你可以定義這些委派如下：

```csharp
//宣告 各種可能要用的委派   
public delegate void TextBoxUpdateDelegate(string text);
public delegate void ProgressBarUpdateDelegate(int value);
```

然後，你可以定義對應的刷新函數，並將它們分配給相應的委派：

```csharp
// 分配刷新函數給委派   下面 兩種寫法都行
TextBoxUpdateDelegate textBoxUpdateDelegate = UpdateTextBox;
ProgressBarUpdateDelegate progressBarUpdateDelegate = new ProgressBarUpdateDelegate(UpdateProgressBar);

public void UpdateTextBox(string text)
{
    // 更新文本框的程式碼
}

public void UpdateProgressBar(int value)
{
    // 更新進度條的程式碼
}


```

接下來，你可以在適當的地方使用這些委派來調用相應的刷新函數，以完成刷新 UI 的操作  
這樣可以根據不同的刷新類型區分程式碼，提高代碼的可讀性和可維護性。

---

# 最新作法  使用了 匿名  

```csharp
private void button1_Click(object sender, EventArgs e)
{
    Thread thr = new Thread(gogo_thread);
    thr.Start();
}


private void gogo_thread()
{
    Action<string, string> some_one_delegate = delegate (string code, string str)
    {
        if (code == "=")
        {
            textBox1.Text = str;
        }
        else if (code == "+=")
        {
            textBox1.Text += str;
        }

    };

    this.Invoke(some_one_delegate, "=","");


    int i = 0;
    while (true)
    {
        if (i % 10 == 0)
        {
            this.Invoke(some_one_delegate, "+=", Environment.NewLine);

            //textBox1.Text += Environment.NewLine;
        }
        else
        {
            this.Invoke(some_one_delegate, "+=", i % 10 + ",");

            //textBox1.Text += i % 10 + ",";
        }

        Thread.Sleep(500);
        i++;
    }
}
```

---
# Action<int> 是一個委派 (delegate) 類型  這種東西有很多嗎
是的，C# 中有許多內建的委派類型，用於表示不同類型的方法。以下是一些常見的委派類型：

1. `Action`: 表示不返回任何值的方法。可以帶有零到多個參數。
2. `Func`: 表示返回值的方法。最後一個類型參數表示返回值的類型，前面的參數表示方法的參數。
3. `Predicate`: 表示返回布林值的方法，通常用於進行條件判斷。
4. `EventHandler`: 用於處理事件的方法，接受兩個參數，第一個是事件的發送者，第二個是事件的參數。
5. `Comparison`: 用於比較兩個對象的方法，返回一個表示比較結果的整數值。

除了這些內建的委派類型，你還可以自定義自己的委派類型，以滿足特定的需求。委派類型可以提高代碼的可讀性和靈活性，並促進代碼的重用性和模塊化設計。