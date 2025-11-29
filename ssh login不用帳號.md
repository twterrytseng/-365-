
## linux client 
執行 ssh-copy-id

## windows client
scp .\id_rsa.pub XXXXX@<RemoteIP>:/tmp

#linux side, change file name
mv id_rsa.pub to a .ssh\authorized_keys
