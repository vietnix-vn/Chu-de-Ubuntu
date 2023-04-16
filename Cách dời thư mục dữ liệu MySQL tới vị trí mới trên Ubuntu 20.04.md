# Cách dời thư mục dữ liệu MySQL tới vị trí mới trên Ubuntu 20.04
Trong quá trình sử dụng MySQL, bạn có thể gặp tình trạng khối lượng dữ liệu trong database gia tăng và vượt quá dung lượng hệ thống tệp cho phép. Hay vấn đề xảy ra tranh chấp I/O khi dữ liệu nằm trên cùng một phân vùng với những phần còn lại của hệ điều hành. Để khắc phục những tình trạng trên, trong bài viết này Vietnix sẽ hướng dẫn bạn cách dời thư mục dữ liệu MySQL tới vị trí mới trên Ubuntu 20.04. 

## Mục lục

[Điều kiện để thực hiện việc dời thư mục dữ liệu MySQL tới vị trí mới trên Ubuntu 20.04](#1)

[Bước 1 – Di chuyển thư mục dữ liệu MySQL](#2)

[Bước 2 – Chỉ vào vị trí nơi lưu dữ liệu mới](#3)

[Bước 3 – Cấu hình quy tắc kiểm soát truy cập AppArmor](#4)

[Bước 4 – Khởi động lại MySQL](#5)

[Lời kết](#6)

-----------
<a name="1"></a>
## Điều kiện để thực hiện việc dời thư mục dữ liệu MySQL tới vị trí mới trên Ubuntu 20.04

Để hoàn thành được các bước trong bài viết này, bạn cần có:

- Máy chủ Ubuntu 20.04 với tài khoản non-root user nhưng có quyền `sudo` và firewall đã được bật. 

- Một máy chủ MySQL. Nếu bạn chưa cài đặt MySQL thì có thể tham khảo bài viết Cách cài đặt MySQL trên Ubuntu 20.04.

Trong bài viết này, Vietnix sẽ hướng dẫn bạn chuyển dữ liệu sang thiết bị block storage có địa chỉ `/mnt/volume-nyc1-01`. Dù đang sử dụng bộ nhớ nào thì sau khi đọc bài viết bạn cũng có thể di chuyển thư mục dữ liệu đến một vị trí mới.

Hiện tại Vietnix đang cung cấp dịch vụ VPS tốc độ cao với toàn quyền quản trị giúp người dùng có thể dễ dàng cài đặt hệ điều hành Ubuntu hay MySQL mà không bị hạn chế bất cứ tính năng nào. Với đa dạng cấu hình và mức giá, bạn có thể dễ dàng chọn lựa gói dịch vụ tối ưu nhất theo nhu cầu sử dụng. Liên hệ với đội ngũ Vietnix để được tư vấn chi tiết nhất. 

<a name="2"></a>
## Bước 1 - Di chuyển thư mục dữ liệu MySQL

Trước hết, bạn cần xác minh vị trí hiện tại của thư mục dữ liệu bằng cách chạy lệnh sau để nhắc MySQL server sẵn sàng hoạt động:

    sudo mysql 

**Lưu ý:** Nếu đã cấu hình root MySQL với cách xác thực bằng mật khẩu, bạn có thể kết nối với MySQL bằng lệnh sau:

    mysql -u root -p 

Khi được nhắc, bạn hãy cung cấp mật khẩu người dùng MySQL. Sau đó, từ prompt MySQL chạy câu lệnh `SELECT` để trả về thư mục dữ liệu đang hoạt động trong phiên bản MySQL này. 

    SELECT @@datadir;

Bạn có thể nhận được output như hình với thư mục được ghi trong biến `datadir` của MySQL:

```
Output

+-----------------+

| @@datadir       |

+-----------------+

| /var/lib/mysql/ |

+-----------------+

1 row in set (0.00 sec)
```

Output này đã xác nhận rằng MySQL được cấu hình sử dụng thư mục dữ liệu mặc định `/var/lib/mysql/`. Vì vậy, đây là thư mục bạn cần di chuyển. Khi đã xác nhận được như vậy, hãy gõ exit để rời khỏi màn hình và quay lại dấu nhắc của bạn.

    exit

```
Output

Bye
```

Để đảm bảo tính toàn vẹn của dữ liệu, bạn cần tắt MySQL trước khi thay đổi thư mục với lệnh:

    sudo systemctl stop mysql

**Lưu ý rằng:** `systemctl` không hiển thị kết quả của tất cả các lệnh. Vì vậy, nếu bạn muốn kiểm tra xem mình đã thực thi thành công hay chưa, hãy sử dụng lệnh sau:

    sudo systemctl status mysql

Bạn có thể xác nhận MySQL đã tắt khi Active ở output xuất hiện inactive (dead)như dưới đây:

```
Output

● mysql.service - MySQL Community Server

     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset:>

     Active: inactive (dead) since Wed 2022-03-23 19:03:49 UTC; 5s ago

    Process: 3415 ExecStart=/usr/sbin/mysqld (code=exited, status=0/SUCCESS)

   Main PID: 3415 (code=exited, status=0/SUCCESS)

     Status: "Server shutdown complete"
```

Khi máy chủ đã tắt, bạn có thể sao chép thư mục database `/var/lib/mysql` sang vị trí `/mnt/volume-nyc1-01` với `rsync`. Sử dụng cờ `-a` để duy trì quyền và các thuộc tính thư mục khác. Đồng thời `-v` cung cấp output chi tiết giúp bạn dễ dàng theo dõi tiến trình:

**Lưu ý:** Bạn phải đảm bảo rằng không có dấu gạch chéo nào ở cuối thư mục để tránh tình trạng rsync đổ nội dung của thư mục vào mount point thay vì chuyển nó vào thư mục mới.

    sudo rsync -av /var/lib/mysql /mnt/volume-nyc1-01

Khi lệnh rsync hoàn tất, hãy đổi tên phần mở rộng của thư mục hiện tại thành .bak và để như vậy cho đến khi bạn xác nhận việc di chuyển đã thành công. Bằng cách đổi tên, bạn sẽ tránh được sự nhầm lẫn có thể phát sinh từ các tệp ở cả vị trí mới và cũ:

`sudo mv /var/lib/mysql /var/lib/mysql.bak

<a name="3"></a>
## Bước 2 - Chỉ vào vị trí nơi lưu dữ liệu mới

Có nhiều cách để ghi đè lên các giá trị đã được cấu hình trong MySQL. Theo mặc định, giá trị `datadir` sẽ được đặt ở `/var/lib/mysql` trong file `/etc/mysql/mysql.conf.d/mysqld.cnf`. Bạn có thể chỉnh sửa tệp này trong trình soạn thảo văn bản để tạo thành thư mục dữ liệu mới. Ví dụ với việc sử dụng `nano`:

    sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

Sau đó, bạn tìm dòng bắt đầu bằng `datadir=`. Bỏ ghi chú dòng bằng cách xóa dấu thăng (#) và thay đổi đường dẫn để chỉ tới vị trí mới. Nội dung tệp được cập nhật sẽ như sau:

```
. . .
datadir=/mnt/volume-nyc1-01/mysql
. . .
```

Khi đã cập nhật thành công, bạn hãy lưu và thoát khỏi tệp. Nếu đang sử dụng `nano`, bạn có thể thực hiện bằng cách nhấn **CTRL + X**, sau đó nhấn **Y** và **ENTER**. 

<a name="4"></a>
## Bước 3 - Cấu hình quy tắc kiểm soát truy cập AppArmor

Trong bước này, bạn cần yêu cầu AppArmor cho phép MySQL ghi dữ liệu vào thư mục mới. Trong đó, AppArmor là một module bảo mật trong Linux cho phép quản trị viên hạn chế quyền hạn thông qua cấu hình chương trình thay vì người dùng. Hãy bắt đầu bằng cách mở và chỉnh sửa file alias trên AppArmor:

    sudo nano /etc/apparmor.d/tunables/alias

Ở dưới cùng của tệp, bạn hãy bỏ ghi chú dòng sau và thêm quy tắc alias:

```
. . .
alias /var/lib/mysql/ -> /mnt/volume-nyc1-01/mysql/,
. . .
```

Sau khi hoàn tất, hãy lưu và thoát khỏi tệp.

Để các thay đổi có hiệu lực, bạn cần khởi động lại AppArmor với lệnh:

    sudo systemctl restart apparmor

**Lưu ý:** Nếu bỏ qua bước cấu hình AppArmor, bạn sẽ nhận được thông báo lỗi sau:

```
Output

Job for mysql.service failed because the control process 

exited with error code. See "systemctl status mysql.service" 

and "journalctl -xe" for details.
```

Sau khi đã cấu hình AppArmor chuẩn, bạn có thể chuyển sang bước tiếp theo.

<a name="5"></a>
## Bước 4 - Khởi động lại MySQL

Bây giờ nếu tiến hành khởi động lại MySQL ngay thì bạn sẽ gặp phải một lỗi khác. Lỗi này xuất phát từ `mysql-systemd-start`, một script hỗ trợ quản lý MySQL thông qua `systemd`. Bạn tiến hành gõ lệnh:

    nano /usr/share/mysql/mysql-systemd-start

Script này kiểm tra sự tồn tại của thư mục `-d` hoặc symbolic link `-L` xem có khớp với đường dẫn thư mục dữ liệu mặc định hay không. Nếu không tìm thấy một trong hai thứ này, script sẽ gây ra lỗi và ngăn MySQL khởi động:

```
. . .
if [ ! -d /var/lib/mysql ] && [ ! -L /var/lib/mysql ]; then
 echo "MySQL data dir not found at /var/lib/mysql. Please create one."
 exit 1
fi

if [ ! -d /var/lib/mysql/mysql ] && [ ! -L /var/lib/mysql/mysql ]; then
 echo "MySQL system database not found. Please run mysql_install_db tool."
 exit 1
fi

. . .
```

Sau khi bạn đã kiểm tra file này, hãy đóng file và đảm bảo không thực hiện thay đổi nào.

Bạn cần thư mục thích hợp hoặc symbolic link để khởi động máy chủ. Do đó, hãy tạo cấu trúc thư mục tối thiểu với lệnh sau:

    sudo mkdir /var/lib/mysql/mysql -p

Bây giờ khởi động MySQL với lệnh:

    sudo systemctl start mysql

Xác nhận MySQL đang chạy bằng cách kiểm tra trạng thái:

    sudo systemctl status mysql

Bạn có thể nhận được output như sau, chú ý vào dòng active:

```
Output

● mysql.service - MySQL Community Server

     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset:>

     Active: active (running) since Wed 2022-03-23 20:51:18 UTC; 4s ago

    Process: 17145 ExecStartPre=/usr/share/mysql/mysql-systemd-start pre (code=>

   Main PID: 17162 (mysqld)

     Status: "Server is operational"

      Tasks: 38 (limit: 1132)

     Memory: 376.7M

     CGroup: /system.slice/mysql.service

             └─17162 /usr/sbin/mysqld
```

Để đảm bảo thư mục dữ liệu mới thực sự có thể sử dụng, hãy khởi động màn hình MySQL. Thay thế username database tương ứng của bạn:

    /mysql -u username -p

Thực hiện truy vấn lại giá trị của thư mục dữ liệu:

```
SELECT @@datadir;

Output

+----------------------------+

| @@datadir                  |

+----------------------------+

| /mnt/volume-nyc1-01/mysql/ |

+----------------------------+

1 row in set (0.01 sec)
```

Sau khi khởi động lại MySQL, cần xác nhận rằng thư mục dữ liệu đang sử dụng vị trí mới cũng như đã có đầy đủ chức năng. Cuối cùng, hãy thoát khỏi database và quay lại command prompt:

    exit

```
Output
Bye
```

Sau khi xác minh được tính toàn vẹn dữ liệu, bạn có thể xóa thư mục backup trước đó bằng lệnh:

    sudo rm -Rf /var/lib/mysql.bak

Sau đó, hãy khởi động lại MySQL lần cuối:

    sudo systemctl restart mysql

Cuối cùng, thực hiện kiểm tra trạng thái xem nó đã hoạt động đúng như mong muốn chưa:

    sudo systemctl status mysql

```
Output

● mysql.service - MySQL Community Server

     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset:>

     Active: active (running) since Wed 2022-03-23 20:53:03 UTC; 4s ago

    Process: 17215 ExecStartPre=/usr/share/mysql/mysql-systemd-start pre (code=>

   Main PID: 17234 (mysqld)

     Status: "Server is operational"

      Tasks: 38 (limit: 1132)

     Memory: 368.9M

     CGroup: /system.slice/mysql.service

             └─17234 /usr/sbin/mysqld
```

Nếu dòng **Active** xuất hiện trạng thái **active(running)** thì có nghĩa là MySQL đang hoạt động.

Ngoài ra, bạn cũng nên tham khảo thêm về MariaDB, một hệ quản trị cơ sở dữ liệu mã nguồn mở được phát triển dựa trên MySQL cũng như cách cài đặt và sử dụng MariaDB trên Ubuntu 20.04. Để có thể tận dụng các tính năng mạnh mẽ của hệ quản trị cơ sở dữ liệu mã nguồn mở này xây dựng và quản lý các ứng dụng dữ liệu hiệu quả trên Ubuntu 20.04.

<a name="6"></a>
## Lời kết

Trên đây là những hướng dẫn đơn giản và chi tiết về cách dời thư mục dữ liệu MySQL tới vị trí mới trên Ubuntu 20.04. Hy vọng bài viết đã giúp bạn hiểu rõ về quy trình và các bước cần thiết để thực hiện việc này một cách thành công. Nếu bạn đang quan tâm đến việc di chuyển thư mục dữ liệu MySQL trên Ubuntu 20.04, hãy thực hiện các bước một cách cẩn thận và chắc chắn làm sao cho phù hợp với nhu cầu và điều kiện cụ thể của bạn. 

Ngoài ra, bạn cũng có thể tìm hiểu thêm cách quản lý các thư mục dữ liệu MySQL ở những bài viết khác của Vietnix để giúp quá trình thực hiện dễ dàng hơn. Nếu bạn cần thêm thông tin hoặc có những thắc mắc cụ thể hãy để lại bình luận bên dưới, Vietnix sẽ hỗ trợ bạn nhanh chóng nhất.

Nguồn: [vietnix.vn](https://vietnix.vn/doi-thu-muc-du-lieu-mysql-toi-vi-tri-moi-tren-ubuntu-20-04/)