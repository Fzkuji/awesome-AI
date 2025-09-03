


[如何使用WSL在Windows上安装Linux](https://learn.microsoft.com/zh-cn/windows/wsl/install)

WSL基于Clash的全局代理：

```
#!/bin/bash
host_ip=$(cat /etc/resolv.conf |grep "nameserver" |cut -f 2 -d " ")
export ALL_PROXY="http://$host_ip:7890"
```



