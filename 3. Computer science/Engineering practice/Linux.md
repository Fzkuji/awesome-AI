



## Linux 简单查看网卡实时网速


### 简易方法

```sh
watch -n 1 ifconfig
```

> ifconfig 实时查看效果 
> **RX: 接收流量** 
> **TX: 发送流量** 
> **计算方法：** (**KB** = 数值/1000) (**MB** = 数值/100000) 
> **退出查看： 按 ctrl+c 键**

https://cloud.tencent.com/developer/article/2149404

### [Nethogs](https://github.com/raboof/nethogs#readme)

```bash
sudo apt install nethogs

sudo nethogs
```

动态结果，可以查看使用的命令名称


## 查看硬盘占用


```shell
df -h
```



## 查看文件大小

查看当前目录下所有文件和文件夹的大小：

```shell
du -h --max-depth=1
```

缩写成：

```shell
du -h -d 1
```



