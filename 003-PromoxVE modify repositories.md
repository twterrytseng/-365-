
## Proxmox VE 官方軟體源配置 (PVE Repositories)
## PVE 自己的軟體源是固定的，您主要需要確保您使用的是 非訂閱 (No-Subscription) 版本，以便免費取得更新。
## 編輯 /etc/apt/sources.list.d/pve-no-subscription.list（如果檔案不存在，請自行建立）

# Proxmox VE No-Subscription Repository
deb http://download.proxmox.com/debian/pve trixie pve-no-subscription

##說明：

  ##  http://download.proxmox.com/debian/pve 是官方的 No-Subscription 來源。
  ##  bookworm 是 PVE 8.x 的底層 Debian 版本。
  ##  如果您的 PVE 是 9.x，請將 bookworm 替換為 trixie。
  ##  台灣並沒有官方的 Proxmox 鏡像站，因此您必須直接連線到 PVE 官方伺服器。這個連線通常是穩定的。

## 完整 /etc/apt/sources.list 範例
# -------------------------------------------------------------
# Debian Trixie (PVE 9.x) - 國家高速網路與計算中心 (NCHC) 鏡像站
# -------------------------------------------------------------

# Main components (主要的套件)
deb http://opensource.nchc.org.tw/debian/ trixie main contrib non-free non-free-firmware

# Security updates (安全性更新)
deb http://opensource.nchc.org.tw/debian/ trixie-updates main contrib non-free non-free-firmware

# Security updates (安全性更新 - 官方)
deb http://security.debian.org/ trixie-security main contrib non-free non-free-firmware

# -------------------------------------------------------------
# 移除或註解掉以下行（如存在）：
# deb http://ftp.tw.debian.org/debian/ trixie main
# deb http://ftp.debian.org/debian/ trixie main

## 步驟 1: 備份原始設定 (可選)
cp /etc/apt/sources.list /etc/apt/sources.list.bak
vi /etc/apt/sources.list

## 步 驟 3: 編輯 Proxmox 非訂閱源
## 確保您已正確配置 pve-no-subscription.list：
vi /etc/apt/sources.list.d/pve-no-subscription.list

## 更新
apt update
apt upgrade -y
