# 多個回傳值 就用Tuple吧

### C# 函式如果要有多個回傳值，大抵離不開以下幾種作法：
1. 使用輸出參數。  
亦即透過參數列的 out 修飾詞來定義輸出參數。  
2. 傳回一個 dynamic 物件。  
此作法的缺點是效能較差，而且沒有編譯時期的型別安全檢查。  
3. 使用自訂型別。  
亦即寫一個新類別，把要返回的多項資訊包在這個類別裡面  
然後讓函式返回這個自訂類別的物件。
4. 使用 .NET 現成的 `System.Tuple`。
  
>如果只是比較簡單、或者用完即丟的場合，就沒必要寫一個新類別  
>而可以選擇 .NET framework 提供的 Tuple  
  

---
### 創建 & 訪問元素  
`Tuple` 預設是以 Item1、Item2 …   的方式來取得其內部的物件
```csharp
var person = Tuple.Create(1, "Steve", "Jobs");
person.Item1; // 返回 1
person.Item2; // 返回 "Steve"
person.Item3; // 返回 "Jobs"
```
### 也可以具名  
C# 7.1 加入自動推斷 Tuple Literal 元素名稱功能，比照 new 自動取 PorpName 作為屬性名的概念。
```csharp
var person1 = (Name: "nick666", Age: 18);//隱式宣告
(string Name, int Age) person2 = ("nick666", 18);//顯式宣告

// 訪問 Tuple 的具名元素
person1.Name;// 返回 nick666
person1.Age;// 返回 18
```
---
### 以`Tuple`做為回傳
```csharp
static Tuple<int, string, string> GetPerson() 
{    
    return Tuple.Create(1, "Bill", "Gates");
}
```


