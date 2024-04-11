# TrackBar 作動延遲問題
如果 TrackBar 的滑桿移動時  
會觸發 實際動作且動作需要時間  
那大幅度的滑桿移動就會帶來 數量龐大的動作執行  

小問題就是 UI執行緒鎖死一段時間  
大問題就是 實際動作的硬體可能無法承受數量龐大的操作 而損壞

---

## 解法一
當 `TrackBar` 的 `Scroll事件` 觸發時
就做一個 `Timer` 啟動
```csharp
private void trb_Tsolpe_LT_Scroll(object sender, EventArgs e)
{
    // 每次 TrackBar 拉動時啟動計時器
    debounceTimer.Stop();
    debounceTimer.Start();
}
```
然後 `Timer` 的 `Tick事件` 就放真正的動作
```csharp
private void debounceTimer_Tick(object sender, EventArgs e)
{
    // 計時器過後處理事件的程式碼放在這裡
    //txt_note.Text = trb_Tsolpe_LT.Value.ToString();

    txt_Tslope_LT_dec.Text = trb_Tsolpe_LT.Value.ToString();
    this.Refresh();
    btn_LT_slope_set_Click(sender, e);

    // 計時器觸發後停止計時器
    debounceTimer.Stop();
}
```

因為 `Timer` 有設定響應頻率  
所以動作可以控制再一個時間間隔上才響應
就不會 `過度作動`


