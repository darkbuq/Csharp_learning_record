# 如果我要用 C# 去 insert MSSQL 的某個table 但是要管理的 column 數量很多 有什麼好的寫法
當您需要傳遞多個參數到一個方法或函數中時，可以使用 Parameter Object 來組織這些參數  
以下是一個簡單的例子：

假設您有一個方法用於創建一個新的用戶帳戶，需要傳遞多個相關的參數，包括用戶名、密碼、電子郵件等。原始的方法可能如下所示：

```csharp
public void CreateUserAccount(string username, string password, string email, string firstName, string lastName)
{
    // 創建新的用戶帳戶的邏輯
}
```

這個方法的參數列表可能變得很長，並且當有新的相關參數加入時，代碼會變得難以閱讀和維護。現在，您可以使用 Parameter Object 來改進代碼：

```csharp
public class UserAccountParameters
{
    public string Username { get; set; }
    public string Password { get; set; }
    public string Email { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}

public void CreateUserAccount(UserAccountParameters parameters)
{
    // 創建新的用戶帳戶的邏輯，使用 parameters 對象中的屬性
}
```

現在，您可以將相關的參數封裝到 UserAccountParameters 對象中。這樣，您可以將方法的參數列表簡化為只有一個對象，使得代碼更易讀並且易於擴展。使用方式如下：

```csharp
UserAccountParameters parameters = new UserAccountParameters();
parameters.Username = "john123";
parameters.Password = "password";
parameters.Email = "john@example.com";
parameters.FirstName = "John";
parameters.LastName = "Doe";

CreateUserAccount(parameters);
```

這樣，您可以更清楚地看到每個參數的含義，並且可以根據需要更輕鬆地添加、修改或刪除參數。同時，方法的接口也更具可讀性和可維護性。