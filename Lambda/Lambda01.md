## 基本理解
### Lambda表達式  

原本常見寫一個方法像這樣：
```csharp
Bool A ( int a,int b)
{
 return a==b;
}
bool FT = A(a,b); //調用回傳值
```
而Lambda的概念是，把上面 改成 輸入 => 黑箱=>輸出
也就是：
```csharp
A = (a,b)=>(a==b)
bool FT = A(a,b); //調用回傳值
```

```csharp
new int[]{1,2,3}.Select(a=>(a+1))
//會回傳 2,3,4
```

---

## 來個小範例
#### 設vcc_val 是 double[]  如何一行印出
```csharp
txt_result.Text += $"vcc_val {vcc_val}\r\n";
```
#### 直接印出所有值（用空格分隔）：
```csharp
txt_result.Text += $"vcc_val {string.Join(" ", vcc_val)}\r\n";
//設vcc_val 是 double[]  如何一行印出
```
#### 輸出範例：
```csharp
vcc_val 1.23 4.56 7.89
```

#### 如果要格式化數字（例如保留兩位小數）：
```csharp
txt_result.Text += $"ibias_val: {string.Join(" ", ibias_val.Select(v => v.ToString("0.00")))}\r\n";
```
#### `Select()` 是 LINQ 的方法  
#### 用來對集合的每個元素執行()內操作，並回傳一個新的集合。  
Select() 是 LINQ (Language Integrated Query) 提供的方法，  
用來逐一處理集合中的每個元素，並把處理後的結果組成一個新的集合。  
你可以想像成一個「轉換工廠」，它會拿每個元素來加工，然後輸出加工後的結果！


### `v => v.ToString("0.00")`  就用到基本的 Lambda 表達式
v：Lambda 的輸入參數，每次迭代時代表 ibias_val 陣列中的每個值。  
v.ToString("0.00")：對每個值 v 呼叫 ToString()，並使用 "0.00" 格式化輸出。  

Lambda 表達式可以理解為「匿名函數」，它提供了一種簡潔的方式來表示「輸入 → 輸出」的邏輯。常見的語法如下：
```csharp
(input) => { 處理邏輯; return 輸出值; }//可以有多行步驟
```
簡化後可以寫成：  
```csharp
input => 處理邏輯//快速單行步驟
```
