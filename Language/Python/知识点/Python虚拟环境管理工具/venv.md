# venv

# 安装

make venv

# 创建

python3 -m venv <env_name>

# 激活

source myenv/bin/activate

# 安装

pip install requests xxx

# 退出

deactivate

# 生成依赖文件

pip freeze > requirements.txt

# 从依赖文件安装

pip install -r requirements.txt