# item2

> 安装 iTerm2
> 

```c
brew cask install iterm2
```

> iTerm2保持ssh连接不断开
> 

```bash
vim ~/.ssh/config

Host *
    ServerAliveInterval 60

```