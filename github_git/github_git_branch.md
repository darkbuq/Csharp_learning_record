# github & git & branch(分支)

# 基本概念  


## 👉 ** 正統多人協作（推薦）**

```
clone → 開新分支(本地) → 本地改程式 → commit(本地) → push 分支 → PR → merge
```

### 這是 GitHub 團隊開發最常見的方式。

---

# ✅ **分支通常是在「本地端」開，然後 push 上 GitHub**

多人開發最常見流程：

1. **git clone** 把專案拉下來
2. 在自己的本地端開分支，例如：

   ```
   git checkout -b feature/login
   ```
3. 寫完程式後 commit
4. 把分支 push 到 GitHub：

   ```
   git push --set-upstream origin feature/login
   ```

   → GitHub 上就會自動出現這個分支
5. 上 GitHub 開 Pull Request
6. 主程式負責人 review & merge
7. 刪掉分支（本地 & 遠端都可刪）

---


### 1️⃣ 為什麼用 `checkout`？

`git checkout` 原本是用來 **切換分支** 或 **還原檔案** 的指令。

* 單純切換分支：`git checkout main` → 直接切換到 `main` 分支
* 建立 + 切換分支：`git checkout -b 新分支名稱` → 同時建立新分支並切換過去

也就是說，`checkout` 同時有 **切換** 和 **建立** 分支的能力。

> 其實在新版本 Git 也可以用 `git switch -c feature/login` 來代替，語意更清楚：`switch` = 切換，`-c` = create。

---

### 2️⃣ 為什麼分支叫 `feature/login`？

這個名字其實是 **開發慣例（convention）**，不是 Git 強制的。

* `feature/` → 表示這個分支是開新功能（feature）
* `login` → 表示這個功能是「登入功能」

所以整個分支名稱 `feature/login` 很直觀地告訴你：

> 這是一個用來開發登入功能的新功能分支

常見的分支命名慣例還有：

* `bugfix/xxx` → 修 bug
* `hotfix/xxx` → 緊急修正
* `release/xxx` → 發布版本

這樣大家一看分支名稱，就知道這個分支的用途。


