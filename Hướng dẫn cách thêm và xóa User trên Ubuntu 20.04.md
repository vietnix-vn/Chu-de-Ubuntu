# Hướng dẫn cách thêm và xóa User trên Ubuntu 20.04

Khi làm việc với các hệ điều hành như Linux, Ubuntu hay MacOS, hay bất cứ một hệ thống nào, để tương tác thực hiện được các tác vụ của mình, đều cần phải có một tài khoản hay user với các đặc quyền tương ứng, giúp đảm bảo rằng mỗi user sẽ chỉ được làm những việc được cho phép. Trong bài hướng dẫn này, Vietnix sẽ giới thiệu cho các bạn cách để quản lý thêm và xóa user trên Ubuntu 20.04 cũng như cấp quyền cho một user trên hệ thống Ubuntu.

# Mục lục

[1. Giới thiệu](#1)

[2. Yêu cầu tiên quyết để thêm và xóa User trên Ubuntu 20.04](#2)

[3. Các bước tiến hành](#3)
- [3.1. Thêm User](#3.1)
- [3.2. Cấp đặc quyền quyền Sudo cho user](#3.2)
- [3.3. Kiểm tra đặc quyền Sudo của User](#3.3)
- [3.4. Xóa User](#3.4)

[4. Lời kết](#4)

-----
<a name="1"></a>
# 1. Giới thiệu

Thêm và xóa users trên các hệ thống [Linux](https://vietnix.vn/linux-la-gi/) là một trong những tác vụ rất quan trọng mà một người quản trị viên cần nắm. Theo thông thường khi tạo một hệ thống mới, mặc định bạn chỉ được phép truy cập vào tài khoản root.

Và khi các bạn sử dụng hệ thống với vai trò là một root user, bạn sẽ có hoàn toàn quyền kiểm soát đối với hệ thống đó. Điều này sẽ khá nguy hiểm nếu chẳng may bạn thao tác không đúng lên một tác vụ nào đó sẽ có thể dẫn đến crash toàn bộ hệ thống. Đối với các tác vụ quản trị hệ thống phổ biến hiện nay, tốt hơn hết bạn nên thêm vào những user không có đặc quyền trên như root user để hạn chế truy cập đến các tác vụ có ảnh hướng lớn đến hệ thống. 

Bên cạnh đó, bạn vẫn có thể thêm bất kì các tài khoản không có đặc quyền cho các user hiện có trên hệ thống - mỗi user nên có một tài khoản riêng biệt cho chính user đó trên mỗi hệ thống.

Đối với các tác vụ yêu cầu quyền quản trị hay quyền admin, [Ubuntu](https://vietnix.vn/he-dieu-hanh-ubuntu-la-gi/) cung cấp một công cụ có tên gọi là `sudo`. Nói ngắn gọn, `sudo` sẽ cho phép các bạn có thể chạy các lệnh như những user khác bao gồm cả những user có quyền admin. Trong bài hướng dẫn này, các bạn sẽ tìm hiểu về cách tạo tài khoản user, gán quyền `sudo` cho user và xóa user.

<a name="2"></a>
# 2. Yêu cầu tiên quyết để thêm và xóa User trên Ubuntu 20.04

Bạn cần có một server Ubuntu phiên bản 20.04 và có quyền truy cập root và firewall (tường lửa) đã được bật.

Nếu bạn cần server để cài đặt Ubuntu bạn có thể tham khảo các gói dịch vụ VPS tốc độ cao tại Vietnix. Các gói VPS được thiết kế với cấu hình đa dạng, chi phí phải chăng, hỗ trợ nhiều hệ điều hành khác nhau, bạn có thể toàn quyền cài đặt và sử dụng theo mục đích riêng.

<a name="3"></a>
# 3. Các bước tiến hành

<a name="3.1"></a>
## 3.1. Thêm User

Nếu đã đăng nhập với quyền root user, các ban có thể tạo một user mới bất kì lúc nào bằng cách chạy lệnh sau trên terminal: 

    adduser newuser

Còn nếu đã đăng nhập bằng tài khoản của non-root user đã được cấp quyền `sudo`, các bạn có thể tạo một user mới với câu lệnh sau:

    sudo adduser newuser

Với cả hai cách tạo user mới trên, các bạn sẽ được yêu cầu phải trả lời các câu hỏi sau đây:

- Tạo và xác nhận mật khẩu cho user mới.
- Thêm các thông tin khác cho user mới. Lựa chọn là không bắt buộc và có thể được bỏ qua bằng cách nhấn <b>ENTER</b> nếu các bạn không muốn tận dụng các thông tin bổ sung này.
- Cuối cùng, các bạn sẽ được yêu cầu dể xác nhận lại các thông tin mà các bạn vừa cung cấp là chính xác. Nhấn <b>Y</b> để tiếp tục.

Đến đây, các bạn đã hoàn tất việc tạo user mới có tên là `newuser` và có thể sử dụng mật khẩu vừa tạo để đăng nhập.

Hơn nữa, nếu các bạn muốn user mới tạo của mình có thêm các đặc quyền admin khác, hãy tiếp tục theo dõi các mục tiếp theo sau đây.

<a name="3.2"></a>
## 3.2. Cấp đặc quyền quyền Sudo cho user

Nếu muốn user vừa tạo có khả năng thực thi các câu lệnh với đặc quyền root (hay admin), các bạn cần phải cho phép user đó truy cập vào `sudo`. Các bạn có hai cách để thực hiện việc này: 

- <b>Cách 1:</b> Thêm user mới tạo đó vào sudo group đã được tạo sẵn hay pre-defined.

- <b>Cách 2:</b> Chỉ định các đặc quyền cho mỗi user trong cấu hình của `sudo`.

Dưới đây, Vietnix sẽ hướng dẫn lần lượt về từng cách nói trên. Đầu tiên là thêm user mới tạo vào Sudo Group.

<a name="3.2.1"></a>
### 3.2.1. Thêm user mới vào Sudo Group

Theo mặc định, `sudo` trên các hê thống Ubuntu 20.04 đã được cấu hình để cung cấp đầy đủ các quyền cho tất cả các user nằm trong sudo group.

Để xem user mới tạo đang ở trong các groups nào, hãy sử dụng lệnh groups như sau:

    groups newuser

```
Output
ewuser : newuser
```
Mặc định, một user mới tạo sẽ chỉ ở trong group của chính user đó do `adduser` tạo ra ngoài profile của user. Như output trên, user mới tạo và group của mình sẽ có cùng tên.

Để thêm user vào một group mới, ta sử dụng lệnh usermod

    usermod -aG sudo newuser

`usermod` sẽ biết cần phải thêm user vào trong các group đã được liệt kê ra (với lệnh như trên là sudo group) nhờ vào lựa chọn `-aG`.

Một điều cần lưu ý ở đây là chính lệnh `usermod` cần phải có đặc quyền là `sudo`. Điều này có nghĩa là bạn chỉ có thể thêm user vào sudo group chỉ khi đã đăng nhập vào với tài khoản là root user hoặc một user khác đã được thêm vào bên trong group `sudo` từ trước. Ở trường hợp phía sau, bạn cần phải prefix lệnh của mình bằng `sudo` như ví dụ dưới đây.

    sudo usermod -aG sudo newuser

<a name="3.2.2"></a>
### 3.2.2. Chỉ định trực tiếp những đặc quyền của User trong `/etc/sudoers` 

Như đã đề cập ở trên, bạn có hai cách để cấp quyền `sudo` cho user. Sau khi đã tìm hiểu cách đầu tiên là thêm user vào một group có sẵn ở trên, sau đây các bạn sẽ tìm hiểu đến cách thứ hai là sử dụng lệnh `visudo`, lệnh này sẽ mở một file cấu hình được gọi là `/etc/sudoers` bằng editor mặc định của hệ thống và các bạn có thể chỉ rõ đặc quyền sử dụng cơ bản của từng user bằng file này. 

`visudo` là cách được khuyến khích nên sử dụng khi cần phải chỉnh sửa `/etc/sudoers`. Vì nó sẽ giúp ngăn chặn việc chỉnh sửa phải bị diễn ra đồng thời và `visudo` sẽ giúp thực hiên kiểm tra tính hợp lệ của nội dung bên trong file trước khi được cập nhật lại. Bằng cách này, bạn sẽ tránh được việc cấu hình sai cho `sudo` và không thể khôi phục hay hoàn tác lại được do đã bị mất quyền `sudo`.

Nếu đã đăng nhập bằng quyền root, hãy chạy lệnh dưới đây:

    visudo

Nếu bạn đăng nhập bằng tài khoản non-root user với đặc quyền `sudo` đã được cấp, hãy chạy lệnh giống như trên nhưng với prefix `sudo`:

    sudo visudo

Trước đây, `visudo` sẽ mở `/etc/sudoers` bằng vi editor, điều này khiến những người mới bắt đầu, chưa có nhiều kinh nghiệm dễ nhầm lẫn. Do đó, mặc định trên các phiên bản mới của Ubuntu, `visudo` sẽ sử dụng `nano` editor, là một editor mang lại trải nghiệm được thuận tiện hơn và dễ sử dụng hơn.

Sau khi đã mở file `/etc/sudoers`, hãy sử dụng các phim mũi tên để di chuyển con trỏ  trong file và tìm kiếm dòng lệnh nhìn giống như bên dưới:

    root    ALL=(ALL:ALL) ALL

Dưới dòng lệnh trên, hãy thêm các lệnh giống như hình dưới đây. Nếu tên user mới tạo của các bạn khác với tên được dùng trong bài hướng dẫn này, hãy thay đổi `newuser` thành tên user của các bạn để có thể cấp quyền `sudo`:

```
root    ALL=(ALL:ALL) ALL`
newuser ALL=(ALL:ALL) ALL`
```
Thêm dòng lệnh giống như trên sẽ cấp đầy đủ các đặc quyền của sudo cho user đó. Sau khi đã hoàn thành, hãy lưu và đóng file lại bằng phím CTRL + X, theo sau là Y và ENTER để xác nhận.   

<a name="3.3"></a>
## 3.3. Kiểm tra đặc quyền Sudo của User

Sau khi đã thêm cấp quyền `sudo` cho user theo một trong hai cách nói trên, bây giờ bạn có thể thực hiện các lệnh với đặc quyền admin cho user mới tạo của mình.

Khi đăng nhập bằng user mới tạo, các bạn có thể thực hiện các lệnh như thông thường bằng cách nhập các lệnh như:

    some_command

Ngoài ra, cũng có thể thực hiện các lệnh của đặc quyền `sudo` bằng cách prefix sudo cho các lệnh đó như sau:

    sudo some_command

Khi thực hiện lệnh `sudo` như trên, bạn sẽ được yêu cầu phải xác nhận mật khẩu của tài khoản user mà bạn đã hiện tại đang đăng nhập.

Với user mới tạo trên, ở đây Vietnix khuyến khích bạn hãy thử thực hiện lại việc tạo thêm một user mới theo các bước hướng dẫn ở trên bằng tài khoản của user với đặc quyền sudo mới tạo này, nếu như trước đó bạn đã dùng tài khoản của root user.

<a name="3.4"></a>
## 3.4. Xóa User

Trong trường hợp có một user nào đó mà bạn không cần dùng nữa thì nên xóa user đó đi.

Các bạn có thể xóa chính user đi ngoại trừ các files thuộc về user đó, hãy sử dụng lệnh sau nếu là root user:

    deluser newuser 

Tương tự, nếu đăng nhập bằng tài khoản non-root user, hãy thêm prefix sudo cho câu lệnh:

    sudo deluser newuser

Ngoài ra, nếu muốn xóa thư mục home của user sau khi user bị xóa đi, hãy chạy lệnh sau nếu là root user:

deluser --remove-home newuser

Nếu là một non-root user với đặc quyền `sudo`, hãy thêm prefix sudo cho câu lệnh:

    sudo deluser --remove-home newuser

Nếu trước đó, bạn đã cấu hình cho user vừa bị xóa có đặc quyền `sudo`, đừng quên chỉnh sửa lại file cấu hình. Bạn sẽ mở lại file cấu hình `sudo` bằng lệnh `visudo` hoặc `sudo visudo` (nếu là non-root user với đặc quyền sudo), sau đó tìm và xóa dòng lệnh cấp quyền `sudo` cho  user đã được khai báo trước đó.

    visudo

Hoặc:

    sudo visudo

Sau đó:
```
root    ALL=(ALL:ALL) ALL

newuser ALL=(ALL:ALL) ALL   # DELETE THIS LINE
```

Điều này đảm bảo chắc chắn rằng, khi có một user mới thêm vào sẽ không bị trùng tên và tránh gây xung đột cho việc cấp quyền `sudo`.

<a name="4"></a>
# 4. Lời kết

Qua bài hướng dẫn cách thêm và xóa User trên Ubuntu 20.04 này, Vietnix đã hướng dẫn các bạn khá kĩ về việc xử lý thêm và xóa user trên hệ thống Ubuntu 20.04. Hơn nữa là còn cách để quản lý user hiệu quả, để co thể tách riêng biệt các users ra và phân quyền vừa đủ cho các users để hoàn thành các công việc. Cuối cùng, nếu có gì thắc mắc, các bạn hãy để lại bình luận bên dưới nhé.

Nguồn: [vietnix.vn](https://vietnix.vn/them-va-xoa-user-tren-ubuntu-20-04/)