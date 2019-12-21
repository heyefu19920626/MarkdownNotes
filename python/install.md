
#### CentOS7安装python3
-  先安装一些依赖
    - yum install zlib-devel libffi-devel sqlite-devel redline-devel -y
        - redline-devel模块解决删除，方向键乱码
-  配置
    - ./configure --prefix=/usr/local/python3Dir
        - 配置安装目录
        - yum install gcc -y
- 编译
    - make
- 安装
    - make install
- 创建软链接
    - 备份 mv /usr/bin/python /usr/bin/python.bak
    - ln -s /usr/local/python3Dir/bin/python3 /usr/bin/python
