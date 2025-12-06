# 開始製作Template

步驟一：開始複製 (Clone)

    找到 Template： 在 Proxmox VE 介面的左側資源樹中，找到您剛剛轉換好的 Template（它的圖示會是一個藍色的文件或紙張圖案）。

    開啟複製選項： 在該 Template 上點擊 右鍵，選擇 Clone。

    設定新 VM ID 和名稱：

        New VM ID: 填入新 VM 的 ID (例如：102)。

        Name (名稱): 填入新 VM 的 Hostname (例如：rocky-webserver-01)。


        <img width="629" height="281" alt="圖片" src="https://github.com/user-attachments/assets/2d3c92d7-ee74-4fe4-b91d-60a46df7c1cf" />

模式,完整複製 (Full Clone),連結複製 (Linked Clone)
速度,較慢 (需要複製所有資料),極快 (只需複製元資料)
空間,佔用完整磁碟空間,極省空間 (只佔用與 Template 的差異)
獨立性,獨立，刪除 Template 不影響,依賴 Template，Template 被刪除則 VM 損壞
用途,需要獨立、可遷移的正式環境,快速測試、實驗或開發環境 (推薦)


步驟三：配置 Cloud-Init 參數 (啟動前的魔法)

Clone 完成後，請不要急著啟動新 VM。由於您在 Template 中安裝了 Cloud-Init，您現在可以透過 PVE 介面來設定新機器的身份資訊。

在新 VM 的選單中，點擊 Cloud-Init 分頁，設定以下關鍵資訊：

    使用者 (User): (可選) 您可以在此覆寫 Template 預設的使用者名稱。

    密碼或 SSH Key：

        Password: 設定新機器的登入密碼。

        SSH Public Key: 強烈推薦 貼上您本地電腦的公鑰。這樣在啟動後，您可以直接 SSH 連線，無需密碼。

    IP Configuration (IP 設定) 🌐：

        這是最重要的設定，用來指定新機器的 IP 地址。

        DHCP： 如果您希望由 DHCP 伺服器自動分配 IP，則選擇 DHCP。

        Static (靜態)： 選擇靜態，並填寫 IP/CIDR (例如：192.168.1.100/24) 和 Gateway (閘道)。

    Hostname： 如果在 Clone 步驟中沒設定，這裡也可以設定，Cloud-Init 會將其寫入 Rocky Linux。


可以，在 VM 成功啟動並完成 Cloud-Init 初始化後，Cloud-Init Drive 可以安全地移除。
