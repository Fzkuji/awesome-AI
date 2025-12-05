创建新的session：

```shell
tmux new -s my_session	# (Optional)
```


连接已有session：

```shell
tmux attach-session -t my_session
```


列出所有sessions：

```shell
tmux list-sessions
```


To kill a session with a known name (e.g., "my_session"), use the `-t` option:

```
tmux kill-session -t my_session
```

`Ctrl-b` then `[` then you can use your normal navigation keys to scroll around (eg. `Up Arrow` or `PgDn`). Press `q` to quit scroll mode.


如果tmux session的窗口大小出现的了缩小：
https://unix.stackexchange.com/questions/218815/tmux-force-resize-window