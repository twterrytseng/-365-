## 建立一般使用者帳號 @pve
## 提供連線 PVE portal

# PVE 中，帳號領域主要有兩種內建選項：
#   pam 領域： 映射到 PVE 底層的 Linux 系統使用者 (本地帳號)。
#   pve 領域： 儲存在 PVE 自己的 內部資料庫 (Internal DB) 中。

*** 選擇 pam 的理由 (建議)****
#理由,說明
#1. SSH/Console 存取 (核心優勢),pam 帳號同時是 Linux 系統帳號。 您可以使用這個帳號透過 SSH 或 PVE 的實體 Console 登入底層作業系統，進行檔案系統修復、網路配置修改或執行任何 CLI 任務，無需再額外建立帳號。
#2. 權限提升 (Sudo/Su),pam 帳號可以被配置使用 sudo 或 su 來執行需要 root 權限的操作（如您前一個問題所述），這是管理 Linux 伺服器的標準、安全的做法。
#3. 安全性標準,pam 領域使用標準的 Linux 密碼散列 (hashing) 機制，並可以整合到標準的 Linux 安全稽核流程中。
#4. 單點密碼 (Single Password),一個密碼可以同時用於 Web 介面登入和 SSH 命令行存取，簡化了使用者的記憶負擔。

# Create a user account
sudo addsers <a/c>
sudo usermod -aG sudo <a/c>
## usermod： 修改使用者帳號。
## -aG： 將使用者追加 (append) 到指定的附加群組 (Group)。
## sudo： 這是擁有 sudo 權限的群組名稱。

2️⃣ 在 PVE 裡建立對應的使用者 x001@pam

登入 PVE（用 root@pam 先登）：
點左側 Datacenter
上面選單點 Permissions → Users
點 Add
User ID：x001
Realm：選 pam
其它可以先用預設

儲存

這一步是告訴 PVE：「有一個來自 Linux PAM 的使用者叫 x001 可以用來登入」。
