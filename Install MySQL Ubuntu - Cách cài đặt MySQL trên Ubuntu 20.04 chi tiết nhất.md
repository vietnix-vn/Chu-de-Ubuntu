# Install MySQL Ubuntu: Cách cài đặt MySQL trên Ubuntu 20.04 chi tiết nhất

MySQL là một hệ quản trị cơ sở dữ liệu mã nguồn mở được sử dụng vô cùng rộng rãi hiện nay, tuân theo mô hình quan hệ và sử dụng SQL để quản lý dữ liệu. Bài viết này sẽ hướng dẫn chi tiết cách cài đặt MySQL trên Ubuntu 20.04.

## Mục lục

- [Điều kiện](#1)
- [Các bước trong cách cài đặt MySQL trên Ubuntu 20.04](#2)
    - [Bước 1 – Cài đặt MySQL](#2.1)
    - [Bước 2 – Cấu hình MySQL](#2.2)
    - [Bước 3 – Tạo và gán quyền cho user MySQL riêng](#2.3)
    - [Bước 4 – Kiểm tra MySQL](#2.4)
- [Lời kết](#3)
-----------
<a name="1"></a>
## Điều kiện

Để install MySQL Ubuntu, trước hết bạn cần có server Ubuntu 20.04, user non-root có quyền quản trị và Firewall cấu hình bằng UFW.

<a name="2"></a>
## Các bước trong cách cài đặt MySQL trên Ubuntu 20.04

Để cài đặt MySQL trên Ubuntu 20.04 chúng ta cần thực hiện theo 4 bước sau.

<a name="2.1"></a>
### Bước 1 - Cài đặt MySQL

Trên Ubuntu 20.04, ta có thể cài đặt MySQL dễ dàng bằng repo APT:

    sudo apt update

Sau đó cài đặt package `mysql-server`:

    sudo apt install mysql-server

Để đảm bảo MySQL đang chạy, sử dụng lệnh `systemctl start`:

    sudo systemctl start mysql.service

Các lệnh trên sẽ cài đặt và khởi động MySQL nhưng chưa yêu cầu đặt mật khẩu hay thay đổi cấu hình của server. Nếu giữ nguyên cấu hình mặc định thì khả năng bảo mật của MySQL sẽ không được bảo đảm, do đó bạn cần tiếp tục cấu hình thêm cho MySQL ở những bước sau.

Ngoài ra, bạn cũng nên tham khảo thêm cách dời thư mục dữ liệu MySQL đến vị trí mới trên Ubuntu 20.04 cũng là một quy trình quan trọng khi bạn cần di chuyển hoặc thay đổi vị trí lưu trữ dữ liệu của MySQL trên hệ thống Ubuntu của mình.

<a name="2.2"></a>
### Bước 2 - Cấu hình MySQL

Trước tiên, ta nên chạy script bảo mật đi kèm trong MySQL để thay đổi một số thiết lập bảo mật kém.

**Lưu ý:** Một số người dùng có thể gặp lỗi khi chạy script `mysql_secure_installation` vì script này sẽ đặt một mật khẩu cho tài khoản MySQL gốc của bản cài đặt. Tuy nhiên trên Ubuntu thì tài khoản này không được cấu hình để kết nối bằng mật khẩu (theo mặc định). Output sau sẽ được trả về khi bạn nhập và xác nhận mật khẩu:

```
Output
 ... Failed! Error: SET PASSWORD has no significance for user 'root'@'localhost' as the authentication method used doesn't store authentication data in the MySQL server. Please consider using ALTER USER instead if you want to change authentication parameters.

New password:
```

Để tránh gặp lỗi này, trước tiên ta cần thay đổi cách root user của MySQL xác thực.

Mở prompt MySQL:

    sudo mysql

Sau đó chạy lệnh `ALTER USER` dưới đây để đổi phương pháp xác thực của root user thành xác thực bằng mật khẩu:

    ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';

Sau đó thoát khỏi prompt MySQL:

    exit

Bây giờ ta có thể chạy script `mysql_secure_installation` mà không còn gặp lỗi nữa.

Sau khi script chạy xong, ta có thể mở lại MySQL rồi đổi phương thức xác thực của root user về mặc định (`auth_socket`). Để xác thực user MySQL bằng mật khẩu, chạy lệnh dưới đây:

    mysql -u root -p

Rồi chuyển về phương thức xác thực mặc định:

    ALTER USER 'root'@'localhost' IDENTIFIED WITH auth_socket;

Khi đó ta có thể tiếp tục kết nối đến MySQL từ root user bằng lệnh `sudo mysql`.

Quay lại vấn đề chính, sau khi khắc phục lỗi chạy script bảo mật (nếu có), hãy chạy lệnh bằng sudo như sau:

    sudo mysql_secure_installation

Bây giờ ta sẽ được chuyển đến nhiều prompt khác nhau để thực hiện các thay đổi cần thiết cho những tùy chọn bảo mật của MySQL. Prompt đầu tiên yêu cầu thiết lập Validate Password Plugin - dùng để kiểm tra độ mạnh mật khẩu của user MySQL mới trước khi công nhận là hợp lệ.

Nếu đồng ý thiết lập Validate Password Plugin, mọi user MySQL được tạo và chọn xác thực bằng mật khẩu sẽ bắt buộc phải tạo mật khẩu dựa trên các chính sách mà bạn đã chọn. Mức chính sách mạnh nhất là 2 - mật khẩu phải có ít nhất tám ký tự, gồm cả chữ in hoa, chữ thường, số và ký tự đặc biệt.

```
Output
Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: Y

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG:
 2
```

Prompt tiếp theo yêu cầu thiết lập mật khẩu cho root user của MySQL. Nhập rồi xác thực lại mật khẩu:

```
Output
Please set the password for root here.

New password:

Re-enter new password:
```

**Lưu ý** rằng đến bước này dù đã đặt mật khẩu cho user MySQL nhưng bạn vẫn chưa cấu hình cho user hiện tại xác thực bằng mật khẩu khi kết nối đến shell MySQL. Tiếp đến, prompt sẽ đánh giá độ mạnh của mật khẩu (nếu sử dụng Validate Password Plugin trước đó) và hỏi lại xem có chắc chắn muốn sử dụng mật khẩu này không. Nhập Y để tiếp tục:

```
Output
Estimated strength of the password: 100
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : Y
```

Bây giờ, nhập **Y** rồi nhấn **Enter** để đồng ý với các thiết lập mặc định cho những câu hỏi tiếp theo. Thao tác này sẽ xóa một số user ẩn danh và cơ sở dữ liệu test, disable các đăng nhập vào root từ xa, và cuối cùng là tự áp dụng các thay đổi này.

Sau khi script hoàn tất, bản cài đặt MySQL của bạn đã được đảm bảo bảo mật. Bây giờ bạn có thể bắt đầu tạo một user chuyên dụng cho cơ sở dữ liệu bằng MySQL client.

<a name="2.3"></a>
### Bước 3 - Tạo và gán quyền cho user MySQL riêng 

Sau khi install MySQL Ubuntu, hệ thống sẽ tạo một root user để quản lý cơ sở dữ liệu. User này có toàn quyền trên server MySQL, có thể quản lý mọi database, bảng, user,... Vì vậy bạn nên tránh sử dụng tài khoản này nếu không cần đến các chức năng quản trị. Ở bước này sẽ hướng dẫn tạo tài khoản root user mới và gán cho nó các đặc quyền phù hợp.

Trong Ubuntu chạy MySQL 5.7 trở lên, root user được thiết lập xác thực bằng plugin `auth_socket` thay vì mật khẩu. Plugin này yêu cầu tên của user hệ điều hành gọi MySQL client phải khớp với tên của user MySQL được chỉ định trong lệnh. Vì vậy ta cần gọi lệnh mysql bằng quyền sudo để truy cập được vào root user MySQL:

    sudo mysql

Sau khi truy cập vào prompt MySQL, tạo một user mới bằng lệnh `CREATE USER`:

    CREATE USER 'username'@'host' IDENTIFIED WITH authentication_plugin BY 'password';

Trong đó "username" là tên người dùng tương ứng, sau đó là dấu `@` và tên của host mà user này kết nối. Nếu chỉ có nhu cầu truy cập đến user này cục bộ từ server Ubuntu thì ta có thể chỉ định `localhost`. 

Bạn cũng có thể lựa chọn sử dụng plugin xác thực người dùng cho phù hợp. Plugin `auth_socket` cung cấp khả năng bảo mật tốt mà không cần nhập mật khẩu để truy cập database. Tuy nhiên plugin này không cho phép kết nối từ xa nên có thể khiến tương tác giữa chương trình bên ngoài và MySQL trở nên phức tạp hơn khi cần thiết.

Ngoài ra bạn cũng có thể bỏ hẳn phần WITH `authentication_plugin` nếu muốn xác thực bằng plugin mặc định của MySQL (`caching_sha2_password`). Bạn có thể tham khảo chi tiết hơn tài liệu về plugin này trên website của MySQL: https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html#upgrade-caching-sha2-password

Chạy lệnh dưới đây để tạo một người dùng xác thực bằng `caching_sha2_password`, trong đó thay username và password cần tạo tương ứng:

    CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';

**Lưu ý:** Một số phiên bản PHP có thể sẽ gặp lỗi với plugin `caching_sha2_password`. Nếu muốn sử dụng database với một ứng dụng PHP như phpMyAdmin thì bạn nên tạo một user xác thực bằng `mysql_native_password` trước:

    CREATE USER 'username'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';

Sau đó chuyển phương thức xác thực thành `caching_sha2_plugin`:

    ALTER USER 'username' IDENTIFIED WITH mysql_native_password BY 'password';

Sau khi tạo xong người dùng, bạn có thể bắt đầu gán các đặc quyền phù hợp theo cú pháp chung là:

    GRANT PRIVILEGE ON database.table TO 'username'@'host';

Trong đó giá trị `PRIVILEGE` định nghĩa những hành động mà user được phép thực hiện trên database và table được chỉ định. Người dùng cũng có thể gán nhiều đặc quyền khác nhau trong cùng một lệnh bằng cách phân tách nhau bởi dấu phẩy. Nếu muốn gán quyền cho user trên toàn cục thì có thể dùng ký tự * (tất cả) ở vị trí tên của database và table. 

Danh sách các đặc quyền có thể gán có thể được xem tại đây: https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#privileges-provided-summary

Chạy lệnh `GRANT`, thay username tương ứng rồi gán quyền:

    GRANT CREATE, ALTER, DROP, INSERT, UPDATE, INDEX, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'username'@'localhost' WITH GRANT OPTION;

**Lưu ý** rằng lệnh này cũng có cả option `WITH GRANT OPTION` để cho phép user MySQL gán mọi quyền nó có cho các user khác trên hệ thống.

Sau khi chạy các lệnh `CREATE USER` và `GRANT`, bạn nên flush để giải phóng bộ nhớ cache trên server:

    FLUSH PRIVILEGES;

Sau đó thoát khỏi MySQL client:

    exit

Sau này nếu muốn đăng nhập bằng user mới, bạn có thể dùng lệnh như dưới đây:

    mysql -u username -p

Trong đó flag `-p` sẽ yêu cầu nhập mật khẩu để xác thực.

<a name="2.4"></a>
### Bước 4 - Kiểm tra MySQL

Sau khi install MySQL Ubuntu và cấu hình quyền user, MySQL sẽ tự động khởi chạy. Bây giờ bạn có thể kiểm tra nhanh trạng thái bằng lệnh sau:

    systemctl status mysql.service

Bạn có thể nhận được output tương tự như sau:

```
Output
● mysql.service - MySQL Community Server
     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2020-04-21 12:56:48 UTC; 6min ago
   Main PID: 10382 (mysqld)
     Status: "Server is operational"
      Tasks: 39 (limit: 1137)
     Memory: 370.0M
     CGroup: /system.slice/mysql.service
             └─10382 /usr/sbin/mysqld
```

Nếu MySQL chưa chạy thì bạn có thể khởi động thủ công bằng lệnh `sudo systemctl start mysql`.

Ngoài ra bạn cũng có thể thử kết nối đến database bằng công cụ `mysqladmin` - một client cho phép chạy các lệnh quản trị. Giả sử bạn cần kết nối đến một user MySQL có tên `vietnix (-u vietnix)`, yêu cầu nhập mật khẩu (`-p`) và trả về phiên bản:

    sudo mysqladmin -p -u vietnix version

Bạn có thể nhận được output tương tự như sau:

```
Output
mysqladmin  Ver 8.0.19-0ubuntu5 for Linux on x86_64 ((Ubuntu))
Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version        8.0.19-0ubuntu5
Protocol version    10
Connection        Localhost via UNIX socket
UNIX socket        /var/run/mysqld/mysqld.sock
Uptime:            10 min 44 sec

Threads: 2  Questions: 25  Slow queries: 0  Opens: 149  Flush tables: 3  Open tables: 69  Queries per second avg: 0.038
```

Hiện tại, [Vietnix](https://vietnix.vn/) đang cung cấp các dịch vụ VPS tốc độ cao với đa dạng cấu hình, mức giá giúp bạn có thể tự do cài đặt hệ điều hành Ubuntu và thiết lập, triển khai cấu hình MySQL dễ dàng mà không bị giới hạn quyền hay tính năng nào. Liên hệ với đội ngũ Vietnix để được tư vấn chi tiết hơn và lựa chọn gói dịch vụ tối ưu nhất.

<a name="3"></a>
## Lời kết

Bài viết này đã hướng dẫn chi tiết các bước install MySQL Ubuntu, thiết lập và cấu hình MySQL trên Ubuntu 20.04. Đây là một hệ quản trị cơ sở dữ liệu quan hệ rất thông dụng, thường được cài đặt trong stack LAMP (Linux, Apache, MySQL, PHP/Python/Perl). Nếu có bất kỳ thắc mắc nào khác, hãy để lại ở phần comment bên dưới để được Vietnix hỗ trợ nhanh nhất nhé!