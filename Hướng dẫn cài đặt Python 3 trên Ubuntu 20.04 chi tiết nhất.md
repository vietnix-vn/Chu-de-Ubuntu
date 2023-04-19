# Hướng dẫn thiết lập môi trường lập trình và cài đặt Python 3 trên Ubuntu 20.04
Python là ngôn ngữ lập trình ngày càng phổ biến cho cả người mới bắt đầu và nhà phát triển có kinh nghiệm, rất linh hoạt và dễ dàng tiếp cận. Bài viết này sẽ hướng dẫn bạn quy trình cài đặt Python 3 trên Ubuntu 20.04 cũng như thiết lập môi trường lập trình để có thể viết và thực thi các mã Python. Đây là các bước thiết yếu để làm việc với các ứng dụng dựa trên Python. 

## Mục lục

[Bước 1 - Cập nhật Package Lists để chuẩn bị cho cài đặt Python 3 trên Ubuntu 20.04](#1)

[Bước 2 - Kiểm tra và cài đặt Python 3](#2)

[Bước 3 - Thiết lập môi trường lập trình](#3)

[Bước 4: Viết và chạy mã Python](#4)

---------
<a name="1"></a>
## Bước 1 - Cập nhật Package Lists để chuẩn bị cho cài đặt Python 3 trên Ubuntu 20.04

Trước khi bắt đầu vào loạt phần hướng dẫn bên dưới, hãy chắc rằng tài khoản người dùng của bạn có quyền `sudo`.

Ubuntu 20.04 thông thường đã được cài đặt sẵn Python 3. Để đảm bảo rằng các phiên bản hiện tại đã được cập nhật, bạn có thể chạy lệnh sau để cập nhật chỉ mục cho gói cục bộ:

    sudo apt update

Sau đó chạy tiếp lệnh bên dưới để nâng cấp lên phiên bản mới:

    sudo apt -y upgrade

Cờ `-y` sẽ xác nhận rằng chúng ta đồng ý cài đặt tất cả các phần mềm cần thiết. Tuy nhiên ở một số phiên bản Linux sẽ yêu cầu bạn phải xác nhận các thông báo bổ sung trong quá trình cập nhật và nâng cấp hệ thống.

<a name="2"></a>
## Bước 2 - Kiểm tra và cài đặt Python 3

Sau khi quá trình trên hoàn tất, bạn có thể kiểm tra phiên bản Python 3 được cài đặt trong hệ thống bằng cách nhập:

    python3 -V

Bạn sẽ nhận được output là kết quả kiểm tra phiên bản của Python giống như sau:

```
Output
Python 3.7.10
```

Nếu bạn không nhận được kết quả đầu ra là một phiên bản Python cụ thể thì có thể cài đặt lại Python thông qua lệnh này:

    sudo apt install python3

Tiếp đến, để quản lý các gói phần mềm cho Python, hãy cài đặt `pip`. Đây là một công cụ hỗ trợ cài đặt và quản lý các gói lập trình (programming packages) mà bạn có thể muốn sử dụng trong dự án. Dùng lệnh bên dưới để cài đặt:

    sudo apt install -y python3-pip

Sau khi cài xong `pip`, giờ đây bạn có đã có thể cài đặt các gói lập trình mà Python thường được sử dụng như Numpy, Pandas. Các gói Python có thể được cài đặt bằng cách nhập lệnh:

    pip3 install package_name

Có thể thay thế `package_name` bằng tên gói mà bạn muốn cài. Ví dụ để cài gói Numpy bạn nhập lệnh `pip3 install numpy`.

Ngoài ra bạn còn có thể dùng lệnh sau đây để cài đặt các thành phần giúp đảm bảo môi trường lập trình Python đầy đủ hơn. Lệnh này giúp bạn cài đặt một số gói và công cụ phát triển liên quan đến Python:

    sudo apt install -y build-essential libssl-dev libffi-dev python3-dev

Đến đây xem như đã gần hoàn thành phần thiết lập và cài đặt Python. Bây giờ hãy cùng qua bước tiếp theo để thiết lập môi trường lập trình trên máy chủ Ubuntu của bạn nhé.

Ngoài ra, bạn cũng có thể tham khảo thêm [hướng dẫn tạo Rules cùng Timelines và các Cases từ sự kiện Suricata bằng ứng dụng SIEM của Kibana](https://vietnix.vn/tao-rules-cung-timelines-bang-ung-dung-siem-cua-kibana/) trên Vietnix để có thêm các tùy chọn kiến thức cho riêng mình.

<a name="3"></a>
## Bước 3 - Thiết lập môi trường lập trình

Để có thể viết và thực thi các mã Python trên Ubuntu, chúng ta cần phải thiết lập một môi trường lập trình biệt lập hay còn gọi là môi trường ảo (Virtual Environment). Môi trường ảo cho phép bạn có một không gian biệt lập trên máy chủ, đảm bảo rằng mỗi dự án đều có thể có tập hợp đầy đủ các thành phần cần thiết mà không làm ảnh hưởng hay gián đoạn các dự án khác.

Bạn có thể thiết lập số lượng môi trường lập trình Python tùy thích. Mỗi môi trường về cơ bản là một danh mục hoặc là một thư mục trên máy chủ Ubuntu mà sở hữu một vài tập lệnh trong đó để làm cho nó hoạt động như một môi trường.

Có rất nhiều công cụ khác nhau để thiết lập môi trường lập trình, nhưng trong bài viết này chúng ta sẽ học cách dùng module `venv` để thiết lập. Để cài đặt module `venv`, bạn gõ lệnh:

    sudo apt install -y python3-venv

Tiếp đến, tạo một thư mục và điều hướng đến thư mục vừa tạo đó bằng lệnh:

    mkdir environments

    cd environments

Ở đây thư mục của chúng ta là environments, tuy nhiên bạn có thể đặt một tên khác nếu muốn. Sau khi điều hướng đến thư mục hiện hành, bạn tiến hành khởi tạo môi trường với lệnh:

    python3 -m venv my_env

Sau khi chạy lệnh này bạn sẽ thấy xuất hiện một số thư mục mới xuất hiện. Đó là các thư mục cần thiết để thiết lập môi trường, có thể kiểm tra nhanh danh sách thư mục bằng cách nhập:

    ls my_env

Bạn sẽ thấy output xuất hiện một số thư mục tương tự bên dưới:

```Output
bin include lib lib64 pyvenv.cfg share
```

Tiếp theo, để tiến hành sử dụng môi trường này, chúng ta cần phải khởi động nó lên bằng cách dùng lệnh sau để kích hoạt môi trường vừa tạo:

    source my_env/bin/activate

Bạn sẽ thấy sự thay đổi về dấu nhắc lệnh của mình. Giờ đây nó không phải là biểu tượng $ nữa mà sẽ là tên môi trường vừa tạo. Điều đó có nghĩa là bạn hiện đã ở trong môi trường lập trình vừa khởi chạy và đã có thể tiến hành viết hay thực thi các mã Python cho dự án của mình.

**Lưu ý nhỏ:** Khi cài các package mới ở trong môi trường ảo này, nếu bạn dùng lệnh python3 hoặc pip3 nhưng không hoạt động thì có thể thay thế lại bằng lệnh python và pip. Ví dụ như pip install numpy.

Ngoài ra, bạn cũng có thể tìm hiểu về [cách thực hiện các thao tác CRUD (Create, Read, Update, Delete) trong MongoDB bằng thư viện PyMongo trên Ubuntu 20.04](https://vietnix.vn/thao-tac-crud-trong-mongodb-bang-pymongo-ubuntu-20-04/) để có thể tương tác hơn với cơ sở dữ liệu MongoDB, từ việc thêm, đọc, cập nhật, xóa dữ liệu, giúp bạn phát triển ứng dụng MongoDB trên Ubuntu 20.04 một cách hiệu quả nhất.

<a name="4"></a>
## Bước 4: Viết và chạy mã Python

Bây giờ bạn đã sẵn sàng để viết và chạy mã Python! Mở trình soạn thảo văn bản `nano` và tạo một tệp mới có tên `my_script.py`. Thêm một số mã Python vào tệp, sau đó lưu nó và tiến hành chạy thử. Bạn cần làm điều này để xác định liệu môi trường vừa khởi động có hoạt động đúng hay không.

Khởi tạo tệp mới bằng lệnh:

    nano my_script.py

Tiếp theo hãy nhập một mã lệnh cơ bản của Python để in ra thứ gì đó:

    print("Hello, World!")

Lưu lại bằng tổ hợp phím Ctrl + X, Y, và Enter để lưu. Và tiến hành khởi chạy tệp để kiểm tra:

    python my_script.py

Nếu bạn thấy đầu ra xuất ra dòng chữ **Hello, World** có nghĩa là đã thành công, môi trường hoạt động bình thường, các mã Python đã có thể thực thi một cách ổn định.

Nếu bạn muốn thoát ra khỏi môi trường thì dùng lệnh sau:

    deactivate

Hiện tại, Vietnix đang cung cấp các dịch vụ VPS tốc độ cao với đa dạng cấu hình, mức giá giúp bạn có thể tự do cài đặt hệ điều hành Ubuntu và thiết lập, triển khai Python dễ dàng. Liên hệ với đội ngũ Vietnix để được tư vấn chi tiết hơn và lựa chọn gói dịch vụ tối ưu nhất.

Bây giờ bạn đã thành công cài đặt Python 3, thiết lập môi trường lập trình, viết và thực thi một số mã Python trên máy chủ Ubuntu. Bạn có thể bắt đầu dự án của mình ngay. Nếu thấy bài hướng dẫn thiết lập môi trường lập trình và cài đặt Python 3 trên Ubuntu 20.04 hữu ích thì hãy chia sẽ đến nhiều người khác nhé.

Nguồn: [Vietnix.vn](https://vietnix.vn/cai-dat-python-3-tren-ubuntu-20-04/)

## Tham khảo bài viết mới

1. [Cách dời thư mục dữ liệu MySQL tới vị trí mới trên Ubuntu 20.04](https://github.com/vietnix-vn/Chu-de-Ubuntu/blob/Vietnix/C%C3%A1ch%20d%E1%BB%9Di%20th%C6%B0%20m%E1%BB%A5c%20d%E1%BB%AF%20li%E1%BB%87u%20MySQL%20t%E1%BB%9Bi%20v%E1%BB%8B%20tr%C3%AD%20m%E1%BB%9Bi%20tr%C3%AAn%20Ubuntu%2020.04.md)

2. [Hướng dẫn cách thêm và xóa User trên Ubuntu 20.04](https://github.com/vietnix-vn/Chu-de-Ubuntu/blob/Vietnix/H%C6%B0%E1%BB%9Bng%20d%E1%BA%ABn%20c%C3%A1ch%20th%C3%AAm%20v%C3%A0%20x%C3%B3a%20User%20tr%C3%AAn%20Ubuntu%2020.04.md)