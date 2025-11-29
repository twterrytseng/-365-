
# Linux 帳號名稱修改步驟

假設：

    您想修改的 舊帳號名稱 是：wrongname

    您想修改成的 新帳號名稱 是：correctname

步驟 1: 登入並確認權限

請以 root 身份，或使用有 sudo 權限的帳號登入 PVE Shell。

步驟 2: 踢出並停止該使用者所有程序

如果使用者正在使用中，必須先將其登出並終止其所有程序，以避免資料損毀。
# 檢查該使用者是否登入
who | grep wrongname

# 如果有登入，使用 pkill 終止其所有程序
pkill -u wrongname

# 語法：groupmod -n 新群組名稱 舊群組名稱
sudo groupmod -n correctname wrongname

# 語法：usermod -l 新帳號名稱 -d 新家目錄路徑 -m 舊帳號名稱
sudo usermod -l correctname -d /home/correctname -m wrongname

# 語法：groupmod -n 新群組名稱 舊群組名稱
sudo groupmod -n correctname wrongname
