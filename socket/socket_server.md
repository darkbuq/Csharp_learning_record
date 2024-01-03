## socket server  
1. new TcpListener
2. waiting for client to connect --> AcceptTcpClient
3. new thread to receive 
4. use UI_thread to send message

### new TcpListener
```csharp
TcpListener server = new TcpListener(IPAddress.Parse("127.0.0.1"), 12345);
```

---
### waiting for client to connect --> AcceptTcpClient  
```csharp
server.Start();
//從 start 到 AcceptTcpClient 會UI阻塞  無所謂 就用 UI thread 去做
TcpClient client = server.AcceptTcpClient();
```

---
### new thread to receive
```csharp
// 開啟新執行緒處理receive
Thread client_receive = new Thread(receive);
client_receive.Start(client);
```

```csharp
private void receive(object clientObj)
{
    TcpClient tcpClient = (TcpClient)clientObj;
    NetworkStream clientStream = tcpClient.GetStream();

    // 在這裡實現與客戶端通訊的邏輯
    byte[] message = new byte[4096];
    int bytesRead;

    while (true)//一直跑  
    {
        bytesRead = 0;

        try
        {
            bytesRead = clientStream.Read(message, 0, 4096);
            // NetworkStream.Read 方法是一個阻塞的方法，
            // 它會一直等待，直到有資料可供讀取，
            // 或者發生了例外情況（例如，連線關閉、超時等）。
            // 這就是為什麼在這個 while 迴圈中，
            // 當沒有資料可供讀取時，它會一直等待。
        }
        catch
        {
            Action<TextBox, string> reflashUI3 = (TextBox, strr) => TextBox.Text += strr + "\r\n";
            this.Invoke(reflashUI3, txt_note, $"try_catch...catch something...");
            break;
        }

        //如果使用阻塞方法 那下面這個if基本上用不到
        if (bytesRead == 0)//等於0  就是沒收到東西  就跳過下面程式碼
        {
            break;
        }
            

        string receivedMessage = Encoding.ASCII.GetString(message, 0, bytesRead);
        //Console.WriteLine($"Received: {receivedMessage}");
        Action<TextBox, string> reflashUI = (TextBox, strr) => TextBox.Text += strr + "\r\n";
        this.Invoke(reflashUI, txt_note, $"{receivedMessage}");
    }
}
```

---
### use UI_thread to send message
```csharp
private void btn_send_Click(object sender, EventArgs e)
{
    string message = txt_send.Text;
    try
    {
        NetworkStream clientStream = client.GetStream();
        byte[] data = Encoding.ASCII.GetBytes(message);
        clientStream.Write(data, 0, data.Length);
        clientStream.Flush();
    }
    catch (Exception ex)
    {
        MessageBox.Show($"Error sending message to client: {ex.Message}");
    }
}
```

---
### while迴圈會卡在這一行  bytesRead = clientStream.Read(message, 0, 4096);   
程式如果 呼叫  NetworkStream.Read  那就會一直等待  直到  讀到東西  
才結束這一行 bytesRead = clientStream.Read(message, 0, 4096);  

#### `NetworkStream.Read` 方法是一個阻塞的方法
它會一直等待，直到有資料可供讀取，或者發生了例外情況（例如，連線關閉、超時等）  
這就是為什麼在這個 while 迴圈中，當沒有資料可供讀取時，它會一直等待。

如果你希望在沒有資料可供讀取時不阻塞  
可以考慮使用非阻塞的方法  
例如 `NetworkStream.DataAvailable` 屬性和 `NetworkStream.ReadAsync` 方法。

這是一個使用非阻塞方法的簡單例子：

```csharp
while (tcpClient.Connected)
{
    if (clientStream.DataAvailable)
    {
        bytesRead = await clientStream.ReadAsync(message, 0, 4096);
        if (bytesRead == 0)
        {
            // 處理連線關閉的情況
            break;
        }

        string receivedMessage = Encoding.ASCII.GetString(message, 0, bytesRead);
        Console.WriteLine($"Received: {receivedMessage}");

        Action<TextBox, string> reflashUI = (TextBox, strr) => TextBox.Text += strr + "\r\n";
        this.Invoke(reflashUI, txt_note, $"{receivedMessage}");
    }
    else
    {
        // 沒有資料可供讀取，你可以在這裡做一些其他事情，或者加入一些延遲。
        Thread.Sleep(10);
    }
}
```

在這個例子中，我使用了 `DataAvailable` 屬性檢查是否有資料可供讀取，如果有，才進行非阻塞的讀取操作。這樣可以避免 `Read` 方法一直等待，並允許你在沒有資料時做其他事情。