## LINQ 常見用法
### 常用的擴充方法

 **最常用的 LINQ 擴充方法**，用最白話、最容易記的方式來解釋它們「到底在做什麼」：

| 方法          | 中文白話說明                              | 主要用途                          | 回傳型別簡述                  | 立即執行 / 延遲執行 | 超常見使用場景舉例                              |
|---------------|--------------------------------------------|-----------------------------------|-------------------------------|----------------------|--------------------------------------------------|
| **Where**     | 「幫我把符合條件的留下，其他通通丟掉」     | 過濾（篩選）                      | IEnumerable<T>                | 延遲                 | 年齡 > 18、價格 >= 100、名字開頭是 "A"          |
| **Select**    | 「把每個東西變成我想要的樣子/型別」        | 轉換、投影、挑選欄位              | IEnumerable<新型別>           | 延遲                 | 只取名字、轉成大寫、new 一個新物件、計算折扣價 |
| **Any**       | 「有沒有至少一個符合條件的？」             | 存在性檢查（有沒有）              | bool                          | 立即（找到就停）     | 有沒有成人？購物車有東西嗎？有錯誤訊息嗎？      |
| **OrderBy**   | 「照某個欄位由小到大排好」                 | 升冪排序（預設）                  | IOrderedEnumerable<T>         | 延遲                 | 依年齡、價格、日期、姓名排序                     |
| **GroupBy**   | 「把相同 key 的東西分到同一組」            | 分組                              | IEnumerable<IGrouping<TKey,T>>| 延遲                 | 依城市分組、依部門分組、依等級統計               |
| **Count**     | 「總共有幾個？」                           | 計算數量                          | int / long                    | 立即                 | 總共有幾筆資料？符合條件的幾個？                 |
| **Sum**       | 「全部加起來多少？」                       | 合計（數值欄位）                  | 數值型別（int、decimal 等）   | 立即                 | 總金額、總分數、總庫存量                         |

### 快速記憶 + 範例對照

```csharp
var numbers = new List<int> { 5, 12, 3, 8, 15, 7, 20 };

// Where     → 留下 > 10 的
var big = numbers.Where(x => x > 10);           // 12,15,20

// Select    → 每個都乘 2
var doubled = numbers.Select(x => x * 2);       // 10,24,6,16,30,14,40

// Any       → 有沒有 > 18 的？
bool hasAdult = numbers.Any(x => x > 18);       // true

// OrderBy   → 由小到大排
var sorted = numbers.OrderBy(x => x);           // 3,5,7,8,12,15,20

// GroupBy + Count 經典組合
var scores = new[] { ("小明", 85), ("小華", 92), ("小明", 78), ("小華", 95) };
var group = scores.GroupBy(x => x.Item1)                // 依名字分組
                 .Select(g => new { Name = g.Key, Avg = g.Average(x => x.Item2) });

// Count
int howMany = numbers.Count();                  // 7
int howManyBig = numbers.Count(x => x > 10);    // 3

// Sum
int total = numbers.Sum();                      // 70
decimal totalPrice = products.Sum(p => p.Price);
```


