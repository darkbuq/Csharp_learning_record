
# 匿名方法和 Lambda 表達式都是用於表示未命名的方法  
但它們在語法和用法上有所不同。

匿名方法是一種在 C# 2.0 中引入的特性  
它通過使用 `delegate` 關鍵字來定義，並且可以包含程式碼塊  
匿名方法可以用於替代具名方法，並且可以被賦值給委派類型的變數或參數。  
使用匿名方法：
```csharp
int val = 8;
Action<int> printNumber = delegate (int number)
{
    Console.WriteLine(number);
};

this.Invoke(printNumber, val);
```
  
---
### Lambda表達式  
Lambda 表達式則是在 C# 3.0 中引入的  
Lambda 表達式使用 `=>` 運算符 它的左側是輸入參數，右側是方法主體  
原本常見寫一個方法像這樣：
```csharp
bool a=True;
bool b=Flase;

Bool A ( int a,int b){return a==b;}
bool TF1 = A(a,b);

//而Lambda的概念是，把上面 改成 輸入 => 黑箱=>輸出
A = (a,b)=>(a==b)
bool TF2 = A(a,b);
```
---
### Action<int> 是一個委派 (delegate) 類型  這種東西有很多嗎
是的，C# 中有許多內建的委派類型，用於表示不同類型的方法。以下是一些常見的委派類型：

1. `Action`: 表示不返回任何值的方法。可以帶有零到多個參數。
2. `Func`: 表示返回值的方法。最後一個類型參數表示返回值的類型，前面的參數表示方法的參數。
3. `Predicate`: 表示返回布林值的方法，通常用於進行條件判斷。
4. `EventHandler`: 用於處理事件的方法，接受兩個參數，第一個是事件的發送者，第二個是事件的參數。
5. `Comparison`: 用於比較兩個對象的方法，返回一個表示比較結果的整數值。

除了這些內建的委派類型，你還可以自定義自己的委派類型，以滿足特定的需求。委派類型可以提高代碼的可讀性和靈活性，並促進代碼的重用性和模塊化設計。

--- 
### `Func<>`  基本寫法    加上如何接回傳值
```csharp
// Func<TResult>：表示無參數方法，返回 TResult 類型的值
Func<int> func1 = () => 42; // 無參數，返回整數

// Func<T, TResult>：表示一個參數方法，返回 TResult 類型的值
Func<string, int> func2 = (s) => s.Length; // 接受一個字符串參數，返回字符串的長度

// Func<T1, T2, TResult>：表示兩個參數方法，返回 TResult 類型的值
Func<int, int, int> func3 = (x, y) => x + y; // 接受兩個整數參數，返回它們的和

// 調用 Func 並接收返回值
int result1 = func1();           // 無參數，返回 42
int result2 = func2("Hello");    // 接受一個字符串參數，返回字符串的長度
int result3 = func3(10, 20);      // 接受兩個整數參數，返回它們的和

// 顯示結果
Console.WriteLine("Result 1: " + result1);
Console.WriteLine("Result 2: " + result2);
Console.WriteLine("Result 3: " + result3);
```