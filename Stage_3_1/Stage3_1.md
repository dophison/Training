
# Table of contents


1. [Cài đặt mô hình LAMP](#cai-dat-mo-hinh-lamp)
2. [Config vhost](#config-vhost)
3. [HAProxy](#cau-hinh-haproxy-thay-cho-Nginx)
3. [Iptables](#iptabless)




# Cài đặt mô hình LAMP 

> Linux : máy cá nhân (được cài Ubuntu 22.04.4 LTS)

> Apache

> MySQL

> PhP
  
 

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

>wordpress: ``apache2 \
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

>Cài đặt Laravel bằng ``composer create-project --prefer-dist laravel/laravel example`` trong ``/var/www/``

>Câp quyền ``chmod`` và ``chown`` cho phù hợp đê ``www-data`` có thể truy câp đến.

>Trong ``/var/www/example`` chạy ``composer install`` để hoàn tất thiết lập các package cần thiết (xuất hiện thư mục ``vendor``)

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

Đồng thời cài đặt Nginx trên VPS

![VPS](/Images/Stage3_1/nginx_status.png)


> Nginx sẽ chạy ở  port 80, apache sẽ chạy ở port 8080 với 2 dịch vụ là larevel và wordpress như trên


Cấu hình file ``default`` trong ``/etc/nginx/sites-available`` 



![VPS](/Images/Stage3_1/config_rproxy.png)


Sau đó test thử bằng cách tắt Apache truy cập website báo lỗi ``502 bad gateway`` là đươc.

Tạo một bộ cert ssl (pem + private key) bằng openssl


![SSL](/Images/Stage3_1/create_ssl.png)


![SSL](/Images/Stage3_1/certificate.png)

Thêm vào file virutal host của lavarel

![SSL](/Images/Stage3_1/ssl_content.png)

![SSL](/Images/Stage3_1/https.png)

Tương tự với nginx với cấu hình như sau 

![SSL](/Images/Stage3_1/nginx_ssl.png)

### Cấu hình haproxy thay cho Nginx


> HAProxy (High Availability Proxy) là một công cụ mã nguồn mở ứng dụng cho giải pháp cần bằng tải TCP và HTTP.

Cài đặt và kiểm tra trạng thái và log của HAProxy.

Log: ``haproxy -f /etc/haproxy/haproxy.cfg -db``

![HAProxy](/Images/Stage3_1/status_haproxy.png)

Cấu hình trong ``/etc/haproxy/haproxy.cfg``

![HAProxy](/Images/Stage3_1/stat_haproxy.png)


![HAProxy](/Images/Stage3_1/haproxy_statistic.png)


Vì một vài xung đột nên thay đôi haproxy lắng nghe trên port 8081 và 4433.


Đây là cấu hình thêm vào trong file haproxy.cfg

![HAProxy](/Images/Stage3_1/config_haproxy.png)

> 14.225.217.222:80 đang có dịch vụ wordpress

14.225.217.222:443 đang chạy dịch vụ laravel


![HAProxy](/Images/Stage3_1/result_haproxy.png)



### Iptables




_Dùng iptables drop port 80 của vps lab._

``iptables -A INPUT -p tcp --dport 80 -j DROP``

Trong đó: 
- -A: CHAIN (INPUT; OUTPUT, FORWSARD, PREPROUTING, POSTROUTING)
- -p: xác định giao thức (tcp, udp, icmp,..) 
- ---dport: Định nghĩa cổng đích (80: http)
- -j: Xác định hành động sẽ làm (DROP, ACCEPT,..)

>Mặc dù server vps vẫn đang host dịch vụ wordpress ở cổng 80 nhưng sau khi áp dụng rule iptables thì không truy cập được vào từ máy remote nữa.

![iptables](/Images/Stage3_1/drop_80.png)



_Dùng iptables drop port 80 của vps lab nhưng theo src ip._

Chỉ cần thêm tham số  ``-s`` cùng với địa chỉ IP.


``iptables -A INPUT -p tcp --dport 80 -s 115.78.5.187 -j DROP``


![iptables](/Images/Stage3_1/drop_from_src.png)

> Với rule này ở máy remote bật VPN thì mới truy cập vào được.
![iptables](/Images/Stage3_1/access_vpn.png)

_Dùng iptables drop port 80 của vps lab nhưng theo dest ip._

Chỉ cần thêm tham số  ``-d`` cùng với địa chỉ IP.

``iptables -A INPUT -p tcp --dport 80 -d <IP> -j DROP``




_Dùng iptables nat traffic vào port 80 của ip vps lab sẽ được foward sang ip 192.168.0.83_

> Thêm vào forward sang ip 192.168.0.83 với port 3306 (mysql)

``iptables -t nat -A PREROUTING -p tcp --dport 80 -d 14.225.217.222 -j DNAT --to-destination 192.168.0.83:3306``


![iptables](/Images/Stage3_1/add_rule_prerouting.png)

Sau đó dùng máy remote truy cập vào trong 14.225.217.222:80. Và kiểm tra log của mysql thì thấy có ghi lại lần truy cập => rule đã chuyển tiếp.

![iptables](/Images/Stage3_1/logmysql.png)
