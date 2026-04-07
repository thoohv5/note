# 升级node版本（MAC系统）

```sql
# 查看node版本
node -v

# 清除node.js的cache
sudo npm cache clean -f

# npm WARN using --force Recommended protections disabled.
npm cache verify

# 安装n模块
sudo npm install -g n

# 查看node所有版本
npm view node versions

# 升级到最新版本
sudo n latest

# 升级到稳定版本
sudo n stable

# 升级到具体版本
sudo n xx.xx
```