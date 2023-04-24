# Hướng dẫn cài Chrome trên Ubuntu qua 2 cách đơn giản và chi tiết

Google Chrome luôn là một trong những trình duyệt web phổ biến nhất trong nhiều năm trở lại đây. Mặc dù Ubuntu có sẵn Firefox khi cài đặt, nhưng nhiều người dùng vẫn muốn trải nghiệm Google Chrome trên Ubuntu. Bài viết này sẽ hướng dẫn cách cài Chrome trên Ubuntu nhanh nhất.

## Mục lục

- [Cách cài Google Chrome trên Ubuntu](#1)
    - [1. Cài Chrome trên Ubuntu bằng dòng lệnh dpkg](#1.1)
    - [2. Cài Chrome trên Ubuntu bằng đồ họa](#1.2)
- [Một số mẹo khi dùng Chrome trên Ubuntu](#2)
    - [Đồng bộ hóa Chrome với tài khoản Google](#2.1)
    - [Thêm Chrome vào phần Favourites](#2.2)
    - [Tự động cập nhật Google Chrome](#2.3)
-[Lời kết](#3)
--------------
<a name="1"></a>
## Cách cài Google Chrome trên Ubuntu

Cài đặt Chrome trên Ubuntu rất dễ dàng. Tất cả những gì bạn phải làm là tải xuống tệp gói từ nguồn chính thức và giải nén chúng trên hệ thống của mình. Có nhiều cách để thực hiện việc này, với sự trợ giúp của trình quản lý gói dpkg hoặc phần mềm quản lý ứng dụng.

<a name="1.1"></a>
### 1. Cài Chrome trên Ubuntu bằng dòng lệnh dpkg

Với những người dùng đã quen với Terminal thì có thể cài đặt Chrome trên Ubuntu mà không gặp bất kỳ khó khăn nào. Bạn có thể cài đặt Chrome trên Ubuntu bằng dòng lệnh dpkg, các bước thực hiện như sau:

- Bước 1: Để có thể cài đặt Chrome bằng dòng lệnh, đầu tiên bạn hãy khởi chạy thiết bị đầu cuối bằng tổ hợp phím **Ctrl + Alt + T**.

- Bước 2: Bạn cần download file `.deb` bằng lệnh wget. Đây là tiện ích của Linux cho phép bạn tải xuống được các tệp HTTP, HTTPS, FTP và FTPS.

    wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb

- Bước 3: Bạn có thể cài đặt các gói đã tải xuống bằng dpkg hoặc apt. Bạn chỉ cần nhập một trong các dòng lệnh sau vào Terminal.

    sudo dpkg -i google-chrome-stable_current_amd64.deb

    sudo apt install ./google-chrome-stable_current_amd64.deb

- Bước 4: Lúc này hệ thống sẽ yêu cầu bạn nhập mật khẩu người dùng của bạn cho mục đích ghi nhật ký. Bạn chỉ cần nhập mật khẩu và nhấn **Enter**.

<a name="1.2"></a>
### 2. Cài Chrome trên Ubuntu bằng đồ họa

Đối với những người mới dùng Ubuntu hay Linux thì việc cài đặt trình duyệt web qua Terminal sẽ tương đối phức tạp, do đó bạn có thể tham khảo cài đặt qua các bước đơn giản dưới đây:

Lưu ý rằng Google Chrome chỉ hỗ trợ các hệ điều hành 64-bit.

- Bước 1: Truy cập vào website của Google Chrome để download tại đây.

- Bước 2: Sau đó click vào nút **Download Chrome**. Chọn download file **64 bit .deb** để cài Chrome trên Ubuntu. Tiếp tục chọn **Accept and Install** để chấp nhận cài đặt.

- Bước 3: Tiếp theo, chọn lưu file về máy tính (đừng lưu về Software Install của Ubuntu).

- Bước 4: Sau khi tải xong, bạn vào thư mục chứa file cài đặt đã tải về máy, sau đó click đúp vào file `.deb`.

- Bước 5: Sau đó, hệ thống sẽ hiển thị trung tâm phần mềm của Ubuntu để cho phép cài đặt Google Chrome như ảnh dưới đây.

**Lưu ý:** Đối với Ubuntu 20.04, ta cần click chuột phải vào file `.deb` rồi chọn **Open With** > **Software Install** để cài đặt.

- Bước 6: Sau đó nhập mật khẩu để bắt đầu quá trình cài đặt.

- Bước 7: Sau khi cài Chrome trên Ubuntu xong, ta có thể tìm thấy ứng dụng ở trong menu. Bây giờ chỉ cần click vào icon để chạy và bắt đầu sử dụng Google Chrome trên Ubuntu thôi.

<a name="2"></a>
## Một số mẹo khi dùng Chrome trên Ubuntu

Để kết thúc bài viết, Vietnix muốn đưa ra một số mẹo cho người dùng khi sử dụng Google Chrome trên Ubuntu.

<a name="2.1"></a>
### Đồng bộ hóa Chrome với tài khoản Google

Nếu đăng nhập Chrome bằng tài khoản Google thì ta có thể đồng bộ bookmark, lịch sử duyệt web, plugin và các tiện ích trên nhiều thiết bị khác nhau.

<a name="2.2"></a>
### Thêm Chrome vào phần Favourites

Nếu sử dụng Google Chrome thường xuyên thì có thể thêm vào phần ứng dụng yêu thích để truy cập nhanh chóng.

<a name="2.3"></a>
### Tự động cập nhật Google Chrome

Google Chrome thêm kho lưu trữ chính thức của Google vào danh sách nguồn của hệ thống để nhận các bản cập nhật và bản phát hành trong tương lai. Bạn có thể xác minh rằng kho lưu trữ đã được thêm vào hệ thống của mình bằng cách đọc tệp danh sách nguồn của Google Chrome.

    cat /etc/apt/source.lst.d/google-chrome.list

Bạn sẽ nhận được một đầu ra như sau:

```
THIS FILE IS AUTOMATICALLY CONFIGURED ###
# You may comment out this entry, but any other modifications may be lost.
deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main
```

Nếu bạn không thể tìm thấy tệp nói trên trong hệ thống của mình. Bạn có thể tạo thủ công và nối đoạn mã đầu ra vào tệp.

    sudo touch /etc/apt/source.list.d/google-chrome.list

Chỉnh sửa tệp bằng trình soạn thảo văn bản của bạn.

    nano /etc/apt/source.list.d/google-chrome.list

Cuối cùng thêm đoạn mã đầu ra đã được cung cấp bên trên và lưu tệp.

<a name="3"></a>
## Lời kết

Việc cài Chrome trên Ubuntu là tương đối đơn giản mặc dù không có sẵn trong Software Center của hệ điều hành. Bài viết này đã hướng dẫn hai cách cài đặt đơn giản và nhanh chóng nhất là download trực tiếp từ website hoặc cài đặt qua Terminal trên Ubuntu. Cảm ơn các bạn đã theo dõi bài viết này.

Nguồn: [Vietnix.vn](https://vietnix.vn/cai-chrome-tren-ubuntu/)

## Tham khảo bài viết mới 

1. [Cách cài đặt Node.js trên Ubuntu 20.04 chi tiết](https://github.com/vietnix-vn/Chu-de-Ubuntu/blob/Vietnix/C%C3%A1ch%20c%C3%A0i%20%C4%91%E1%BA%B7t%20Node.js%20tr%C3%AAn%20Ubuntu%2020.04%20chi%20ti%E1%BA%BFt.md)

2. [Hướng dẫn cài đặt Mastodon trên Ubuntu 20.04 chi tiết](https://github.com/vietnix-vn/Chu-de-Ubuntu/blob/Vietnix/H%C6%B0%E1%BB%9Bng%20d%E1%BA%ABn%20c%C3%A0i%20%C4%91%E1%BA%B7t%20Mastodon%20tr%C3%AAn%20Ubuntu%2020.04%20chi%20ti%E1%BA%BFt.md)

3. [Hướng dẫn cấu hình và cài đặt Ansible trên Ubuntu 20.04](https://github.com/vietnix-vn/Chu-de-Ubuntu/blob/Vietnix/H%C6%B0%E1%BB%9Bng%20d%E1%BA%ABn%20c%E1%BA%A5u%20h%C3%ACnh%20v%C3%A0%20c%C3%A0i%20%C4%91%E1%BA%B7t%20Ansible%20tr%C3%AAn%20Ubuntu%2020.04.md)