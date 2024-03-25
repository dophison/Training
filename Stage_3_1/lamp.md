
# Table of contents

Cài đặt mô hình lamp.
Thực hiện reset pass mysql, mở remote mysql ở port 3306.


Cài đặt source laravel và wordpress với 2 vhost như sau:

Vhost: laravel.vietnix.vn -> trỏ về source laravel 
Vhost: wordpress.vietnix.vn -> trỏ về source wordpress

Sau đó từ máy remote truy cập vào 2 domain này nếu load đúng giao diện là thành công.
Thực hiện reset pass user admin (hoặc bất kì user nào bạn tạo lúc cài đặt wordpress).

1. [Cài đặt mô hình LAMP](#cai-dat-mo-hinh-lamp)
2. [Config vhost](#config-vhost)

# Cài đặt mô hình LAMP 

> Linux : máy cá nhân (được cài Ubuntu 22.04.4 LTS)
Apache
MySQL
PHP

``apt install lamp-server^``


Các bước cài đặt:

``apt install apache2``

![apache2](/Images/Stage3_1/apache_active.png)


``apt install mysql``

![mysql](/Images/Stage3_1/sql_active.png)


Có thể dùng  ``ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';`` để **reset password** của một user/super user khi đăng nhập vào mysql 

![mysql](/Images/Stage3_1/mysql_root.png)


Mở port 3306

Trong file ``mysqld.cnf`` chỉnh ``bind_address = 0.0.0.0`` hoặc ip của máy remote 

![mysqlbind](/Images/Stage3_1/mysqld.png)


Kiểm tra thông tin lắng nghe bằng ``netstat -plant``

![netstatsql](/Images/Stage3_1/netstat_sql.png)



Ở máy remote dùng

``mysql -u username -p -h remote_host``

Với username phải đảm bảo có quyền truy cập

![sqlremote](/Images/Stage3_1/user_son.png)


![sqlremote](/Images/Stage3_1/remote_access_sql.png)


``sudo apt install php libapache2-mod-php php-mysql``


![php](/Images/Stage3_1/php.png)


Sử dụng apache trỏ đường dân vào một file php bất kì khác với file index.html mặc định.





![php](/Images/Stage3_1/php_test.png)



## Config vhost


> Luôn nhớ cài đặt các dependencies
laravel : ``php libapache2-mod-php php-mbstring php-xmlrpc php-soap php-gd php-xml php-cli php-zip php-bcmath php-tokenizer php-json php-pear``
wordpress: ``apache2 \
                 ghostscript \
                 libapache2-mod-php \
                 mysql-server \
                 php \
                 php-bcmath \
                 php-curl \
                 php-imagick \
                 php-intl \
                 php-json \
                 php-mbstring \
                 php-mysql \
                 php-xml \
                 php-zip ``




Tạo các tệp vhost mới trong ``etc/apache2/sites-available/laravel.conf wordpress.conf``


![vhost](/Images/Stage3_1/create_vhost.png)


Nội dung của file ``laravel.conf`` và ``wordpress.conf``

![vhost](/Images/Stage3_1/laravel_config_content.png)


![vhost](/Images/Stage3_1/wordpress_config_content.png)

> Lưu ý nên cài Composer để thiết lập Laravel dễ dàng hơn 
``curl -sS https://getcomposer.org/installer | php``
Cài đặt Laravel bằng ``composer create-project --prefer-dist laravel/laravel example`` trong ``/var/www/``
Câp quyền ``chmod`` và ``chown`` cho phù hợp đê ``www-data`` có thể truy câp đến.
Trong ``/var/www/example`` chạy ``composer install`` để hoàn tất thiết lập các package cần thiết (xuất hiện thư mục ``vendor``)

Dùng ``a2ensite lavarel.conf`` | ``a2ensite wordpress.conf`` và ``a2enmod rewrite`` để kích hoạt virtual host. Kiểm tra trong thư mục ``/etc/apache2/sites_enablé``

![vhost](/Images/Stage3_1/a2ensite_lararel.png)


Dùng máy remote để truy câp vào server name ``laravel.vietnix.vn`` và ``wordpress.vietnix.vn``


![vhost](/Images/Stage3_1/romote_to_2vhost.png)



> Với status ``200 OK`` trong ``/var/www/apache2/access.log`` cũng như ``/var/www/apache2/access_wordpress.log``

![vhost](/Images/Stage3_1/access_laravel.png)


Hoàn thành thiết lập WordPress

![vhost](/Images/Stage3_1/completee_set_up_wordpress.png)


Thay đổi password ở chỗ này



![vhost](/Images/Stage3_1/change_password.png)



## Stage 3.1

Bên máy VPS 

![VPS](/Images/Stage3_1/nginx_ok_vps.png)






