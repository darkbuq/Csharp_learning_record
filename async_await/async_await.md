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

---
### 一點小想法

>如果 任務是兼著做兩件事  
>一個主線   一個支線  支線實際要跑要算很久才會跑完  
>就不太適合  非同步了吧  
>非同步   適合兩件事  都有   一些地方沒事做在等待   
>  
>以上看法對嗎

---

你的看法**非常接近正確核心了**，我幫你再整理一下，把關鍵差異補得更完整：

---

### ✅ 你的場景：「主線 + 支線（耗時計算）同時進行」

> ➡️ 支線不是在等待，是**真的要算很久（CPU密集）**

這種情況下，用 `async/await` 的**非同步處理方式**就**不適合**，原因如下：

---

### ❌ 為何「純 async/await」不適合跑很久的計算？

* `async/await` 適合「**I/O-bound 工作**」：

  * 例如等資料庫、讀檔案、網路請求、延遲時間
  * 它背後用的是 **一條主執行緒 + 排程器 + 等待點切換**

* 你說的支線工作是「**CPU-bound 計算密集工作**」：

  * 這類工作不會自己「釋放控制權」，會一直佔著 CPU 算到底
  * 所以會**卡住主執行緒，無法切換**，即使你用 `await`，也沒幫助

---

### ✅ 那該怎麼辦？

要讓「**真的在跑的計算密集支線**」不卡住主線，你應該使用：

```csharp
await Task.Run(() => LongRunningWork());
```

這才是「**讓重計算的任務跑在背景執行緒上**」的正確方法。

---

### 🔄 總結對比表

| 類型         | 適合 async/await | 適合 Task.Run / 多執行緒 |
| ---------- | -------------- | ------------------ |
| 等待下載       | ✅ 是            | ❌ 不建議              |
| 資料庫查詢      | ✅ 是            | ❌ 不建議              |
| 檔案讀寫       | ✅ 是            | ❌ 不建議              |
| 長時間 CPU 計算 | ❌ 不適合          | ✅ 建議使用             |

---

### 🎯 你說的觀念（我補充整理）

> 非同步適合「兩件事都有一些地方在等待」，但如果一件事是「一直跑、一直算」，就不適合非同步了。

✅ \*\*這句話邏輯完全對，只差一個詞補強：「**這樣的計算型任務要用多執行緒（如 Task.Run）**」就完美了！

---

### 🧪 想試試改寫嗎？

```csharp
await Task.Run(() =>
{
    // 這段是耗時的計算
    for (int i = 0; i < 100000000; i++)
    {
        double x = Math.Sqrt(i);
    }
});
```

---

