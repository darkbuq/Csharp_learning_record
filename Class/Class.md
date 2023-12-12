## 屬性 Property => public vs. set&get


 ```csharp
class Car() //一般是要下存取權限 不然會報錯
{
    public int Speed; //如果是public 那就自動 { get; set; }
    int Value1 { get; set; } //這也算是自動 get; set;
    int Value2
}
```

