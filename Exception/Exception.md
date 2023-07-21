# 異常處理 例外處理.....Exception

### 基本概念  
```csharp
try
{
    ... // 正常流程的程式碼
}
catch
{
    ... // 意外狀況發生時，會執行此區塊的程式碼
}
```
### 對Exception做分類
```csharp
try
{
    ... // 正常流程的程式碼
}
catch (FileNotFoundException fileEx)
{
    ... // 處理「檔案找不到」的意外狀況。
}
catch (SqlException sqlEx)
{
    ... // 處理資料庫相關操作的意外狀況。
}
catch (Exception ex)
{
    ... // 前面幾張「網子」都沒抓到的漏網之魚，全都在此處理。
}
```
### finally 子句  
不管異常是否被抛出 都會執行  
無論執行過程是否發生例外狀況，最終都要執行 finally 區塊中的程式碼

`一種常見的寫法是在 finally 區塊中撰寫資源回收的工作`  
例如關閉檔案、關閉網路連線或資料庫連線等等
```csharp
StreamReader reader = null;
try
{
    reader = new StreamReader("app.config");
    var content = reader.ReadToEnd();
    Console.WriteLine(content);
}
catch (FileIOException ex)
{
    Console.WriteLine(ex.Message);
}
finally
{
    if (reader != null)
    {
        reader.Dispose();
    }    
}
```



---
### 正確重拋例外
[[C#] 正確重拋例外 (Exception) 的方式](https://www.dotblogs.com.tw/wasichris/2015/06/07/151505)  
 

 