# FortiClient

### **打开后空白处理**

```jsx
launchctl print-disabled system

sudo launchctl enable system/com.fortinet.fctservctl
sudo launchctl enable system/com.fortinet.fctctl
sudo launchctl enable system/com.fortinet.config
```

### 解除锁

```jsx
sudo /usr/bin/chflags -R noschg /Applications/FortiClient.app
```