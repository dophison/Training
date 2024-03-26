# Table of content
Cài đặt Windows Server 2012 + SQL Server 2012. Sau khi cài đặt xong có thể login vào bằng user sa. Sau đó reset pass user sa, reset pass windows bằng kali linux (dùng chntpw).


1. [Install SQL Server](#install-sql-server)
2. [Using chntpw](#using-chntpw-kali-linux)

# Install SQL Server

Tiến hành cài đặt Window Server 2012 R2 và SQL Server 2012 

Trong quá trình cài đặt sẽ có chọn tạo tài khoản _SQL Server Authentication_

![SQLServer](/Images/WinServer/choose_sa.png)


Sau đó ở _Authentication_ chọn SQL Server Authentication và đăng nhập bằng sa


![SQLServer](/Images/WinServer/sa.jpg)


Thay đổi mật khẩu của user sa trong _Security_ -> _Logins_ -> (right click) sa -> _Properties_

![SQLServer](/Images/WinServer/changepw_sa.jpg)

# Using ```chntpw``` Kali Linux

Trên VMware Workstation, thêm vào 1 CD/DVD với iso của kali 

![chntpw](/Images/WinServer/kali_boot.png)

> Vào boot chỉnh priority boot là kali để vào

Biết được muốn reset passwd của user trong Window thì dựa vào file SAM (../Windows/System32/config/SAM)

Thực hiện mount ổ của Window và đến đường dẫn chứa file SAM

```chntpw -i SAM```

![chntpw](/Images/WinServer/chntpw_1.png)

Lựa chọn các tùy chọn thích hợp 

![chntpw](/Images/WinServer/chntpw_2.png)

Sau đó restart vào lại Window Server, kết quả là tự động đăng nhập vào Administrator không cần mật khẩu

![chntpw](/Images/WinServer/chntpw_result.png)


















