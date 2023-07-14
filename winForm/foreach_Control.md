# How can i foreach all buttons in winforms
```csharp
foreach(var button in this.Controls.OfType<Button>())
{
    do something...
}
```
## 這種做法  有些control會沒訪問到  
## 如果你的表格上有一些按鈕沒有被循環到，可能有以下幾個原因：
```
控件不是直接位於表單的Controls集合中：  
Controls集合只包含直接位於表單上的控件，而不包括控件中的控件。  
如果有一些按鈕是控件中的控件（例如其他面板或組合框），則它們不會出現在Controls集合中。  
您可能需要遍歷查詢的容器控件來找到這些按鈕。  

控件的屬性不正確：可能有一些按鈕的屬性設置導致它們不滿足OfType<Button>()篩選條件。  
例如，如果按鈕的類型不是Button，或者它們的可視性被設置為false，則它們不會被循環到
```

---

如果你的一些GroupBox中的按钮没有变色，可能是因为GroupBox本身也是一个控件容器，它会包含其他控件，例如按钮。在你的代码中，使用`this.Controls.OfType<Button>()`只会遍历窗体的直接子控件，并不会递归遍历容器中的子控件。

为了处理GroupBox中的按钮，你需要修改代码来递归遍历所有的控件，包括窗体和容器控件中的控件。可以使用递归方法来实现：

```csharp
private void SetButtonColors(Control control)
{
    foreach (Control c in control.Controls)
    {
        if (c is Button button)
        {
            button.BackColor = Color.Lime;
        }
        else if (c.HasChildren)
        {
            SetButtonColors(c); // 递归调用以处理容器中的控件
        }
    }
}
```

然后，你可以在你的代码中调用`SetButtonColors`方法来设置所有按钮的背景颜色，包括GroupBox中的按钮：

```csharp
SetButtonColors(this);
```

这样，所有GroupBox中的按钮也会被正确地循环到，并进行背景颜色的变化。


