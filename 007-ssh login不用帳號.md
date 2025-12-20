
## Rocky linux
檢查 /etc/ssh/sshd_config.d/ 下的子設定
在 Rocky Linux 9 中，主要的 sshd_config 可能會被 /etc/ssh/sshd_config.d/ 目錄下的 .conf 檔案覆蓋。
如果裡面有類似 50-redhat.conf 的檔案，請檢查它是否也包含了 PasswordAuthentication no。
子目錄下的設定通常會優先於主檔案。
將其改為 yes 或刪除該行，然後重啟 sshd。

修改後，使用檢查語法   sshd -t  確認配置檔載入沒有問題
重啟sshd
  sudo systemctl restart sshd
  sudo systemctl status sshd

tips
   請問如果子目錄有2個檔案， a檔案寫著 PasswordAuthentication no ，b檔案寫著 PasswordAuthentication yes ，哪一個最後會生效? 
   答案:
      在 Rocky Linux 9 (以及多數現代 Linux 發行版) 中，這個問題的答案取決於 「載入順序」，而且結果可能跟你想像的不同。
結論：檔案名稱排序較前的檔案（a檔案）會生效。

SSH 設定檔的邏輯是 「先入為主（First Match Wins）」。當 SSH 服務讀取設定時，一旦找到某個參數的設定值，之後出現的相同參數都會被忽略。
字母順序載入： sshd_config 通常透過 Include /etc/ssh/sshd_config.d/*.conf 來讀取子目錄。系統會依照檔案名稱的 字母/數字排序 讀取。
優先權： 因為 a 的排序在 b 之前，系統會先讀取到 a 檔案中的 PasswordAuthentication no。
忽略後續： 當系統隨後讀取到 b 檔案時，因為已經有了設定，它會直接無視 b 檔案中的 PasswordAuthentication yes。

檢查語法  sshd -T | grep -i passwordauthentication

建議做法
  不要修改預設檔案 50-xxx ，因為套件更新後 50-xxx檔案會被更新，造成設定不見
  建立一個新檔案   00-custom.conf (它會優先在檔案 50 載入)

  作法二：使用 Match 區段（更精密的做法）
    如果您擔心全面開啟密碼登入不安全，您可以在主設定檔 /etc/ssh/sshd_config 的最後面（或自訂檔案中）針對「特定一般帳號」開啟權限。
    Match 區段的特性是它會覆蓋全域設定：Plaintext
    # 在檔案末尾加入
      Match User 您的帳號名稱
      PasswordAuthentication yes
    這樣做的好處是：其他帳號（如 root）依然維持不可用密碼登入的高安全性，只有您指定的帳號可以透過密碼登入。

## 帳號加入到 wheel
su -
usermod -aG wheel <您的帳號>

檢查  id <你的帳號>


## linux client 
執行 ssh-copy-id

## windows client
scp .\id_rsa.pub XXXXX@<RemoteIP>:/tmp

#linux side, change file name
mv id_rsa.pub to a .ssh\authorized_keys
