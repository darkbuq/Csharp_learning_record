## async_await

### async
async 是用來標記一個方法為非同步的關鍵字  
允許你在方法內部使用 await 關鍵字來等待非同步操作完成  
需要注意的是，async 本身不會讓一個方法變得非同步  
它只是一個標記，允許該方法內部執行非同步操作  

async 方法的返回類型：通常是 Task 或 Task\<T>。  
如果不返回任何結果，可以使用 Task；  
如果返回結果，則使用 'Task\<T>'

---

### await
用於等待一個異步操作完成。   
當程式遇到 await 時，它會暫停當前方法的執行  
將控制權交還給呼叫者  

`await Task.Delay(1000);`   
若有指定等待時間 則時間結束後 再把控制權搶回來  

並在後台繼續執行被 await 的操作  
當操作完成時，程式會從 await 處繼續執行。

---

```csharp
public static async Task Main(string[] args)
{
    var waitingTask = WaitExampleAsync(); // 開始等待但不等它完成

    // 主線程持續做其他事情（例如不斷印出）
    for (int i = 0; i < 10; i++)
    {
        Console.WriteLine($"Main doing other work... {i}..{DateTime.Now}");
        await Task.Delay(1000); // 模擬主線程的其他工作（短暫非同步延遲）
    }

    await waitingTask; // 最後才等 WaitExampleAsync 結束
    Console.WriteLine("Main finished all work.");

    Console.ReadLine();
}

public static async Task WaitExampleAsync()
{
    Console.WriteLine($"Start async wait...{DateTime.Now}");
    await Task.Delay(2000);//這時會放開控制權2秒 所以控制權會回到  Main(string[] args)
    Console.WriteLine($"Finished waiting asynchronously!.....{DateTime.Now}");
}
```