## 依賴反轉原則 Dependency Inversion Principle (DIP)
依賴反轉原則要求高層模組不能依賴低層模組，而是兩者都依賴抽象  

原本是 `高階模組 → 低階模組` 的關係  
變成了 `高階模組 → 介面 ← 低階模組`  
並不是高階去依賴低階，而是低階去依賴高階要求的功能

---
### What’s DI 依賴注入?  

所謂的 DI 架構，指的是一整套設計模式與實務原則。這是一種程式設計面上的思維訓練，而不是特指某種技術

---
### DI跟控制反轉 (IoC) 的關係是……？  
Inversion of Control （控制反轉）是實現低耦合的最佳設計方式之一，讓通用的程式碼來控制應用特定的程式碼，相依於抽象而不倚賴實作。

---
### DI跟依賴反轉原則 (DIP) 的關係是……？
DIP 是個使用抽象時依賴關係的準則或概念，IoC 說明了依賴關係的控制方向，而 DI 是一種處理依賴關係的模式。

---
### DI 依賴注入  

- 建構子注入
- 屬性注入
- 方法注入

---
### 建構子注入
```csharp
public interface IMessage
{
    void SendMessage();
}
public class Email : IMessage
{
    public void SendMessage()
    {
        Console.WriteLine("Send Email");
    }
}
public class SMS : IMessage
{
    public void SendMessage()
    {
        Console.WriteLine("Send Sms");
    }
}
public class Notification
{
    private IMessage _msg;
    public Notification(IMessage msg)
    {
        this._msg = msg;
    }
    public void Notify()
    {
        _msg.SendMessage();
    }
}
class Program
{
    public static void Main()
    {
        Email email = new Email();
        Notification notify = new Notification(email);
        notify.Notify();

        SMS sms = new SMS();
        notify = new Notification(sms);
        notify.Notify();
    }
}

```

---
### 後續研究  

[DI（依賴注入） 定義、跟 IoC（控制反轉）和 DIP（依賴反轉原則）的關係、優缺點](https://medium.com/wenchin-rolls-around/%E6%B7%BA%E5%85%A5%E6%B7%BA%E5%87%BA-dependency-injection-ea672ba033ca)  




