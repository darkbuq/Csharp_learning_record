# 神奇的語法糖

```csharp
public void autoScale() => Thread.Sleep(500);
```

這裡的 `=>` 不是「lambda」  
而是 **C# 的 expression-bodied member（運算式主體成員）** 語法糖  
意思是：當一個方法（或屬性、建構子、解構子…）的實作只需要「一行運算式」  
就可以用 `=>` 來簡化，不用寫大括號 `{ }`。

等價寫法是：

```csharp
public void autoScale()
{
    Thread.Sleep(500);
}
```

---

📌 用途總結：

1. **方法**：

   ```csharp
   public int Add(int a, int b) => a + b;
   // 等價於：
   public int Add(int a, int b) { return a + b; }
   ```

2. **唯讀屬性**：

   ```csharp
   public string Name => "Dummy DCA";
   // 等價於：
   public string Name { get { return "Dummy DCA"; } }
   ```

3. **建構子 / 解構子**（C# 7.0+）：

   ```csharp
   public DCA_Dummy() => Console.WriteLine("Constructed");
   ~DCA_Dummy() => Console.WriteLine("Destructed");
   ```

---






