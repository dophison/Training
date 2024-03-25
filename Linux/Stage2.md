
# Table of content

1. [Tìm kiếm một file, directory](#tìm-kiếm-một-file-directory)

    [Với tên hoặc bằng extension (ví dụ .jpg)](#với-tên-hoặc-bằng-extension-ví-dụ-jpg)
    
    [Với giá trị -maxdepth, -mindepth](#với-giá-trị--maxdepth--mindepth)
    
    [Với giá trị -mtime](#với-giá-trị--mtime)
2. [Disk and Partition](#disk-and-partition)
    
    [Xem dung lượng disk](#xem-dung-lượng-disk)
        
    [lsblk](#lsblk)
        
    [df (disk filesystem) & du (disk usage)](#df-disk-filesystem--du-disk-usage)
    
    [CPU](#cpu)

    [Stress-testing the system’s CPU by stress tool](#stress-testing-the-systems-cpu-by-stress-tool)

3. [RAM](#ram)

4. [Process Manager](#process-manager)

5. [Permission](#permission)

6. [Editor](#editor)

7. [Symbolic Links, Hard Links](#symbolic-links-hard-links)

8. [Compression](#compression)

9. [NetworkTool](#network-tool)


   [Telnet](#telnet)

   [ping](#ping)

   [hping3](#hping3)

   [SSH](#ssh)

   [scp](#scp)laravel-laravel-087543a/


   [rsync](#rsync)

10. [Working with file](#working-with-file)
11. [Traceroute](#traceroute)
12. [Netstat](#netstat)
13. [Print](#print)


14. [Working with contents](#working-with-contents)

    [sort](#sort)

    [uniq](#uniq)

    [wc](#wc)

    [cut](#cut)

    [join](#join)

15. [Tìm kiểu các khái niệm cơ bản](#tìm-kiểu-các-khái-niệm-cơ-bản)

    [Standard Input (stdin)](#standard-input-stdin)

    [Standard Output (stdout)](#standard-output-stdout)

    [Standard Error (stderr)](#standard-error-stderr)    
    
    
# Tìm kiếm một file, directory

``` find [path] [options] [expression] ``` : dạng lệnh tìm kiếm tổng quát

## Với tên hoặc bằng extension (ví dụ .jpg).

```find ~ -name "son.txt" ``` (tìm ở home directory với tham số -name)
```sh
find /home/dophison/Pictures/ -name *.jpg
```
![FindCommand](/Images/findname_ext.png)

## Với giá trị -maxdepth, -mindepth
(mục đích không muốn tìm quá sâu/tất cả bên trong thư mục)

```find ~ -maxdepth levels``` (với ý nghĩa là số lượng cấp tối đa (levels) tìm trong các thư mục con) 

```shtrị
find /home/dophison/Pictures/ -name *.jpg -maxdepth 1 

find /home/dophison/Pictures/ -name *.jpg -maxdepth 2 
```

✨Hai cấp (1 - hiện hành và 2 - sâu hơn) khác nhau được thể hiện✨

```find ~ -mindepth levels``` (với ý nghĩa là số lượng cấp tối thiểu (levels) tìm trong các thư mục con)

![FindCommand](/Images/findmaxmindepth.png)

## Với giá trị -mtime

```-mtime n``` là dùng để tìm file được chỉnh sửa (modification) trong vòng n ngày.
_(1 file có 3 yếu tố thời gian access time, modification time, change time)_
- +n: more than n.
-  n: Exactly n.
- -n: Less than n.

(tương tự với mmin: trong vòng n phút)

![FindCommand](/Images/findmmin.png)

# Disk and Partition

## Xem dung lượng disk

### lsblk

```lsblk [options] [<device> ...]```
Hiển thị thông tin lưu trữ

![Disk](/Images/lsblk.png)

Thêm các tham số vào sẽ có thêm các thuộc tính  bổ sung

![Disk](/Images/lsblktype.png)

### df (disk filesystem) & du (disk usage)

Dùng để lấy toàn bộ thông tin về lượng ổ cứng khả dụng và lượng ổ cứng đã dùng 

```df [OPTION]... [FILE]...```

Để hiển thị thông tin của tất cả dung lượng dùng
```df -a```
![Disk](/Images/df_a.png)

Tuy nhiên đê có thể dễ dàng đọc được các thông tin với định dạng  theo MB, GB 
```df -h```
![Disk](/Images/df_h.png)

Trong đó:
- filesystem: tên filesystem có thể trùng với phân vùng đĩa.
- 1K-blocks: Số lượng khối (block) có trong filesystem có kích thước 1Kb.
- Used: Số lượng 1K-block được sử dụng trong filesystem.
- Available: Số lượng 1K-block đang có sẵn.
- Use%: Phần trăm đĩa đã sử dụng trong filesystem.
- Mounted on: Nơi mount.

Chi tiết hơn thì thêm vào sau đó đĩa/phân vùng cụ thể
```df <option> path```

![Disk](/Images/df_h_root.png)

Lệnh du giúp kiểm tra dung lượng ổ đĩa được sử dụng bởi các thư mục 
```
du <option> <path|file>
du <option> <path1> <path2> <path3>  
```

![Disk](/Images/du_file_path.png)

Trong đó tham số -shc có nghĩa là tóm tắt các đường dẫn với các đơn vị có thể đọc được và nếu có nhiều đường dẫn thì sẽ tính tổng dung lượng

 > **Phân vùng /** là phân vùng root, chứa các tệp và thư mục quan trọng nhất của hệ thống. Việc cung cấp đủ không gian lưu trữ cho phân vùng / là rất quan trọng để hệ thống có thể hoạt động một cách ổn định và hiệu quả

![Disk](/Images/df_root.png)

> **Phân vùng tmpfs**  dùng để sử dụng để lưu trữ dữ liệu tạm thời trong bộ nhớ RAM thay vì trên đĩa cứng.

![Disk](/Images/df_tmpfs.png)

> **Inode** là một cấu trúc dữ liệu. Nó xác định một file hoặc một thư mục trên hệ thống file và được lưu trữ trong directory entry nodes trỏ đến các block tạo nên một file. **Khi sử dụng hết các inodes, ngay cả khi có đủ dung lượng trống trên đĩa, bạn sẽ không thể tạo file mới.**

![Disk](/Images/df_ih.png)

> **Phân vùng LVM** cho phép chia không gian đĩa cứng thành những Logical Volume từ đó giúp cho việc thay đổi kích thước trở nên dễ dàng.
(```pvs```             Display information about physical volumes;
```lvs```             Display information about logical volumes;
```vgs```             Display information about volume groups)


## Mount/Umount Partition

```mount [options] <source> <directory>```

Mount một ổ cứng nvme0n1p3 vào trong /media/Test
![Mount](/Images/mount.png)

```umount [-hV]```

Umount ổ cứng nvme0n1p3 trong /media/Test

![Mount](/Images/umount.png)


## CPU

Một số các lệnh như :

```lscpu```

![CPU](/Images/lscpu.png)

Chi tiết thông số trong  ```/proc/cpuinfo```

Và theo thời gian thực như:

```top```

![CPU](/Images/top_cpu.png)

```mpstat```

![CPU](/Images/mpstat_2s.png)


- load average : thời gian tải trung bình (the last 1 minute, the last 5 minutes, and the last 15 minutes)
- zombie process : là những tiến trình tuy đã chấm dứt nhưng chưa xóa hoàn toàn trong bảng tiến trinh, đợi tiến trình cha cập nhật thông tin trạng thái.

>```ps aux | egrep "Z|defunct"``` : tìm các tiến trình khả nghi khi dùng ```top``` thấy có tiến trình zombie xuất hiện.
Tiêu diệt bằng cách:

     - Tìm ra tiến trình cha của tiến trình zombie :  ```ps -o ppid= -p  <pid>```
     - XáC định tiến trình cha có tồn tại hay không: ```ps -e | grep <ppid>```
     - ```kill -SIGKILL <ppid>```
     
- sleeping process : là những tiến trình đang ở trạng thái đợi từ một tiến trình khác
- us : Thời gian CPU sử dụng cho các tiến trình người dùng. (User space)
- sy : Thời gian CPU sử dụng cho các tác vụ hệ thống. (Kernel space)
> Vì Linux là monolitic kernel nên sẽ đặc trưng bởi hai không gian user và kernel

- ni : Thời gian CPU sử dụng cho các tiến trình được ưu tiên. (do người dùng xác định) 
- id : Thời gian CPU nhàn rỗi.
- wa : Thời gian CPU chờ đợi để xử lý các yêu cầu I/O.
- hi : Thời gian CPU sử dụng cho xử lý các ngắt phần cứng.
- si : Thời gian CPU sử dụng cho xử lý các ngắt phần mềm.
- st : Thời gian CPU liên quan đến phần ảo hóa.

### Stress-testing the system’s CPU by _stress tool_

Sau khi cài đặt: ```sudo apt install stress```

```sudo stress option argument```

Với câu lệnh ```stress --cpu 2 --timeout 120```,  2 tiến trình được tạo ra để tính toán liên tục hàm sqrt() của số bất kì.

![Stress](/Images/stress_evaluate.png)



# RAM

Chi tiết thông số trong  ```/proc/meminfo```

![Ram](/Images/meminfor.png)

Trực quan và dễ hiểu thì dùng:
 
```free```

![Ram](/Images/free.png)

Trong đó: 

- ram used : Đã sử dụng.
- free :  Còn trống.
- shared : Dung lượng dùng chung.
- buff/cache : Dung lượng bộ nhớ sử dụng cho việc lưu đệm.
- available : Dung lượng bộ nhớ khả dụng.

> Có thể xác định đươc ram còn trống trong cột ```free``` hoặc ```available```. Ngoài ra, tổng số bộ nhớ có thể sử dụng sẽ bằng free + buff/cache.

# Process Manager

Kich bản sử dụng là sẽ có một tiến trình stress chạy và dùng ```ps ``` để kiểm tra sau đó sẽ xác định pid và ```kill``` tiến trình đó.

![Process](/Images/ps_e.png)

![Process](/Images/kill.png)

## Working with dicrection and folder 

Liệt kê danh sách file/thư mục, cách show các file ẩn trong thư mục

```ls [OPTION]... [FILE]...```

Dùng ```ls -a```

![WorkDF](/Images/ls_a.png)

Tìm kiếm, copy, di chuyển,... file/thư mục

``find``` [ở trên](#tìm-kiếm-một-file-directory)

```cp [OPTION]... SOURCE... DIRECTORY```

![WorkDF](/Images/cp.png)

```mv [OPTION]... SOURCE... DIRECTORY```

![WorkDF](/Images/mv.png)


# Permission

```chmod```: dùng đê cấp quyền cho file, thư mục, bao gồm các quyền như read, write, execute 
```chmod [OPTION]... OCTAL-MODE FILE...```

![Permit](/Images/chmod.png)

```chown```: dùng thay đổi sở hữu file, thư mục .
```chown [OPTION]... [OWNER][:[GROUP]] FILE...```

![Permit](/Images/chown.png)

```chattr``` (Change Attribute) cho phép thay đổi thuộc tính của file giúp bảo vệ file khỏi bị xóa hoặc ghi đè nội dung, dù cho có đang là user root.
```chattr [operator] [flags] [filename]```
[_operator_] gồm:
- +: Thêm thuộc tính cho file.
- -: Gỡ bỏ thuộc tính khỏi file.
- =: Giữ nguyên thuộc tính của file.

[_flags_] gồm:
- ```i```: Flag này khiến file không thể rename, không thể tạo symlink, không thể thực thi, không thể write. Chỉ có user root mới set và unset được thuộc tính này.
- ```a```: Tương tự như flag ```i``` nhưng có quyèn write.
- ```d```: file có thuộc tính này sẽ không được backup khi tiến trình dump chạy.
- ```S```: Nếu một file có thuộc tính này bị sửa, thay đổi sẽ được cập nhật đồng bộ trên ổ cứng.
- ```A```: Khi file có thuộc tính được truy cập, giá trị atime của file sẽ không thay đổi.

Kịch bản thực hiện như sau: kiểm tra thuộc tính của các file bằng ```lsatrr``` sau đó đặt thuộc tính ```i``` cho file và thử xóa file đã đặt thuộc tính.

![Permit](/Images/chattr.png)

# Editor 

- Vi/Vim: ```i```: insert, ```del```: delete, ```(ESC) + :wq```: save, ```(ESC) + :q!```: exit
- nano: nhập xóa dữ liệu trực tiếp,  "Ctr+ X+Yes/No" : save /don't save and exit

# Symbolic Links, Hard Links

## Hard Links
```ln [file nguồn] [file đích]```
Là các liên kết cấp thấp ( low-level links), tạo một liên kết trong cùng hệ thống tập tin với 2 inode entry tương ứng trỏ đến cùng một nội dung vật lý.  

Có thể thấy 2 file ```son.txt``` và ```son_backup.txt``` có cùng inode và nội dung.

![Hardlink](/Images/hard_link.png)

## Symbolic Links
```ln -s [file nguồn] [file đích]``` (có thể dùng cho thư mục)
Tương tự như một shortcut trong Windows, không dùng đến inode entry. Sẽ tạo ra một inode mới và nội dung của inode này trỏ đến tên tập tin gốc.

![Symbolic](/Images/ln_s.png)

# Compression

```tar``` lệnh phổ biến nhất để nén và giải nén file và thư mục. Nén file bằng ```tar``` sẽ cho kết quả là file ```.tar```.

Lệnh nén:
```tar -cvf [tên file sau nén] [thư mục/file cần nén]```
```tar -cvzf``` : dùng để tạo file ```tar.gz``` 
```zip [tên file sau nén] [thư mục/file cần nén]````

Lệnh giải nén:
```tar -xvf ```
```unzip [tên file nén] -d destination_folder ``

![Compression](/Images/tar.png)

![Compression](/Images/zip.png)


# Network Tool

## Telnet
Giao tiếp với máy chủ khác bằng giao thức Telnet.
``telnet [host] [port]``

![ToolNetwork](/Images/telnet.png)

## ping

```ping [options] <destination>```

Dưới hình sư dụng [option] là ``-c`` dùng để dừng gửi sau số lần chỉ định.
>ping is running an ICMP echo request 

![ToolNetwork](/Images/ping.png)

## hping3

```hping3 host [options]```

>hping3 is running a "ping" using the TCP protocol on port 80

Hình ảnh mô tả thực hiện scan port từ 1 tới 1000, port 8888 và tất cả các port liệt kê trong /etc/services.

![ToolNetwork](/Images/hping3_scan.png)


## SSH
ssh với key, ssh với port custom, gen key ssh.

Kịch bản là một máy X sẽ tạo ra pubkey như hình bằng ```ssh-keygen```.

![ToolNetwork](/Images/sshkeygen.png)

Server sẽ thêm public key vào trong ``authorized_keys``.

![ToolNetwork](/Images/author.png)

Máy X thông qua ssh có thể truy cập vào server.

![ToolNetwork](/Images/ssh_pubkey.png)

Tiếp tục thử với kịch bản là thay đổi port của ssh thành **123** và truy cập:

Thay đổi trong file ``/etc/ssh/sshd_config``

![ToolNetwork](/Images/change_port_ssh.png)

Kiểm tra bằng lệnh ``systemctl status ssh``

![ToolNetwork](/Images/status_after_change_port.png)

Truy cập ``ssh username@IP -p 123``

![ToolNetwork](/Images/ssh_newport.png)

## scp 
SCP (secure copy) là một tiện ích dòng lệnh cho phép sao chép an toàn các tệp và thư mục (From your local system to a remote system)

```scp [OPTION] [user@]SRC_HOST:]file1 [user@]DEST_HOST:]file2```

Thực hiện kịch bản từ một máy gửi tệp và thư mục  (thêm tham số ```-r ``` vào) đến máy khác 

![ToolNetwork](/Images/scp.png)

## rsync 
file, folder, rsync incremental.
Dùng để sao lưu và đồng bộ dữ liệu. giữa các máy 
```rsync options source destination```
Thực hiện việc đồng bộ từ máy local lên máy VPS thông qua  ```rsync -avzhe ssh Linux/ root@vpsson:/home/rsyn_from_local/```

![ToolNetwork](/Images/rsync_ssh.png)


# Working with file

### Xem nhanh nội dung của file.

```cat <tên file>```
![Workwithfile](/Images/cat.png)

### Chèn một đoạn text vào cuối file.

``echo "nội dung mới" >> <file cần chèn>``

![Workwithfile](/Images/insertfile.png)

### Show 2 dòng đầu file.
``head [OPTION]... [FILE]...``

![Workwithfile](/Images/head.png)

### Show 2 dòng cuối file.

``tail [OPTION]... [FILE]...``

![Workwithfile](/Images/tail.png)

### Dùng sed command để find and replace một trong string trong file text.

``sed OPTIONS... [SCRIPT] [INPUTFILE...] ``

![Workwithfile](/Images/sed.png)

# Traceroute
Dùng traceroute để trace từ máy remote đến ip 103.200.22.11 và nêu ra quá trình đó cần đi qua các hop nào.
Dựa vào kết quả  như hình , có thể thấy được 
- Hop 1: static.vnpt.vn (14.225.198.254) qua gateway/router VNPT 
- Hop 2: Đích đến cuôi cùng.

![Traceroute](/Images/traceroute.png)


# Netstat (network statistic)

Hiển thị thông tin liên quan đến mạng và chẩn đoán sự cố.

``netstat [options]``

Các tham số thường dùng:
- a : hiển thị tất cả các cổng đang lắng nghe và không.
- t : hiển thị các kết nối liên quan đến TCP.
- u : hiển thị các kết nối liên quan đến UDP.
- l : chỉ hiện các cổng đang nghe.

![Netstat](/Images/netstat.png)

# Print

Dùng để in văn bản (sử dụng ``lpr``)

``lpr <file name>``

``lpr -P <printer name> <file name>``  

# Working with contents

## sort

``sort [OPTION]... [FILE]...``

Mặc định sẽ là sắp xếp tăng dần/abc vói  option ``-r `` thì sẽ cho kết quả ngược lại.

![sort](/Images/sort.png)

## uniq

Thường dùng để xử lý danh sách, mặc định là dùng đê loại bỏ các dòng trùng lặp liền kề trong một file.

``uniq [OPTIONS] [INPUT_FILE [OUTPUT_FILE]]``

Với [OPTIONS] có thể là 
- ``c`` : đếm số dòng trùng lặp.
- ``d`` : chỉ in ra dòng có sư trùng lặp.
- ``D`` : in ra tất cả sự lặp lại.
- ``u`` : in ra các dòng duy nhất.

![uniq](/Images/uniq.png)

## wc (word count)

Dùng cho mục đich đếm.

``wc [OPTION]... [FILE]...``

Kết quả ```9 9 27``` là: 9 dòng; 9 từ, 27 kí tự

![wc](/Images/wc.png)

## cut

Dùng đê lấy ra dũ liệu từ một file.

``cut OPTION... [FILE]...``

Với [OPTIONS] có thể là 
- ``b`` : Cát theo vị trí byte.
- ``C`` : Cát theo vị trí kí tự.
- ``f`` : trích xuất theo một trường cụ thể.
- 
![cut](/Images/cut.png)


## join

Nối giữa hai tẹp với nhau  trong một môi truòng chung.

``join [OPTION] FILE1 FILE2``

# Tìm kiểu các khái niệm cơ bản

## Standard Input (stdin):

Là nơi nhận dữ liệu từ người dùng (bàn phím). 

## Standard Output (stdout):

Là nơi chương trình hiển thị kết quả ra màn hình. 

## Standard Error (stderr):

Là nơi chương trình thông báo lỗi.

## Redirect error to a file

![Redirect](/Images/redirect_error.png)

## Redirect output messages to a file

![Redirect](/Images/redirect_output.png)

## Redirect error and output messages to a file

![Redirect](/Images/redirectboth.png)






