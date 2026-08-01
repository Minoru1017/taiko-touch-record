# GitHub Token 學習筆記（太鼓手感紀錄表）

> 這份筆記說明：網頁按「同步到 GitHub」時，為什麼需要 Token、它做什麼、以及怎麼建立／保存／撤銷。  
> 相關頁面：https://minoru1017.github.io/taiko-touch-record/

---

## 一句話先懂

**Token = 你給網頁用的「臨時鑰匙」。**  
網頁本身沒有登入你的 GitHub，所以要用這把鑰匙，才能代表你去改 repo 裡的 `TAIKO_FEEL_LOG.md`。

沒有 Token 時，你仍可：
- 在頁面看紀錄
- 「產生預覽」「複製紀錄內容」
- 改用 GitHub 網頁手動編輯

有 Token 時，才可以：
- 直接按「同步到 GitHub」，把今天的紀錄寫進 repo

---

## 為什麼需要它？

| 角色 | 平常身分 | 能不能改你的檔案？ |
|------|----------|-------------------|
| 你本人在 GitHub 網站 | 已登入帳號 | 可以 |
| 公開網頁（GitHub Pages） | 只是訪客 | **不行**（只能讀公開內容） |
| 網頁 + 你貼上的 Token | 暫時拿到你授權的權限 | **可以**（只限你授權的範圍） |

所以流程是：

```text
填表 → 產生預覽 → 勾選確認 → 同步到 GitHub
                              ↓
                     瀏覽器帶著 Token
                              ↓
                     呼叫 GitHub API
                              ↓
                     更新 TAIKO_FEEL_LOG.md
                              ↓
                     網頁紀錄表下次讀到最新內容
```

技術名稱：GitHub **Personal Access Token（PAT）**，搭配 **Contents API** 讀寫檔案。

---

## Token 存在哪裡？安不安全？

| 項目 | 說明 |
|------|------|
| 存在哪 | **這台裝置的瀏覽器** `localStorage`（鍵名 `gh_token`） |
| 會不會進 repo？ | **不會**。不要把 Token 貼進任何 `.md` / 程式碼再 commit |
| 會不會上傳到網頁主機？ | **不會**。同步時是你的瀏覽器直接打 `api.github.com` |
| 換手機／清資料？ | 要重新貼一次 Token |
| 別人用你的電腦開同一網站？ | 若同瀏覽器未清除， theoretically 看得到；用完可按「清除」 |

建議權限開到最小：
- 只用 **Fine-grained token**（細粒度）
- 只選 repo：`Minoru1017/taiko-touch-record`
- 只開 **Contents: Read and write**
- 不要開整個帳號的無限權限

---

## 怎麼建立（第一次）

### 1. 登入 GitHub
用會管理 `taiko-touch-record` 的那個帳號登入。

### 2. 打開建立頁
直接連結（建議收藏）：

https://github.com/settings/personal-access-tokens/new

或手動路徑：

1. 右上角頭像 → **Settings**
2. 左側最下方 → **Developer settings**
3. **Personal access tokens** → **Fine-grained tokens**
4. **Generate new token**

### 3. 填這些欄位

| 欄位 | 建議填法 |
|------|----------|
| Token name | `taiko-touch-record`（好認就好） |
| Expiration | 例如 90 days；到期後要重建 |
| Repository access | **Only select repositories** → 選 `taiko-touch-record` |
| Permissions → Repository permissions → **Contents** | **Read and write** |
| 其他權限 | 維持 No access（不必開） |

### 4. Generate token
按 **Generate token** 後，畫面會出現一串以 `github_pat_` 開頭的字。

**只會完整顯示一次。** 立刻複製，下一步貼到紀錄表。

若關掉後忘記複製：到 Fine-grained tokens 列表只能撤銷／重建，**無法再看到舊字串**。

---

## 怎麼用在紀錄表

1. 打開 https://minoru1017.github.io/taiko-touch-record/
2. 找到 **GitHub Token 設定**
3. 貼上 Token → 按 **儲存**（會先驗證能不能讀本 repo）
4. 也可先按 **測試連線**
5. 之後：填紀錄 → **產生預覽** → 勾選確認 → **同步到 GitHub**

成功時通常會看到「已同步」與可點的 commit 連結。

---

## 常見狀況速查

| 狀況 | 可能原因 | 怎麼辦 |
|------|----------|--------|
| 儲存／測試失敗，說 Token 無效 | 貼錯、過期、已撤銷 | 重建 Token 再儲存 |
| 權限不足 / 403 | Contents 沒開 Read and write，或選錯 repo | 編輯 token 權限，或重建 |
| 找不到檔案 / 404 | repo 名稱不對，或 token 看不到該 repo | 確認只授權了正確 repo |
| 換瀏覽器後不能同步 | Token 存在舊瀏覽器 | 再貼一次並儲存 |
| 不小心外洩 Token | 別人可能拿去改你的檔 | 立刻到 GitHub **Revoke** 該 token，再重建 |

撤銷位置：  
https://github.com/settings/personal-access-tokens  
找到該 token → **Revoke**

---

## 和「Cursor 確認同步」差在哪？

同一份日誌，兩條路都能寫進 GitHub：

| 方式 | 誰在寫檔？ | 要不要網頁 Token？ |
|------|------------|--------------------|
| 網頁「同步到 GitHub」 | 你的瀏覽器 + Token 呼叫 API | **要** |
| Cursor agent（回覆「確認同步」） | Agent 用已連線的 GitHub 權限 commit／push | **不要**網頁 Token |

兩種都合法；網頁 Token 是為了**手機瀏覽器自己寫入**，不必每次找 agent。

---

## 最小記憶口訣

1. **Token = 臨時鑰匙，不是密碼本體，但一樣要保密**  
2. **只授權這一個 repo 的 Contents 讀寫**  
3. **貼在網頁設定裡，不要貼進日誌檔**  
4. **忘記字串就重建；懷疑外洩就撤銷**

收藏這兩個連結就夠用：

- 建立 Token：https://github.com/settings/personal-access-tokens/new  
- 管理／撤銷：https://github.com/settings/personal-access-tokens  
- 紀錄表：https://minoru1017.github.io/taiko-touch-record/
