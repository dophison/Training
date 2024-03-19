# Tìm kiếm một file, directory

``` find [path] [options] [expression] ``` : dạng lệnh tìm kiếm tổng quát

## Bằng tên hoặc bằng extension (ví dụ .jpg).

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

