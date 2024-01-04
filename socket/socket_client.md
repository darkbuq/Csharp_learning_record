## socket client
1. new TcpClient
2. new thread to receive
3. use UI_thread to send message


### new TcpClient  
```csharp
TcpClient client = new TcpClient("127.0.0.1", 12345);
```

---
### new thread to receive 
```csharp
receiveThread = new Thread(ReceiveMessages);
receiveThread.Start();
```

```csharp
private void ReceiveMessages()
{
    try
    {
        if (client == null || !client.Connected)
        {
            MessageBox.Show("not connect..");
            return;
        }


        NetworkStream clientStream = client.GetStream();
        byte[] message = new byte[4096];
        int bytesRead;
        while (true)
        {
            bytesRead = 0;

            bytesRead = clientStream.Read(message, 0, 4096);
            // NetworkStream.Read 方法是一個阻塞的方法，
            // 它會一直等待，直到有資料可供讀取，
            // 或者發生了例外情況（例如，連線關閉、超時等）。
            // 這就是為什麼在這個 while 迴圈中，
            // 當沒有資料可供讀取時，它會一直等待。

            if (bytesRead == 0)
                break;

            string receivedMessage = Encoding.ASCII.GetString(message, 0, bytesRead);

            // 顯示收到的訊息在 UI 上
            Action<TextBox, string> reflashUI = (TextBox, strr) =>
            {
                TextBox.Text += strr + "\r\n";
                TextBox.SelectionStart = TextBox.Text.Length;
                TextBox.ScrollToCaret();
            };
            this.Invoke(reflashUI, txt_note, receivedMessage);
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show($"Error receiving message: {ex.Message}");
    }
}
```

---
### use UI_thread to send message
```csharp
private void btn_send_Click(object sender, EventArgs e)
{
    if (client == null || !client.Connected)
    {
        MessageBox.Show("Not connected to the server.");
        return;
    }

    NetworkStream clientStream = client.GetStream();
    byte[] data = Encoding.ASCII.GetBytes(message);
    clientStream.Write(data, 0, data.Length);
    clientStream.Flush();
}
```
