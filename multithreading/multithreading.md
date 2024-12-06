
# ManualResetEvent 是一线程用来控制别一个线程的信号
### public ManualResetEvent(bool initialState);  建構子
ManualResetEvent的構造方法有個bool型參數  
當為true時，則表示有訊號  
為false時，則表示無訊號。

### WaitOne();
等待的功能，也就是執行緒阻塞  

也就是 執行緒 執行到WaitOne()時  
無訊號時，它是阻塞的
有訊號時，它將無任何阻塞

### Reset();
將ManualResetEvent物件的訊號狀態設為無訊號狀態  
當下次執行到WaitOne時，又會重新開始阻斷

### Set();
將ManualResetEvent物件的訊號狀態設為有訊號狀態

---
### 以下範例為 TCP/IP 通訊   需要多一個執行緒做接收  
### 在 Query 方法中完成同步發送和接收操作，而同時利用多執行緒進行接收

```csharp
public string Query(string command)
{
    Write(command);

    // 重置事件和接收狀態
    _receiveCompletedEvent.Reset();
    _response = null;

    // 啟動接收執行緒
    _receiveThread = new Thread(ReceiveMessages);
    _receiveThread.Start();


    // 等待接收完成
    bool received = _receiveCompletedEvent.WaitOne(5000); // 等待 5 秒超時

    if (!received)
    {
        throw new TimeoutException("Timeout waiting for response.");
    }

    return _response;
}
```
```csharp
private void ReceiveMessages()
{
    try
    {
        byte[] buffer = new byte[1024];
        int bytesRead = _stream.Read(buffer, 0, buffer.Length);

        if (bytesRead > 0)
        {
            _response = Encoding.ASCII.GetString(buffer, 0, bytesRead);
        }
    }
    catch (Exception ex)
    {
        _response = $"Error: {ex.Message}";
    }
    finally
    {
        // 標記接收完成
        _receiveCompletedEvent.Set(); //通知 Query 等待結束
    }
}
```