# Ubuntu Install Mysql

1. 更新软件包

   ```shell
   sudo apt install update
   ```

2. 安装mysql服务器

   ```shell
   sudo apt install mysql-server
   ```

3. 启动服务

   ```shell
   sudo systemctl start mysql
   ```

4. 开机自启动

   ```shell
   sudo systemctl enable mysql
   ```

5. 修改密码

   ```shell
   # 登录, 因为没有设置密码，管理员下直接以root用户登录
   sudo mysql
   
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
   flush privileges;
   # 
   
   ```

6. 创建新用户

   ```sql
    // % 代表所有地址都可通过此用户登录
   CREATE USER 'lpf'@'%' IDENTIFIED BY '123';
   
   ```
7. 够侦听远程可访问的接口 

	``` shell
	sudo nvim /etc/mysql/mysql.conf.d/mysqld.cnf
	
	```
	`bind-address = 0.0.0.0`

	
   

