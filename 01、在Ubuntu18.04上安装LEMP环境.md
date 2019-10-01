# 01、在Ubuntu18.04上安装LEMP环境
[参考链接]：[Install a LEMP Stack on Ubuntu 18.04](https://github.com/linode/docs/blob/master/docs/web-servers/lemp/how-to-install-a-lemp-server-on-ubuntu-18-04/index.md)
- LEMP(Linux,Nginx,MariaDB,PHP)
## 添加新用户设置主机别名
```
  useradd xxx
  passwd xxx
  usermod -s /bin/bash xxx
  usermod -d /home/xxx xxx
  cat /etc/passwd
  visudo  // 打开/etc/sudoers文件
  csdn ALL=（ALL：ALL） ALL // Ctro+O->Enter->Ctro+X 添加管理员权限
  userdel xxx
```
```
  adduser xxx // 根据系统提示自动配置
  deluser xxx
```
```
  sudo apt update && sudo apt upgrade
  hostnamectl --static set-hostname  xxxx  // 静态(永久)
  hostnamectl -- Tansient  set-hostname xxxx  // 瞬时(计算机生成)
  hostnamectl --  Pretty set-hostname xxxx  // 灵活(随时可能修改)
```
## 软件安装
### NGINX
  `sudo apt install nginx`
### MariaDB
```
  sudo apt install mariadb-server php-mysql
  sudo mysql -u root
  MariaDB [(none)]> SELECT user,host,authentication_string,plugin FROM mysql.user;
  CREATE DATABASE testdb;
  CREATE USER 'testuser' IDENTIFIED BY 'password';
  GRANT ALL PRIVILEGES ON testdb.* TO 'testuser';
  quit
```
### PHP
```
  sudo apt install php-fpm
  sudo sed -i 's/;cgi.fix_pathinfo=1/cgi.fix_pathinfo=0/g' /etc/php/7.2/fpm/php.ini
```
### 配置Nginx
```
  sudo mkdir -p /var/www/html/example.com/public_html
  sudo cp /etc/nginx/sites-enabled/default /etc/nginx/sites-enabled/default.bak
  sudo rm -f /etc/nginx/sites-enabled/default
  sudo vim /etc/nginx/sites-available/example.com.conf 
  sudo ln -s /etc/nginx/sites-available/example.com.conf /etc/nginx/sites-enabled/
```
```
server {
    listen         80 default_server;
    listen         [::]:80 default_server;
    server_name    example.com www.example.com;
    root           /var/www/html/example.com/public_html;
    index          index.html;

    location / {
      try_files $uri $uri/ =404;
    }

    location ~* \.php$ {
      fastcgi_pass unix:/run/php/php7.2-fpm.sock;
      include         fastcgi_params;
      fastcgi_param   SCRIPT_FILENAME    $document_root$fastcgi_script_name;
      fastcgi_param   SCRIPT_NAME        $fastcgi_script_name;
    }
}
```
`Tip:当不是使用sudo编辑文件的时候，权限不够时使用 :w !sudo tee % 强制保存文件`
### 测试LEMP环境
```
  sudo systemctl restart php7.2-fpm
  sudo nginx -s reload
  sudo nginx -t
  sudo vim /var/www/html/example.com/public_html/test.php 
  Go to http://example.com/test.php
  sudo rm /var/www/html/example.com/public_html/test.php
```
```
<html>
<head>
    <h2>LEMP Stack Test</h2>
</head>
    <body>
    <?php echo '<p>Hello,</p>';

    // Define PHP variables for the MySQL connection.
    $servername = "localhost";
    $username = "testuser";
    $password = "password";

    // Create a MySQL connection.
    $conn = mysqli_connect($servername, $username, $password);

    // Report if the connection fails or is successful.
    if (!$conn) {
        exit('<p>Your connection has failed.<p>' .  mysqli_connect_error());
    }
    echo '<p>You have connected successfully.</p>';
    ?>
</body>
</html>
```















