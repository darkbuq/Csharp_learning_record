## null運算子



```csharp
private string SafeToString(object value)
{
    return value?.ToString() ?? string.Empty;
}
```
---
### value?.ToString()
這是 null 條件運算子（null-conditional operator） 的用法。

它的意思是：

如果 value 不是 null，就呼叫 value.ToString()；
如果 value 是 null，就回傳 null，不會報錯。

這樣可以防止因為 value 是 null 而出現 NullReferenceException。

---
### ?? string.Empty
`??`是 null 合併運算子（null-coalescing operator） 的用法。

它的意思是：

如果前面 value?.ToString() 是 null，就回傳右邊的 string.Empty（空字串）。