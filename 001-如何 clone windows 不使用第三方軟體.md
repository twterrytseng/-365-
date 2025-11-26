##
2025-11-25 22:33
如何 clone windows 不使用第三方軟體
參考來源  https://www.youtube.com/watch?v=CDLyNJGzkWE
適用情境:  更換SSD / off-line full-backup c:\
##

## 關機進入到安全模式的疑難排除 再進入下面動作
Command :

    Create Backup File:

dism /capture-image /imagefile:"path\install.wim" /capturedir:"path" /name:"WinBackup" /compress:max /checkintegrity

eg: dism /capture-image /imagefile:D:\backup.wim /capturedir:C:\ /name:"WinBackup" /compress:max /checkintegrity

----------------------------------------------------------------------------------------------------------------------------------
## 先進入到 windows 系統後，打開 cmd -> diskpart -> 把 new hdd 先format 後，再分割磁區。
## 建立開機磁區

    Create EFI Partition : 

cre par efi size=512
format fs=fat32 quick label="System Boot"
assign letter S

----------------------------------------------------------------------------------------------------------------------------------

    Apply backup file :

dism /apply-image /imagefile:backup.wim /index:1 /applydir:W:\

----------------------------------------------------------------------------------------------------------------------------------
