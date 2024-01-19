## GPIB

### 參考資料  

[C# – Sending SCPI/GPIB commands over USB from C#](https://itecnote.com/tecnote/c-sending-scpi-gpib-commands-over-usb-from-c/)  
範例程式碼

[NI-488.2](https://www.ni.com/zh-tw/support/downloads/drivers/download.ni-488-2.html#484357)  
這個包提供了 
1. NI MAX  可以偵測現在GPIB連線的設備 及 下SCPI指令
2. `Ivi.Visa.Interop` 是 NI 提供的 VISA COM 封裝的一部分 可以提供在 C# 開發 加入參考
3. 實際 GPIB 的驅動程式
  

```
NI-488.2（National Instruments 488.2）
是一個用於控制和通信的標準，
它是一種基於IEEE 488.2規範的數據總線標準，
通常稱為GPIB（General Purpose Interface Bus）。
GPIB是一種常見的電子設備連接標準，特別是在實驗室和測試環境中。

NI-488.2是National Instruments為其硬體和軟體開發的一個驅動程式和工具套件。
它提供了一個介面，使得NI的硬體能夠通過GPIB與計算機進行通信。
NI-488.2驅動程式允許你的應用程式使用高級程式設計語言（如C#、LabVIEW等）
來控制和監視連接到GPIB總線的儀器和設備。

應用範疇包括數位多用途儀器、示波器、頻譜分析儀、信號發生器等各種儀器。
NI-488.2也支援設備間的通信，使得多個儀器能夠協同工作。

總的來說，NI-488.2（GPIB）提供了一個通用的、可擴展的標準，
用於實現測試和測量系統中各種設備的連接和通信。
```
  

---
### NI MAX  
![how use NI MAX](/GPIB/NImax_how_use.PNG)

---
### code  

---
需加入參考  
![方案總管](/GPIB/方案總管.PNG)  
---
不用加using  
![不用加using](/GPIB/using.PNG)  
  
---
做個物件
```csharp
Ivi.Visa.Interop.ResourceManager rm = new Ivi.Visa.Interop.ResourceManager();
Ivi.Visa.Interop.FormattedIO488 ioobj = new Ivi.Visa.Interop.FormattedIO488();
```

---
連線
```csharp
private void btn_conn_Click(object sender, EventArgs e)
{
    object[] idnItems;

    ioobj.IO = (Ivi.Visa.Interop.IMessage)rm.Open("GPIB0::10::INSTR",Ivi.Visa.Interop.AccessMode.NO_LOCK, 0, "");
}
```
---
詢問
```csharp
private void btn_SendAndRead_Click(object sender, EventArgs e)
{
    ioobj.WriteString("*IDN?", true);

    object[] idnItems = (object[])ioobj.ReadList(Ivi.Visa.Interop.IEEEASCIIType.ASCIIType_Any, ",");

    foreach (object idnItem in idnItems)
    {
        textBox1.Text += idnItem+"\r\n";
    }
}
```

---
### 後續  
為何  我在使用  Ivi.Visa.Interop  只需加入參考  不用在winform 上加using

```
在 .NET 框架中，當你在 Visual Studio 中使用 COM Interop（元件互通性）引用時，有時候在程式碼中不需要加入 `using` 陳述式來引用相關的命名空間，這是因為 COM Interop 引用會自動處理對 COM 對象的引用。這是一個在使用 COM Interop 時的常見行為。

在你的情境中，`Ivi.Visa.Interop` 這個命名空間可能已經在 COM Interop 引用中被處理了，所以你在程式碼中不需要手動加入 `using`。這在使用 COM 對象時是一個方便的功能，因為你不需要額外的設定，而是直接使用 COM 對象提供的方法和屬性。

請注意，這種自動加入 `using` 的行為僅適用於 COM Interop，對於其他 .NET 組件，你仍然需要使用 `using` 來引用相關的命名空間。如果有任何進一步的問題或需要更多解釋，請隨時告訴我。```