### Nginx 下载

| 操作系统 | CentOS 7.7            |
| -------- | --------------------- |
| 权限     | sudo                  |
| 安装目录 | /etc                  |
| 下载工具 | yum                   |
| 配置目录 | /etc/nginx/nginx.conf |

### 安装

1. 添加yum源

   ```bash
   sudo rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
   
   #安装好后，可以通过yum repolist查看
   ```

2. 安装

   ```bash
   sudo yum install nginx
   ```

3. 配置Nginx服务

   设置开机启动

   ```bash
   $ sudo systemctl enable nginx
   ```

   启动服务

   ```bash
   $ sudo systemctl start nginx
   ```

   停止服务

   ```bash
   $ sudo systemctl restart nginx
   ```

   重新加载，因为一般重新配置之后，不希望重启服务，这时可以使用重新加载。

   ```bash
   $ sudo systemctl reload nginx
   ```

4. 配置防火墙

   ``` bash
   #查看firewalld状态:
   systemctl status firewalld
   #如果是dead状态，即防火墙未开启
   #开启防火墙
   systemctl start firewalld
   #关闭防火墙
   systemctl stop firewalld
   ```

4.安装成功后

```bash
nginx -v
```

### 命令

| 名称                      | 命令                   |
| ------------------------- | ---------------------- |
| 启动nginx                 | start nginx            |
| 修改配置后重新加载生效    | nginx -s reload        |
| 重新打开日志文件          | nginx -s reopen        |
| 测试nginx配置文件是否正确 | nginx -t -c nginx.conf |
| 关闭nginx ：快速停止nginx | nginx -s stop          |
| 完整有序的停止nginx       | nginx -s quit          |



在aliyun ces centos上安装nginx（阿里云基于centOS的部署）

1. 安装yum  类似npm，下载模块以及模块的依赖
   1. yum -y install pcre*  
   2. yum -y install openssl*
2. 下载weget 类似迅雷 下载文件
   1. yum install wget
3. 下载nginx
   1. 下载到对应目录，一般都在usr/local
   2. wget http://nginx.org/download/nginx-1.17.9.tar.gz
4. 解压
   1. tar -zxvf 文件名
5. 编译安装
   1. 进入解压的文件夹里面
   2. ./configure
   3. make -j4 && make install 会产生一个nginx文件夹，所有操作都在这个里面
6. 进入nginx文件的sbin目录
   1. 测试是否安装成功 
   2. ./nginx 启动， ./nginx -t
   3. 在阿里云实例里设置安全组
      1. http/https
      2. 1/65535
      3. 0.0.0.0/0
7. 项目部署
8. 若改变了conf文件则需要重启nginx