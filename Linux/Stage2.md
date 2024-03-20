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

## Với tham số -mtime

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

## Mount/Umount Partition

```mount [options] <source> <directory>```

Mount một ổ cứng nvme0n1p3 vào trong /media/Test
![Mount](/Images/mount.png)

```umount [-hV]```

Umount ổ cứng nvme0n1p3 trong /media/Test

![Mount](/Images/umount.png)



