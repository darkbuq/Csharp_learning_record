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
5. 使用 `valuetuple` 為 `C# 7.0` 的新特性，`.Net Framework 4.7` 以上版本可用

原文網址：https://kknews.cc/news/8mo549g.html

  
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
---
### 以`valuetuple`做為回傳
```csharp
static (int first, string second) GetPerson() 
{    
    return (1, "Bill");
}
var result = GetPerson();
Console.Writeline($"{first},{second}");
```
---
>如果  原專案是用   .Net Framework 4  開發的  
>但我想用  .Net Framework 4.7   的某個新功能  
>我應該怎麼做
  
若原專案是用 .NET Framework 4 開發，而你想要利用 .NET Framework 4.7 的新功能，你可以考慮以下步驟：

1. **更新 .NET Framework 版本：** 首先，確保你的開發環境已經安裝了 .NET Framework 4.7 或更高版本。你可以在 [Microsoft 官方網站](https://dotnet.microsoft.com/download/dotnet-framework) 下載安裝程式。

2. **專案升級：** 開啟原有的專案，右鍵專案，選擇 "Properties"（屬性），然後在 "Application"（應用程式）頁面中找到 "Target Framework"（目標框架）選項。將其設定為 .NET Framework 4.7。

3. **檢查相容性：** 當你升級 .NET Framework 版本時，有些功能或 API 可能會有所變動。請檢查你的程式碼，確保所有的相依性和 API 能夠在新版本中正確運作。

4. **使用新功能：** 一旦專案已經升級，你就可以開始使用 .NET Framework 4.7 的新功能了。查閱相關的文件或資源，以了解這些新功能的使用方式和實施方法。

5. **測試和除錯：** 在修改程式碼後，進行全面的測試，以確保應用程式在新的 .NET Framework 版本中運行正常。處理可能的問題和錯誤。

請注意，對於 .NET Framework 4.7 以上的版本，你可能還需要考慮使用 .NET Core 或 .NET 5+（根據當時的最新版本）等更現代的 .NET 平台，因為微軟已經漸漸將焦點轉移到這些平台上。

---
>如果照 你的方法  我的專案建置出來的執行檔  
>能夠拿去只有  .Net Framework 4  的電腦上執行嗎

如果你升級了專案的目標 .NET Framework 版本，建置出來的執行檔在原本只有 .NET Framework 4 的電腦上可能會遇到相容性的問題。一般來說，較新版本的 .NET Framework 通常能夠支援舊版本的程式碼，但反過來不一定成立。

如果你的應用程式使用了 .NET Framework 4.7 的獨有功能，而該功能在 .NET Framework 4 中不存在，那麼在只有 .NET Framework 4 的環境中執行可能會失敗。

以下是一些建議：

1. **確認相容性：** 在升級 .NET Framework 版本之前，應該先確認所使用的功能和 API 在目標版本中是否存在，以避免相容性問題。

2. **提供正確的 .NET Framework 版本：** 如果你的應用程式需要在 .NET Framework 4 的環境中運行，你可以考慮使用條件編譯或是發佈不同版本的執行檔，以確保在目標環境中正確執行。

3. **升級至 .NET Core 或 .NET 5+：** 如果可能，考慮將專案升級到 .NET Core 或 .NET 5+，這些平台更具現代性，也有更好的跨平台支援。

最終，確保在進行版本升級時測試應用程式的各個方面，以確保在目標環境中的正確運行。