
# C#   how to get value of ui thread
### 理解如下    
1 增加一個額外的執行緒   讓部分程式碼在這個執行緒 上跑  
2 這額外的執行緒   跑的內容如果有關UI刷新  
就做一個函數來刷新UI   +  一個委派    讓委派跑函數刷新UI 

---
### 第一種寫法 如果只需要在一開始取值 就讓子執行緒在一開始就以參數方式取得值
```csharp
Thread thr;
private void btn_start_RT_Click(object sender, EventArgs e)
{
    int ff = 1000;

    thr = new Thread(realtime_DDMI);
    thr.Start(ff);
}

private void realtime_DDMI(object parameter)
{
    int ff;
    ff = (int)parameter;
}  
```

---
### 第二種寫法 若子執行緒 已經開始  在要某個時間取值  那就一定要委派了

```csharp
Thread thr;
private void btn_start_RT_Click(object sender, EventArgs e)
{
    thr = new Thread(realtime_DDMI);
    thr.Start();
}

private void realtime_DDMI()
{
    ///使用 Control.Invoke 在 UI 線程上取得值
    ff = (int)cbo_frequency.Invoke(new Func<int>(() => int.Parse(cbo_frequency.Text)));//連Func的名字都懶得取了

    //上一段 看不懂慢慢寫
    //Func<int> func1 = () => int.Parse(cbo_frequency.Text); 
    //Lambda表達式 Func<bool, bool, bool> A = (a, b) => (a == b);
    //ff = func1();
}
```
---
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