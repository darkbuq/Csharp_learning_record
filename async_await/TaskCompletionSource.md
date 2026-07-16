# `TaskCompletionSource<T>` 是什麼等級的工具？

一句話定位：

> **把「事件 / Timer / Callback」
> 變成「可以 `await` 的同步流程」**

---

# 🔹 常見用途總覽（你一定會用到的）

---

## 1️⃣ 等待「事件發生」⭐⭐⭐⭐⭐

（你現在就在用）

### 傳統寫法（事件地獄）

```csharp
device.OnStable += () => DoNext();
```

### TCS 寫法（流程可讀）

```csharp
await _tcs.Task;
DoNext();
```

📌 用途：

* 等溫度穩態
* 等量測完成
* 等設備 Ready

---

## 2️⃣ 等待「Timer 停止 / 條件成立」⭐⭐⭐⭐⭐

你現在的案例就是典型：

```csharp
if (IsCurveSteady(...))
{
    timer.Stop();
    _tcs.TrySetResult(true);
}
```

📌 用途：

* 曲線平穩
* 超時判斷
* N 秒內達標

---

## 3️⃣ 等待「使用者操作」⭐⭐⭐⭐

### 例：等使用者按 OK

```csharp
var tcs = new TaskCompletionSource<bool>();

btnOK.Click += (_,__) => tcs.TrySetResult(true);

await tcs.Task;
```

📌 用途：

* MessageBox 客製化
* 校正確認
* 人機確認步驟

---

## 4️⃣ 將「同步 API」包成 `async` ⭐⭐⭐⭐

### 舊設備 API

```csharp
void StartMeasure();
event EventHandler MeasureDone;
```

### 包裝後

```csharp
async Task MeasureAsync()
{
    var tcs = new TaskCompletionSource<bool>();
    device.MeasureDone += (_,__) => tcs.TrySetResult(true);
    device.StartMeasure();
    await tcs.Task;
}
```

📌 用途：

* 老設備 SDK
* COM / GPIB callback

---

## 5️⃣ 取消流程（Abort / Stop）⭐⭐⭐⭐⭐

```csharp
_tcs.TrySetCanceled();
```

搭配：

```csharp
try { await _tcs.Task; }
catch (TaskCanceledException) { }
```

📌 用途：

* Stop 鍵
* Reset
* 緊急中止

---

## 6️⃣ Timeout 機制 ⭐⭐⭐⭐⭐（超重要）

```csharp
var tcs = new TaskCompletionSource<bool>();

var timeoutTask = Task.Delay(300000);
var finishedTask = await Task.WhenAny(tcs.Task, timeoutTask);

if (finishedTask == timeoutTask)
{
    tcs.TrySetCanceled();
    throw new TimeoutException();
}
```

📌 用途：

* 設備卡死保護
* 防止無限等待

---

## 7️⃣ 等多個條件其中之一 ⭐⭐⭐⭐

```csharp
await Task.WhenAny(_stableTcs.Task, _abortTcs.Task);
```

📌 用途：

* 穩態 or 手動中止
* Pass or Fail

---

## 8️⃣ 從 Background Thread 回 UI ⭐⭐⭐

```csharp
_tcs.TrySetResult(value); // thread-safe
```

📌 用途：

* 背景量測
* 非同步資料回 UI

---

# 🔸 常用 API 速查表（工程必背）

| 方法 / 屬性               | 用途          |
| --------------------- | ----------- |
| `Task`                | 給 `await` 用 |
| `TrySetResult(T)`     | 正常完成        |
| `TrySetCanceled()`    | 中止          |
| `TrySetException(ex)` | 錯誤          |
| `Task.IsCompleted`    | 是否已完成       |
| `Task.IsCanceled`     | 是否被取消       |
| `Task.IsFaulted`      | 是否錯誤        |

---

# ❗ 重要使用原則（這 4 條很關鍵）

### 1️⃣ 一次性使用

```text
new → await → complete → 丟掉
```

---

### 2️⃣ 永遠用 `TrySetXxx`

❌ `SetResult`
✅ `TrySetResult`

---

### 3️⃣ 永遠「完成 or 取消」

❌ 放著不管（await 卡死）
✅ Stop / Reset 一定通知

---

### 4️⃣ 不要拿來做資料傳輸

TCS 是「**控制流程**」
不是「傳大量資料」

---

# 🧠 給你一個設備工程師級的總結

> **`TaskCompletionSource` =
> WinForms / 設備控制中
> 「事件式程式碼」→「流程式程式碼」的橋樑**

你現在這一整套：

* Timer
* 曲線穩態
* 校正流程
* Stop / Reset / Timeout

👉 **完全是「自動化設備主流程」的典型結構**

---

如果你願意，下一步我可以直接幫你：

* 把 **TCS + Timer** 抽成一個 `WaitCurveSteadyAsync()`
* 或畫一張 **完整流程狀態機圖（Idle / Running / Stable / Aborted）**

你比較想往哪個方向進？
