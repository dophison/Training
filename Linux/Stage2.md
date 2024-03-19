# Tìm kiếm một file, directory

``` find [path] [options] [expression] ``` : dạng lệnh tìm kiếm tổng quát

## Với tên hoặc bằng extension (ví dụ .jpg).

```find ~ -name "son.txt" ``` (tìm ở home directory với tham số -name)
```sh
find /home/dophison/Pictures/ -name *.jpg
```
[![FindCommand]()](/Images/findname_ext.png)

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


 






