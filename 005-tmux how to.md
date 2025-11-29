##  tmux（Terminal Multiplexer）= 終端多工器

用一句話解釋：
SSH 進 Linux 後，如果網路中斷，你的工作不會被中斷；你可以同一個 SSH 同時開很多分頁；斷線後再連上依然保持工作狀態。

# rhel/rocky
yum install tmux -y


# 常用快速表

| 功能         | 按法                    |
| ---------- | --------------------- |
| 分割左右       | `Ctrl+b %`            |
| 分割上下       | `Ctrl+b "`            |
| 離開 session | `Ctrl+b d`            |
| 新視窗        | `Ctrl+b c`            |
| 切換視窗       | `Ctrl+b n / Ctrl+b p` |
| 切換 panel   | `Ctrl+b + 方向鍵`        |
| 列出視窗       | `Ctrl+b w`            |
| 關閉當前 panel | `Ctrl+b x`            |
