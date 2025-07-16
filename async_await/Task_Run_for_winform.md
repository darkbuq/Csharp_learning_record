# Task.Run(...)  for winform


```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace task_run_02
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private async void buttonStart_Click(object sender, EventArgs e)
        {
            txt_result.Text += "主線啟動中...\r\n";
            txt_result.Text += "支線啟動中...\r\n\r\n";

            // 啟動支線任務
            var longRunningTask = Task.Run(() => HeavyCalculation());

            // 主線每 0.5 秒更新一次
            for (int i = 0; i < 10; i++)
            {
                txt_result.Text += $"主線工作中... {i} - {DateTime.Now:HH:mm:ss.fff}\r\n";
                await Task.Delay(500); // 用 await，避免卡住 UI
            }

            await longRunningTask;
            txt_result.Text += "支線計算完成！\r\n";
        }

        // 因為是 Task.Run 內部，所以不能直接改 UI，要用 Invoke
        private void HeavyCalculation()
        {
            for (int i = 0; i < 20; i++)
            {
                string message = $"        支線工作中... {i} - {DateTime.Now:HH:mm:ss.fff}\r\n";

                // 透過主執行緒更新 UI
                this.Invoke(new Action(() => txt_result.Text += message));
                // () => ....   //這是Lambda表達式
                // new Action   //這是 C# 內建的委派類型


                Thread.Sleep(500); // 模擬工作延遲
            }
        }

        private void button2_Click(object sender, EventArgs e)
        {
            txt_result.Text += "@@  測試UI沒有卡住\r\n";
        }
    }
}
```


