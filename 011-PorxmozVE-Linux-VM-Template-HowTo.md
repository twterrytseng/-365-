## 011-PorxmozVE-Linux-VM-Template-HowTo.md ##
Used a Rocky linux 9.6 as a VM teamplate

### Step 1 ###
先安裝好1台 Rocky Linux 9.6 (ISO - mini)
在 Rocky Linux 9 安裝完成並開機後，請使用 root 權限執行以下操作：
  dnf update -y

  # 安裝必要套件： Rocky Linux 的套件名稱與 Ubuntu 類似，但指令不同。 #
  dnf install cloud-init qemu-guest-agent -y
  
  # 啟用 QEMU Guest Agent： 安裝後需要手動啟用服務，確保 PVE 能抓到 IP。#
  systemctl enable --now qemu-guest-agent
