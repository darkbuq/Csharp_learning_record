# buttonAclick_call_buttonBclick_with_async_await

### use google query `C# BUTTON click call other button click cannot await`
google AI say:   
當嘗試在 C# 中從另一個按鈕的點擊處理程序呼叫一個按鈕的點擊處理程序並涉及async/await時  
當調用的點擊處理程序執行需要等待的非同步操作時，就會出現主要問題  

如果Button1_Click調用Button2_Click，並且Button2_Click包含await調用  
Button1_Click則不會自動等待 中的非同步操作完成Button2_Click  
這是因為Button1_Click被視為標準方法調用  
而不是awaitable此上下文中的操作。



當然可以，我幫你把這個概念拆得更清楚，也用 C# 的角度來解釋。重點在於 **UI 事件處理、async/await 與方法呼叫的差別**。

---

### 1️⃣ 基本情境

假設你有兩個按鈕：

```csharp
private async void Button2_Click(object sender, EventArgs e)
{
    await Task.Delay(2000); // 模擬非同步操作
    MessageBox.Show("Button2 完成!");
}

private void Button1_Click(object sender, EventArgs e)
{
    Button2_Click(sender, e); // 直接呼叫另一個按鈕事件
    MessageBox.Show("Button1 完成!");
}
```

**問題**：你可能以為「Button2 完成!」會先出現，再顯示「Button1 完成!」。
但實際上會先顯示「Button1 完成!」，然後 2 秒後才顯示「Button2 完成!」。

---

### 2️⃣ 為什麼會這樣？

1. `Button2_Click` 是一個 **async void** 方法。

   * **async void** 只能被事件處理程序直接呼叫，它不是「可等待（awaitable）」的。
   * 呼叫它時，程式會「啟動非同步操作」但不會等待它完成，呼叫端會立即繼續執行。

2. 當 `Button1_Click` 呼叫 `Button2_Click`，只是 **普通方法呼叫**，它不會知道裡面有 `await`，也不會等待它完成。

3. 結果就是：

   * Button1 的後續程式碼直接執行。
   * Button2 的 await 操作在背景中繼續，之後才完成。

---

### 3️⃣ 正確做法

**原則**：如果你希望「等待另一個按鈕的非同步操作完成」，就不要直接呼叫 `async void`，而是改成 **async Task**，並用 `await` 呼叫。

```csharp
private async Task Button2ActionAsync()
{
    await Task.Delay(2000);
    MessageBox.Show("Button2 完成!");
}

private async void Button2_Click(object sender, EventArgs e)
{
    await Button2ActionAsync();
}

private async void Button1_Click(object sender, EventArgs e)
{
    await Button2ActionAsync(); // 正確等待 Button2 的工作完成
    MessageBox.Show("Button1 完成!");
}
```

✅ 這樣就可以保證：

1. Button2 的非同步操作先完成
2. 然後 Button1 的後續程式碼才執行

---

### 4️⃣ 核心重點

* **async void** 只能用於事件處理程序，不適合直接被其他程式碼 `await`。
* 如果想「呼叫並等待非同步操作」，請改成 **async Task** 方法，並用 `await`。
* 事件處理程序可以呼叫 async Task 方法，再自己 await。

---
---

### 假設我有三大功能按鍵  我要保有按鍵獨立使用

其中這個全程自己跑 無所謂  
```csharp
private async void btn_Tx_test_Click(object sender, EventArgs e)  
```
  
  
下面這兩個 要順序跑  
```csharp
private async void btn_RX_Assert_DeAssert_LOS_VoltHL_Click(object sender, EventArgs e)  
private async void btn_DDMI_Click(object sender, EventArgs e)
```


============  
這三個事件本身  都有UI控制的東西     
也都有  邏輯運算 （但我都用task.run 放到背景跑）只有UI層面的東西  直接放事件上

===============  
我該怎麼改  

---  
---  

明白了，你的需求可以拆成兩件事：

1. **保持按鍵事件可以單獨被使用**（也就是保留 `async void`）。
2. **在自動流程中能順序執行某些功能**（也就是外部呼叫時可以 await）。

核心原則：

* `async void` 只能給事件使用，**不能被外部 await**。
* 想要順序呼叫，必須把可順序執行的邏輯抽成 **`async Task` 的方法**，然後事件內再呼叫這個方法。

---

### 改寫範例

```csharp
// ====================
// 功能1：Tx test，可獨立使用
private async void btn_Tx_test_Click(object sender, EventArgs e)
{
    await TxTestAsync();
}

// 真正邏輯抽成 Task
private async Task TxTestAsync()
{
    // UI 控制
    // 例如：btnTxTest.Enabled = false;
    
    // 背景邏輯
    await Task.Run(() =>
    {
        // 你的計算或測試邏輯
    });

    // UI 控制恢復
    // btnTxTest.Enabled = true;
}

// ====================
// 功能2：RX Assert DeAssert + LOS + VoltHL
private async void btn_RX_Assert_DeAssert_LOS_VoltHL_Click(object sender, EventArgs e)
{
    await RxAssertAsync();
}

private async Task RxAssertAsync()
{
    // UI 控制
    // 背景運算
    await Task.Run(() =>
    {
        // 邏輯
    });
}

// ====================
// 功能3：DDMI
private async void btn_DDMI_Click(object sender, EventArgs e)
{
    await DdmiAsync();
}

private async Task DdmiAsync()
{
    // UI 控制
    await Task.Run(() =>
    {
        // 邏輯
    });
}

// ====================
// 自動流程按鈕
private async void btn_1V1T_AutoRun_Click(object sender, EventArgs e)
{
    // 可直接呼叫 TxTestAsync() 或單純 fire-and-forget
    _ = TxTestAsync(); // 不等它完成

    // RX + DDMI 需要順序
    await RxAssertAsync();
    await DdmiAsync();
}
```

---

✅ **特點**：

1. 三個按鍵事件仍可單獨使用，UI 控制不受影響。
2. 自動流程中，`RxAssertAsync` 和 `DdmiAsync` 可以順序執行（`await`）。
3. Tx test 可在自動流程中同時執行，不影響後面流程（fire-and-forget，前面加 `_ = ...`）。

---





