匿名方法和 Lambda 表達式都是用於定義匿名函式的語法形式，但它們在語法上有一些差異。下面是一個使用匿名方法和 Lambda 表達式的示例，可以看到它們之間的差異：

使用匿名方法：
```csharp
Action<int> printNumber = delegate (int number)
{
    Console.WriteLine(number);
};
```

使用 Lambda 表達式：
```csharp
Action<int> printNumber = (number) => Console.WriteLine(number);
```

兩者的功能相同，都是定義了一個接受 int 參數並輸出到控制台的匿名函式。然而，Lambda 表達式相對於匿名方法具有以下差異：

1. 語法簡潔：Lambda 表達式使用箭頭運算符 (=>) 來表示函式體，比使用匿名方法的 delegate 關鍵字更簡潔。

2. 型別推斷：Lambda 表達式可以進行型別推斷，不需要顯式指定參數的型別，編譯器可以根據上下文自動推斷。

3. 支援表達式樹：Lambda 表達式可以用於表達式樹 (Expression Tree) 的建構，使其在某些情況下能夠進行更高級的操作。

總的來說，Lambda 表達式相對於匿名方法更加簡潔和方便，並且提供了更多的功能和彈性。因此，在 C# 中，Lambda 表達式已經成為定義匿名函式的首選語法。


---  
匿名函數有兩種語法風格：Lambda表達式(lambda-expression)和匿名方法表達式(anonymous-method-expression)。在幾乎所有的情況下，Lambda表達式都比匿名方法表達式更爲簡介具有表現力。但現在C#語言中仍保留了後者，爲了向後兼容。  
　　Lambda表達式：
　　　　async可選 (匿名的函數簽名)=> (匿名的函數體)  
　　匿名方法表達式：
　　　　async可選 delegate (顯式的匿名函數簽名) 可選{代碼塊}  

　　其中匿名的函數簽名可以包括兩種，一種是隱式的匿名函數簽名另一種是顯式的匿名函數簽名：  
　　　　隱式的函數簽名：(p)、(p1,p1)  
　　　　顯式的函數簽名：(int p)、(int p1,int p2)、(ref int p1,out int p2)
