## 轉型失敗的 異常

C#中數據轉換的方法很多，拿將目標對象轉換為整型（int）來講  
有四種方法：分別為  
1. (int)
2. int.Parse()
3. int.TryParse()
4. Convert.ToInt32()

`對於空值NULL`   從運行報錯的角度講  
(int)強制轉換和int.Parse()都不能接受 `NULL`

---
### (int) 強制轉型
C#的默認整型是int32

---
### int.Parse()
如果 string 為 `null` 則拋出 `ArgumentNullException` 異常  
如果 string 不是數字   則拋出 `FormatException` 異常  
如果 string 超出 int類型 可表示的範圍  則拋出 `OverflowException` 異常

---
### int.TryParse()
如果 轉換成功 則 回傳 true  
如果 轉換失敗 則 回傳 false 且 輸出值為0
```csharp
int number = 100;
int.TryParse(stringNumber, out number);
//最後  number會是 0
```

---
### Convert.ToInt32()
`Convert.ToInt32()` 其實是在轉換前先做了一個判斷  
參數如果為NULL，則直接返回0，
