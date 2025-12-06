## 011-PorxmozVE-Linux-VM-Template-HowTo.md ##
Used a Rocky linux 9.6 as a VM teamplate

### Step 1 ###
先安裝好1台 Rocky Linux 9.6 (ISO - mini)
在 Rocky Linux 9 安裝完成並開機後，請使用 root 權限執行以下操作：
  dnf update -y

  # 安裝必要套件： Rocky Linux 的套件名稱與 Ubuntu 類似，但指令不同。 
  dnf install cloud-init qemu-guest-agent -y
  
  # 啟用 QEMU Guest Agent： 安裝後需要手動啟用服務，確保 PVE 能抓到 IP。
  systemctl enable --now qemu-guest-agent


### Step 2 ###
RHEL 9 系列對於 SSH 和網路管理有些預設行為需要調整，才能讓 Cloud-Init 完美運作。
Cloud-Init Drive 是 Proxmox VE 實現現代化 VM 樣板管理的核心機制。
這個「驅動器」並非像硬碟一樣用來儲存作業系統，而是一個虛擬的配置注入點，專門用來在虛擬機第一次啟動時，將您在 Proxmox 介面上設定的參數傳遞給 VM 內部的 Cloud-Init 服務。
在 Proxmox VE 中添加 Cloud-Init Drive 的步驟

您問得非常好，Cloud-Init Drive 是 Proxmox VE 實現現代化 VM 樣板管理的核心機制。

這個「驅動器」並非像硬碟一樣用來儲存作業系統，而是一個虛擬的配置注入點，專門用來在虛擬機第一次啟動時，將您在 Proxmox 介面上設定的參數傳遞給 VM 內部的 Cloud-Init 服務。

以下是 Cloud-Init Drive 的完整概念解析：
什麼是 Cloud-Init (雲端初始化)？

Cloud-Init 是一個行業標準的跨平臺程式，用於初始化雲端伺服器實例 (Cloud Server Instances)。

在虛擬化環境中，Cloud-Init 的作用就是：當您從一個 Template 複製出新的 VM 時，它會接管並執行一次性的配置任務，確保這台新機器的設定是獨一無二且符合需求的。

它主要處理的任務包括：

    Hostname：設定主機名稱。

    User：創建新的使用者帳號，或設定密碼。

    SSH Keys：注入您的公鑰，允許無密碼登入。

    Network：配置靜態 IP 或 DHCP。

    Disk Resize：自動擴展根檔案系統，佔滿您為 VM 分配的磁碟空間。

Proxmox Cloud-Init Drive 的工作原理

Cloud-Init Drive (或稱為 ci-drive) 在 PVE 中是一個特定的虛擬裝置，它扮演了資訊傳遞者的角色。
1. 驅動器的本質

當您在 PVE 介面添加這個驅動器時，Proxmox 實際上是：

    在硬體層面：為 VM 添加了一個特殊的虛擬光碟機或區塊裝置。

    在軟體層面：這個虛擬光碟內包含了符合 Cloud-Init 規範的設定檔（例如 user-data、meta-data）。

2. 資訊注入流程 (以複製新 VM 為例)

    PVE 準備數據： 當您從 Template 複製 (Clone) 出一個新的 VM 時，PVE 會根據您在新 VM 的 Cloud-Init 分頁中填寫的 Hostname、IP 地址、使用者密碼和 SSH Key 等資訊，生成對應的配置檔。

    PVE 掛載驅動器： PVE 將這些配置檔打包，並將其作為這個 Cloud-Init Drive 虛擬裝置，在 VM 啟動時自動掛載到 VM 內部。

    VM 讀取配置： 由於您在 Rocky Linux 中已經安裝了 cloud-init 套件，在系統啟動初期，cloud-init 服務會自動偵測到這個特殊的虛擬驅動器。

    執行初始化： cloud-init 讀取驅動器上的配置檔，然後執行所有的初始化工作，例如設定網路卡、重新生成 SSH Key、創建使用者並設定密碼。

    完成： 初始化完成後，VM 才會進入正常的登入提示，此時機器已經配置完成，並且是獨一無二的新實例。

這個步驟是將基礎作業系統 (例如 Rocky Linux 9) 轉換為 Template 之前必做的硬體配置步驟。

    選定 VM：在 Proxmox 介面中，選擇您已經安裝好 Rocky Linux 9 的 VM（必須處於 Stopped 狀態）。

    進入硬體頁面：點擊 Hardware 分頁。

    添加驅動器：

        點擊上方的 Add 按鈕。

        選擇 CloudInit Drive。

    選擇儲存空間：在彈出的視窗中，選擇您要將這個 Cloud-Init 虛擬磁碟儲存的 Storage (儲存空間)（通常選擇用於儲存 VM 磁碟的空間，例如 local-lvm 或 local-zfs）。

    完成：點擊 Add。

添加完成後，您會在 Hardware 列表看到一個新的裝置，名稱類似於 CD-ROM (cloudinit) 或 CloudInit Drive。這就完成了 Template 在 PVE 端的硬體準備工作。



# 清理 SSH Host Keys (防止金鑰重複)：
rm -f /etc/ssh/ssh_host_*

# 清理 Machine ID (修正版)： 在 RHEL/Rocky 9 中，處理 Machine ID 的最佳方式如下
# 清空 machine-id 內容，但保留檔案 (systemd 需要檔案存在)
truncate -s 0 /etc/machine-id

# 刪除 dbus id (如果有)
rm -f /var/lib/dbus/machine-id

# 清除dnf cache
dnf clean all
rm -rf /var/log/*
history -c

# 關機
poweroff
