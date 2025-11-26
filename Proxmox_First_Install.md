## 安裝 Proxmox VE 9.0.1 
## 設備 ASUS NUC 14 pro , CPU Intel U5 125H , RAM 64G, SSD 500G
## 準備 USB 1支
## 下載 Proxmox VE 9.0.1   https://enterprise.proxmox.com/iso/proxmox-ve_9.1-1.iso
## 下載 rufus iso to USB   https://release-assets.githubusercontent.com/github-production-release-asset/2810292/cd54617d-b9bf-4b81-86cb-66944e75bfab?sp=r&sv=2018-11-09&sr=b&spr=https&se=2025-11-26T15%3A13%3A33Z&rscd=attachment%3B+filename%3Drufus-4.11p.exe&rsct=application%2Foctet-stream&skoid=96c2d410-5711-43a1-aedd-ab1947aa7ab0&sktid=398a6654-997b-47e9-b12b-9515b896b4de&skt=2025-11-26T14%3A12%3A46Z&ske=2025-11-26T15%3A13%3A33Z&sks=b&skv=2018-11-09&sig=f9t4v2vvA3MIBzjvFxi1ZJ2MkKTKxX0LYt1cck7PWfc%3D&jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmVsZWFzZS1hc3NldHMuZ2l0aHVidXNlcmNvbnRlbnQuY29tIiwia2V5Ijoia2V5MSIsImV4cCI6MTc2NDE2ODI3NSwibmJmIjoxNzY0MTY3OTc1LCJwYXRoIjoicmVsZWFzZWFzc2V0cHJvZHVjdGlvbi5ibG9iLmNvcmUud2luZG93cy5uZXQifQ.HBslU6ZJV4eZAGktcXeSl8qeQQTmuq_CUPSoT6I8gQ4&response-content-disposition=attachment%3B%20filename%3Drufus-4.11p.exe&response-content-type=application%2Foctet-stream

Step 1
  將下載好ISO檔案，使用RUFUS tool 燒錄至 USB

Step 2
  設定 ASUS NUC bios 選擇從 USB booting

Step 3
  進入到安裝畫面，依照提示一步一步完成即可

Step 4
  網路配置
    Proxmox VE (PVE) 中，所有網路介面的配置都儲存在 /etc/network/interfaces 這個文件中
    包含了一個用於管理和虛擬機連線的單一橋接介面 (vmbr0)
   
    #  Example
    # 這是 loopback 介面的配置，通常保持不變
auto lo
iface lo inet loopback

# -------------------------------------------------------------
# 實體網路介面：
# 這裡定義了實際連接到網路線的實體網卡，它不帶有 IP 位址。
# 它被 'vmbr0' 用作橋接埠。
# 請將 'enp1s0' 替換為您的實際網卡名稱 (例如 eth0, eno1 等)。
iface nic0 inet manual

# -------------------------------------------------------------
# 虛擬網路橋接 (Linux Bridge):
# vmbr0 是虛擬網路橋接器，這是 PVE 節點用來進行管理和虛擬機連線的主要介面。
# 它帶有 PVE 主機的管理 IP 位址。
auto vmbr0
iface vmbr0 inet static
    # PVE 主機的管理 IP 位址和 CIDR (例如 /24)
    address 192.168.1.100/24 
    
    # 網路的閘道器 (通常是您的路由器 IP)
    gateway 192.168.1.1      
    
    # 將實體網卡 () 橋接到這個虛擬橋接器
    bridge-ports nic0      
    
    # STP (Spanning Tree Protocol) 關閉
    bridge-stp off           
    
    # Forward Delay (延遲) 設為 0
    bridge-fd 0              

# -------------------------------------------------------------
# 其他可能的額外配置：

# 範例：第二個橋接器 (用於隔離網路或第二張網卡)
# auto vmbr1
# iface vmbr1 inet static
#     address 10.10.10.1/24
#     bridge-ports eth1 
#     bridge-stp off
#     bridge-fd 0

區塊,作用,說明
auto lo,Loopback,系統內部自我通訊用，保持不變。
iface enp1s0 inet manual,實體網卡,實體網卡必須設定為 manual（手動），且不能配置 IP 位址。它的唯一作用是作為橋接器 vmbr0 的埠。
auto vmbr0,虛擬橋接器啟動,確保系統開機時自動啟動 vmbr0。
iface vmbr0 inet static,橋接器設定,這是 PVE 主機的管理網路介面，必須使用 static（靜態 IP）。
address,IP 和遮罩,格式為 IP/CIDR，例如 192.168.1.100/24。
gateway,閘道器,定義主機對外網路的出口。
bridge-ports,橋接埠,指定 vmbr0 要將哪個實體網卡連接起來。

# 在修改前，強烈建議您先備份原始文件：
sudo cp /etc/network/interfaces /etc/network/interfaces.bak

# 重新啟動網路服務 (建議在控制台或 KVM 下操作，以防連線中斷)
sudo systemctl restart networking

# 或者，最保險的方法是重新啟動 PVE 主機
sudo reboot
