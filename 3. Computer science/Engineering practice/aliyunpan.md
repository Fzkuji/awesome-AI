
https://github.com/tickstep/aliyunpan

### 同步备份文件

```
aliyunpan sync start -ldir "/usr/src/app/fzkuji/memoGPT" -pdir "/DESKTOP-S2FLSAN/memoGPT" -mode "download"
```

```
aliyunpan sync start -ldir "/usr/src/app/fzkuji/memoGPT/out-owt" -pdir "/DESKTOP-S2FLSAN/memoGPT/out-owt" -mode "upload"
```

```
aliyunpan download --saveto /usr/src/app/fzkuji/memoGPT /DESKTOP-S2FLSAN/memoGPT
```

 - aliyunpan download - p 是并行下载

aliyunpan sync start -ldir "/usr/src/app/fzkuji/memoGPT" -pdir "/DESKTOP-S2FLSAN/memoGPT" -mode "download"

```
aliyunpan download --saveto /data/local/amlworker16-0/fzkuji/better-nanoGPT/data/openwebtext/train.bin /memoGPT/data/openwebtext/gpt2/train.bin -p 3
```



upload /usr/src/app/fzkuji/memoGPT/out-owt/ckpt.pt /tmp

/usr/src/app/fzkuji/memoGPT/tmp/ckpt.pt

```
aliyunpan sync start -ldir "/data/local/amlworker1-0/fzkuji" -pdir "/mac.Zichuans-MacBook-Pro.local/memoGPT" -mode "download"
```



aliyunpan download --saveto /root/autodl-tmp /mac.Zichuans-MacBook-Pro.local/memoGPT -p 3