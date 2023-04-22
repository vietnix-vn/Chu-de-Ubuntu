# Cách cài đặt Node.js trên Ubuntu 20.04 chi tiết

Node.js là một môi trường runtime chạy JavaScript để lập trình phía server-side. Ngôn ngữ này cho phép các developer tạo ra các chức năng phụ trợ có thể mở rộng bằng JavaScript. Trong bài viết này, Vietnix sẽ hướng dẫn cho các bạn chi tiết 3 cách cài đặt Node.js trên Ubuntu 20.04.

## Mục lục

- [Cách 1: Sử dụng apt để cài đặt gói NodeJS từ kho phần mềm mặc định của Ubuntu](#1)

- [Cách 2: Cài đặt Node.js với apt bằng cách dùng NodeSource PPA](#2)

- [Cách 3: Cách cài đặt Node.js trên Ubuntu 20.04 bằng Node Version Manager](#3)

- [Gỡ cài đặt Node.js](#4)

- [Lời kết](#5)

-------

<a name="1"></a>
## Cách 1: Sử dụng apt để cài đặt gói NodeJS từ kho phần mềm mặc định của Ubuntu

Ubuntu 20.04 chứa một phiên bản Node.js trong các kho lưu trữ mặc định để đảm bảo tính nhất quán trên các hệ thống. Để cài đặt Node.js thì trước hết bạn cần cập nhật apt trước:

    sudo apt update

Sau đó là cài đặt Node.js:

    sudo apt install nodejs

Kiểm tra xem quá trình cài đặt có thành công không, chúng ta dùng lệnh sau để kiểm tra phiên bản của Node.js:

    node -v

Nếu kết quả trả về là số phiên bản của Node.js thì bạn đã thực hiện cài đặt thành công.

```
Output
v10.19.0
```

**Lưu ý:** Tại thời điểm viết bài, phiên bản của Node.js đang là 10.19 và phiên bản này hiện không còn được hỗ trợ.

Tiếp theo, `npm` cũng là thứ hữu dụng mà bạn không nên bỏ qua, đây là trình quản lý gói Node.js, giúp bạn cài đặt các module và package khi làm việc sau này. Bạn có thể cài đặt `npm` bằng `apt` với lệnh sau:

    sudo apt install npm

<a name="2"></a>
## Cách 2: Cài đặt Node.js với apt bằng cách dùng NodeSource PPA

Để cài đặt một phiên bản Node.js bất kỳ, bạn có thể sử dụng PPA (Personal Package Archive) được duy trì bởi NodeSource. PPA có sẵn nhiều phiên bản của Node.js hơn là các repository mặc định của Ubuntu.

Đầu tiên, cài đặt PPA để có quyền truy cập vào các gói của nó. Từ thư mục home, dùng curl để truy xuất tập lệnh cài đặt cho phiên bản bạn muốn, ở đây lấy ví dụ bằng phiên bản 16.x:

```
cd ~
curl -sL https://deb.nodesource.com/setup_16.x -o /tmp/nodesource_setup.sh
```

Kiểm tra nội dung của tập lệnh đã tải xuống với `nano` hoặc text editor ưa thích của bạn:

    nano /tmp/nodesource_setup.sh

Khi bạn chỉnh sửa xong, hãy lưu lại và thoát. Sau đó chạy script vừa rồi với quyền sudo:

    sudo bash /tmp/nodesource_setup.sh

PPA sẽ được thêm vào cấu hình của bạn và local package cache của bạn sẽ được cập nhật tự động. Bây giờ bạn có thể cài đặt Node.js giống như cách bạn đã làm trong phần trước:

    sudo apt install nodejs

Và sau đó, dùng lệnh kiểm tra phiên bản để chắc rằng quá trình cài đặt đã thành công:

    node -v

Việc cài đặt Node.js từ NodeSource đã bao gồm `npm` rồi, cho nên bạn không cần cài đặt lại.

<a name="3"></a>
## Cách 3: Cách cài đặt Node.js trên Ubuntu 20.04 bằng Node Version Manager

Một cách khác để cài đặt Node.js là sử dụng `nvm` (Node Version Manager). Phần mềm này cho phép bạn cài đặt và duy trì nhiều phiên bản độc lập khác nhau của Node.js và các gói Node liên quan của chúng cùng một lúc.

Để cài đặt nvm trên máy Ubuntu 20.04, hãy truy cập trang dự án `nvm` trên GitHub. Sao chép lệnh `curl` từ file README. Điều này sẽ giúp bạn có phiên bản mới nhất của `nvm`.

Trước khi đưa câu lệnh vào bash, hãy chắc rằng câu lệnh đó không thực hiện điều gì lạ thường. Bạn có kiểm tra điều đó bằng cách loại bỏ phần `| bash` ở cuối lệnh `curl` rồi chạy:

    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh

Khi bạn đã chắc chắn thực hiện câu lệnh thì hãy ghép thêm | bash vào cuối lệnh:

    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash

Điều này sẽ cài đặt `nvm` script vào tài khoản người dùng của bạn. Để sử dụng, bạn có thể dùng lệnh:

    source ~/.bashrc

Bây giờ, bạn có thể tra cứu phiên bản hiện tại của Node bằng `nvm`:

    nvm list-remote

Phần output ra có thể khá dài. Bạn có thể chọn cài đặt một phiên bản của Node dựa vào danh sách được liệt kê. Chẳng hạn, để cài đặt phiên bản v14.10.0, bạn có thể dùng lệnh:

    nvm install v14.10.0

Bạn có thể xem các phiên bản khác nhau mà bạn đã cài đặt bằng cách liệt kê chúng:

    nvm list

Lệnh này sẽ cho bạn biết phiên bản hiện đang hoạt động tại dòng đầu tiên (-> v14.10.0), tiếp theo là tên alias và các phiên bản mà các alias đó chỉ tới.

**Lưu ý:** Nếu bạn cũng có một phiên bản Node.js được cài đặt thông qua `apt`, bạn có thể nhận được một system entry tại đây. Bạn luôn có thể kích hoạt phiên bản Node đã được cài đặt bằng lệnh:

    nvm use system

Ngoài ra, có các alias cho các phiên bản LTS (long-term support) khác của Node. Bạn cũng có thể cài đặt một phiên bản cụ thể dựa trên các alias này. Chẳng hạn, để cài đặt phiên bản lts/hydrogen thì bạn thực hiện lệnh sau:

    nvm install lts/hydrogen

Bạn cũng có thể chuyển đổi giữa các phiên bản đã cài đặt với lệnh:

    nvm use v14.10.0

Ngoài ra, bạn cũng nên tìm hiểu thêm các hướng dẫn cài đặt và cấu hình Gitlab trên Ubuntu 20.04 có trên website Vietnix để có thể bổ sung thêm kiến thức về các tùy chọn cho Ubuntu 20.04.

<a name="4"></a>
## Gỡ cài đặt Node.js

Bạn có thể gỡ cài đặt Node.js bằng `apt` hoặc `nvm`, tùy thuộc vào cách bạn cài đặt. Để xóa phiên bản Node khỏi repository, hãy sử dụng lệnh:

    sudo apt remove nodejs

Theo mặc định, lệnh này giữ lại bất kỳ file cấu hình local nào đã được tạo kể từ khi cài đặt. Nếu bạn không muốn lưu các file cấu hình này, hãy sử dụng lệnh sau:

    sudo apt purge nodejs

Để gỡ cài đặt phiên bản Node.js mà bạn đã cài đặt bằng `nvm`, trước tiên hãy xác định xem đó có phải là phiên bản hoạt động hiện tại không:

    nvm current

Nếu phiên bản bạn đang muốn gỡ không phải là phiên bản đang hoạt động hiện tại, bạn có thể chạy lệnh:

    nvm uninstall node_version

Lệnh này sẽ gỡ cài đặt phiên bản Node.js mà bạn đã chọn.

Nếu phiên bản bạn muốn xóa là phiên bản hoạt động hiện tại, trước tiên bạn cần deactivate `nvm` để kích hoạt các thay đổi của mình:

    nvm deactivate

Bây giờ bạn có thể gỡ cài đặt phiên bản hiện tại bằng lệnh `uninstall`, lệnh này sẽ xóa tất cả các file được liên kết với phiên bản Node.js được chỉ định.

Vietnix hiện đang cung cấp nhiều gói dịch vụ hosting có hỗ trợ Node.js từ phiên bản 6, 8, 9, 10 và 11 với giá cả phải chăng, tốc độ cao và ổn định. Điều này giúp cho bạn có thể triển khai các ứng dụng Node.js trên website một cách dễ dàng và hiệu quả.

<a name="5"></a>
## Lời kết

Trên đây là một số cách để có thể cài đặt và sử dụng Node.js trên Ubuntu Server 20.04. Đối với nhiều người dùng, sử dụng `apt` với repository mặc định là đủ. Nếu bạn cần các phiên bản mới hơn hoặc một phiên bản cụ thể nào đó của Node, bạn nên sử dụng repository PPA. Nếu bạn đang phát triển các ứng dụng Node và cần chuyển đổi giữa các phiên bản Node thường xuyên, hãy chọn cách cài đặt bằng `nvm`. Chúc bạn thực hiện thành công.

Nguồn: [Vietnix.vn](https://vietnix.vn/cach-cai-dat-node-js-tren-ubuntu-20-04/)