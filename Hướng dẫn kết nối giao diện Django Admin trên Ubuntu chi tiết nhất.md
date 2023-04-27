# Hướng dẫn kết nối giao diện Django Admin trên Ubuntu chi tiết nhất

Django là một framework cấp cao được viết bằng ngôn ngữ Python. Django cung cấp các công cụ và thư viện để người dùng xây dựng các ứng dụng web nhanh chóng và hiệu quả. Bài viết này sẽ hướng dẫn bạn cách kích hoạt trang quản trị và kết nối giao diện Django admin trên Ubuntu để quản lý trang web của mình.

## Mục lục

- [Bước 1: Kiểm tra các yêu cầu cần thiết để kết nối giao diện Django Admin trên Ubuntu](#1)
- [Bước 2: Kích hoạt giao diện Django Admin](#2)
- [Bước 3: Xác minh cài đặt Django Admin](#3)
- [Bước 4: Tạo tài khoản siêu người dùng Admin](#4)
- [Bước 5: Tạo mẫu URL](#5)
- [Bước 6: Kết nối ứng dụng với Admin](#6)
- [Bước 7: Xác minh các thay đổi của ứng dụng](#7)
- [Lời kết](#8)
-------
<a name="1"></a>
## Bước 1: Kiểm tra các yêu cầu cần thiết để kết nối giao diện Django Admin trên Ubuntu

Trước khi bắt đầu vào phần hướng dẫn cụ thể, bạn cần phải đảm bảo rằng máy bạn đã có đủ các phần mềm và ứng dụng cần thiết:

- Phiên bản Django 4 hoặc các phiên bản cao hơn.

- Kết nối Django với cơ sở dữ liệu. Trong bài hướng dẫn này chúng ta sẽ dùng MySQL.

- Sử dụng hệ điều hành dựa trên Unix, cụ thể là Ubuntu 22.04 cloud server.

Nếu bạn muốn phát triển ứng dụng web dựa trên framework như Django thì bạn cần phải có một máy chủ linh hoạt, cấu hình mạnh mẽ, phù hợp với nhiều ngôn ngữ và hỗ trợ đa dạng hệ điều hành. Giải pháp tốt nhất là lựa chọn sử dụng VPS.

Hiện tại, các gói [VPS tốc độ cao](https://vietnix.vn/vps-nvme/) tại Vietnix có cấu hình đa dạng, dễ dàng mở rộng theo nhu cầu sử dụng và hỗ trợ đa dạng hệ điều hành có thể giúp bạn triển khai ứng dụng Django hiệu quả.

<a name="2"></a>
## Bước 2: Kích hoạt giao diện Django Admin

Để bật giao diện Django Admin, chúng ta cần thêm `django.contrib.admin` vào danh sách **INSTALLED_APPS** trong tệp `settings.py`. Trước tiên, hãy điều hướng đến thư mục gốc của ứng dụng, kích hoạt môi trường ảo Python của bạn và điều hướng đến thư mục chứa tệp cài đặt:

```
cd ~/my_blog_app
. env/bin/activate
```

Thay thế `my_blog_app` bằng tên dự án của riêng bạn. Tiếp theo để đảm bảo rằng Django Admin được bật cho dự án của bạn bằng cách thêm vào danh sách **INSTALLED_APPS** trong tệp `settings.py`:

```
cd ~/my_blog_app/blog/blog/
nano settings.py
```

Sau đó thêm `django.contrib.admin` và tên ứng dụng của bạn vào đầu danh sách **INSTALLED_APPS** như sau:

```
INSTALLED_APPS = [
    'blogsite',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

Ở đây tên ứng dụng là **blogsite**, bạn có thể thay thế **blogsite** thành tên ứng dụng của riêng bạn. Sau đó lưu lại bằng tổ hợp phím **Ctrl + X** rồi **Y** để chọn **Yes** và **Enter** để đóng tệp.

Như vậy là coi như ứng dụng Django Admin đã được cài đặt, chúng ta cần thêm URL vào tệp `urls.py` của dự án. Tiến hành mở lại tệp `urls.py` bằng `nano`:

    nano urls.py

Sau đó thêm câu lệnh `from django.contrib import admin` vào đầu của tệp như bên dưới:

```
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls),
]
```

Tiến hành lưu lại tệp bằng tổ hợp phím **Ctrl + X** rồi **Y** để chọn **Yes** và **Enter** để đóng tệp. Vậy là ta đã thiết lập xong cho 2 tệp `settings.py` và `urls.py`, đồng nghĩa với việc ta đã có quyền truy cập vào giao diện Django Admin.

<a name="3"></a>
## Bước 3: Xác minh cài đặt Django Admin

Tiếp theo cần điều hướng đến thư mục chứa tệp `manage.py` của dự án. Điều này thường có thể được tìm thấy trong thư mục gốc của dự án:

    cd ~/my_blog_app/blog

Sau khi đã vào đúng thư mục, chúng ta cần chạy lệnh `migrate` để cập nhật cơ sở dữ liệu của dự án với các mô hình. Trong trường hợp này, chúng ta đã thêm ứng dụng Django Admin vào danh sách các ứng dụng được cài đặt của dự án. Lệnh `migrate` có dạng như sau:

    python manage.py migrate

**Lưu ý** rằng chúng ta cần chạy lệnh này mỗi khi thay đổi mô hình. Nếu không có bất kỳ thay đổi nào trong các tệp trên, khi chạy lệnh `migrate`, chúng ta sẽ nhận được đầu ra thông thường sẽ như sau:

```
Output
Operations to perform:
  Apply all migrations: admin, auth, blogsite, contenttypes, sessions
Running migrations:
  No migrations to apply.
```

Bây giờ đã sẵn sàng để khởi chạy máy chủ Django. Bởi đây là một dự án cục bộ, nên sẽ khởi chạy trên máy chủ cục bộ. Máy chủ sẽ chạy trên cổng 8000:

    python manage.py runserver 0.0.0.0:8000

Thay 0.0.0.0 bằng địa chỉ IP của bạn. Sau đó, hãy truy cập URL của trang bằng trình duyệt:

    http://ip-server:8000/admin/

Nếu bạn thấy màn hình đăng nhập hiện lên thì đã thành công. Việc truy cập vào màn hình này cho biết đã kích hoạt ứng dụng Admin thành công. Tuy nhiên, chúng ta có thể chưa có tài khoản quản trị Django. Xem tiếp hướng dẫn để có thể tạo tài khoản quản trị để đăng nhập nhé.

<a name="4"></a>
## Bước 4: Tạo tài khoản siêu người dùng Admin 

Chúng ta sẽ tiến hành tạo tài khoản siêu người dùng (Super-user), có thể hiểu đơn giản là tài khoản này này sẽ có mức độ ưu tiên cao nhất trong các loại tài khoản.

Đầu tiên, mở một cửa sổ terminal mới để kết nối với máy chủ hoặc tạm dừng ứng dụng Django bằng cách nhấn **CTRL + C** để chúng ta có thể làm việc trong môi trường lập trình trên terminal của máy chủ.

Lệnh tạo tài khoản siêu người dùng có dạng như sau:

    python manage.py createsuperuser

Sau đó, chúng ta sẽ được yêu cầu điền thông tin cho tên người dùng, địa chỉ email và mật khẩu. Trong bài hướng dẫn này, chúng ta sẽ tạo một tài khoản quản trị với tên người dùng là `admin_user`, địa chỉ email là **example@example.com** và mật khẩu là **admin123**.

**Lưu ý:** Trong quá trình nhập mật khẩu, bạn sẽ không nhìn thấy được các ký tự trong quá trình nhập. Sau khi nhập xong 2 lần nhấn Enter để kết thúc, vậy là chúng ta đã tạo thành công tài khoản.

Tiến hành bật lại máy chủ Django bằng lệnh sau (lưu ý thay 0.0.0.0 bằng IP của bạn):

    python manage.py runserver 0.0.0.0:8000

Khởi chạy xong máy chủ Django thì truy cập vào URL bằng trang trình duyệt và tiến hành đăng nhập tài khoản mà bạn vừa khởi tạo:

    http://ip-server:8000/admin/

<a name="5"></a>
## Bước 5: Tạo mẫu URL

Trong bước trước đó, chúng ta đã đăng nhập thành công vào giao diện quản trị, tuy nhiên bạn có thể nhận thấy rằng ứng dụng blog của chúng ta vẫn chưa sẵn sàng. Để thêm ứng dụng blog vào giao diện quản trị, chúng ta cần thêm và đăng ký nó với các mô hình liên quan là Post và Comment.

Để làm điều này, chúng ta sẽ tạo một tệp rỗng có tên là `urls.py`, trong thư mục blogsite, như sau:

    touch ~/my_blog_app/blog/blogsite/urls.py

Sau đó, chúng ta sẽ mở tệp `urls.py` với trình soạn thảo nano:

```
cd ~/my_blog_app/blog/blogsite/
nano urls.py
```

Thêm đoạn mã sau vào tệp:

```
from django.urls import path
from . import views
urlpatterns = [
    path('$/', views.posts, name='posts'),
    path('$/', views.comments, name='comments'),
]
```

Đây là các biểu thức mẫu URL cần thiết để cho phép ứng dụng của chúng ta truy cập vào các chế độ xem cho Posts và Comments. Tuy nhiên, hiện tại chúng ta chưa tạo các chế độ xem này và sẽ nói về chúng ở những bước sau.

<a name="6"></a>
## Bước 6: Kết nối ứng dụng với Admin

Kết nối ứng dụng Blog với trang quản trị cho phép chúng ta thấy các liên kết cho các Posts và Comments trong bảng điều khiển quản trị. Hiện tại, bảng điều khiển chỉ hiển thị liên kết cho các nhóm (Groups) và người dùng (Users).

Để kết nối 2 ứng dụng với nhau, chúng ta cần đăng ký các mô hình Posts và Comments vào tệp `admin.py` của ứng dụng blogsite:

    cd ~/my_blog_app/blog/blogsite

Mở tệp `admin.py` trong trình soạn thảo văn bản nano:

    nano admin.py

Chỉnh sửa các lệnh để giống với bên dưới đây:

```
admin.py
from django.contrib import admin
from blogsite.models import Post
from blogsite.models import Comment

admin.site.register(Post)
admin.site.register(Comment)
```

Sau khi chỉnh sửa xong hay lưu lại tệp bằng tổ hợp **Ctrl + X** và thoát. Bây giờ, bảng quản trị sẽ nhận dạng và hiển thị liên kết đến các mô hình bài đăng và bình luận.

<a name="7"></a>
## Bước 7: Xác minh các thay đổi của ứng dụng

Ứng dụng Django đã được định cấu hình thành công để quản lý các Posts và Comments trực tiếp từ bảng quản trị. Khởi động lại máy chủ trên localhost, đăng nhập vào bảng quản trị và xác minh các thay đổi:

    http://ip-server:8000/admin/

Đăng nhập tài khoản và mật khẩu của bạn để truy cập giao diện quản trị, sau khi đăng nhập nếu thấy không có gì thay đổi so với trước đây, bạn có thể refresh lại trang, bạn sẽ thấy một số trang web được hiển thị bên dưới phần blogsite như Post và Comment.

Sau khi hoàn thành thử nghiệm, chúng ta có thể dừng máy chủ Django một cách an toàn và thoát khỏi môi trường ảo. Từ cửa sổ bảng điều khiển, nhấn **Ctrl + C** để dừng máy chủ Django. Sau đó chạy lệnh sau để thoát khỏi môi trường ảo:

    deactivate

Sau khi chạy lệnh trên, terminal sẽ đưa bạn trở lại phiên bình thường.

Ngoài ra, bạn cũng có thể tham khảo thêm hướng dẫn chi tiết cách tạo các Models trong Django cũng như cách cài đặt Django và thiết lập môi trường development trên Ubuntu 20.04 hiện có trên website Vietnix để có thể nâng cao thêm các tùy chọn kiến thức cho bản thân.

<a name="8"></a>
## Lời kết

Qua 7 bước hướng dẫn trên, bạn đã được giới thiệu cách bật giao diện quản trị, tạo tài khoản quản trị và đăng ký các mô hình tùy chỉnh của ứng dụng (bài đăng và bình luận) với quản trị viên. Giao diện quản trị của Django sẽ giúp bạn tạo Posts và quản lý các Comments trên blog của mình. Cảm ơn các bạn đã đọc bài viết, Vietnix hy vọng sẽ giúp ích được cho các bạn. Nếu thấy hay thì hãy chia sẻ bài viết này cho nhiều người hơn nhé.

Nguồn: [Vietnix.vn](https://vietnix.vn/ket-noi-giao-dien-django-admin-tren-ubuntu/)

## Tham khảo bài viết mới

1. [Install MySQL Ubuntu: Cách cài đặt MySQL trên Ubuntu 20.04 chi tiết nhất](https://github.com/vietnix-vn/Chu-de-Ubuntu/blob/Vietnix/Install%20MySQL%20Ubuntu%20-%20C%C3%A1ch%20c%C3%A0i%20%C4%91%E1%BA%B7t%20MySQL%20tr%C3%AAn%20Ubuntu%2020.04%20chi%20ti%E1%BA%BFt%20nh%E1%BA%A5t.md)

2. [Hướng dẫn cài Chrome trên Ubuntu qua 2 cách đơn giản và chi tiết](https://github.com/vietnix-vn/Chu-de-Ubuntu/blob/Vietnix/H%C6%B0%E1%BB%9Bng%20d%E1%BA%ABn%20c%C3%A0i%20Chrome%20tr%C3%AAn%20Ubuntu%20qua%202%20c%C3%A1ch%20%C4%91%C6%A1n%20gi%E1%BA%A3n%20v%C3%A0%20chi%20ti%E1%BA%BFt.md)

3. [Cách cài đặt Node.js trên Ubuntu 20.04 chi tiết](https://github.com/vietnix-vn/Chu-de-Ubuntu/blob/Vietnix/C%C3%A1ch%20c%C3%A0i%20%C4%91%E1%BA%B7t%20Node.js%20tr%C3%AAn%20Ubuntu%2020.04%20chi%20ti%E1%BA%BFt.md)