# Hướng dẫn cấu hình và cài đặt Ansible trên Ubuntu 20.04
Là một lập trình viên hay một kỹ sư phần mềm, chắc hẳn sẽ không ít lần gặp khó khăn trong việc deploy, quản lý, vận hành và bảo trì server của mình. Vì thế, mà trong bài hướng dẫn này, hãy cùng với Vietnix tìm hiểu về Ansible, cũng như cách cấu hình và cài đặt Ansible trên Ubuntu 20.04 để giúp hệ thống cấu hình, quản lý và vận hành các server một cách tự động và hiệu quả nhé.

## Mục lục

- [Giới thiệu](#giới-thiệu)
- [Yêu cầu tiên quyết để cài đặt Ansible trên Ubuntu 20.04](#yêu-cầu-tiên-quyết-để-cài-đặt-ansible-trên-ubuntu-2004)
- [Các bước tiến hành](#các-bước-tiến-hành)
    - [Bước 1: Cài đặt Ansible](#bước-1-cài-đặt-ansible)
    - [Bước 2: Cài đặt Inventory File](#bước-2-cài-đặt-inventory-file)
    - [Bước 3: Kiểm tra kết nối](#bước-3-kiểm-tra-kết-nối)
    - [Bước 4: Chạy lệnh Ad-Hoc (Không bắt buộc)](#bước-4-chạy-lệnh-ad-hoc-không-bắt-buộc)
- [Lời kết](#lời-kết)

-------

## Giới thiệu

Các hệ thống quản lý cấu hình server được tạo ra để giúp team admin (quản trị) và vận hành hệ thống có thể hợp lý hóa được quá trình quản lý một lượng lớn các máy chủ. Nhờ đó cho phép chúng ta kiểm soát được nhiều hệ thống server khác nhau một cách tự động bằng cách sử dụng một máy tính làm trung tâm duy nhất.

Hiện nay đã có sẵn rất nhiều các công cụ phổ biến giúp quản lý cấu hình cho hệ thống Linux như Chef và Puppet, nhưng những công cụ này thường phức tạp hơn so với cần thiết để có thể sử dụng. Do đó, Ansible là một thay thế cho những lựa chọn trên, bởi vì với kiến trúc của Ansible không cần phải có những phần mềm đặc biệt để có thể cài đặt được. Thay vào đó sử dụng SSH để thực thi các tác vụ tự động và các file ở định dạng YAML để định nghĩa các chi tiết cần thiết. 

Trong bài này sẽ hướng dẫn bạn cách để cài đặt Ansible trên máy chủ Ubuntu phiên bản 20.04 và tìm hiểu về cách sử dụng cơ bản phần mềm này.

## Yêu cầu tiên quyết để cài đặt Ansible trên Ubuntu 20.04

Đầu tiên các bạn cần phải đảm bảo máy tính của mình có các yêu cầu sau:

- **Ansible Control Node:** Là một máy tính mà chúng ta sẽ sử dụng để kết nối tới và điều khiển Ansible hosts trên SSH. Ansible Control Node có thể là máy tính local của các bạn hoặc có thể là một Server đang chạy Ansible, hãy chắc chắn là các bạn đã có Control Node trên hệ thống Ubuntu 20.04.

- **Quyền user là non-root:** Hãy thiết lập để quyền user của các bạn là non-root. Một điểm cần lưu ý là nếu các bạn sử dụng remote server cho Ansible Control Node của mình, thì cần phải cấu hình thêm phần Firewall với ufw và cho phép các truy cập từ bên ngoài vào trong cấu hình của non-root user, hai điều trên sẽ giúp giữ cho remote server được an toàn.

- Cài đặt SSH key cho user của các bạn.

- Một hoặc nhiều Ansible Hosts: Một Ansible host là máy tính mà Ansible Control Node của các bạn được cấu hình để tự động hóa. Đảm bảo Ansible host của bạn là một remote Ubuntu 20.04 server.

- SSH public key của Ansible Control Node phải được thêm vào `authorized_keys` của user. Nhưng user này có thể là root hoặc một user thông thường với quyền sudo.

Vietnix hiện đang cung cấp nhiều gói dịch vụ VPS tốc độ cao, với cấu hình đa dạng và mức giá phải chăng, giúp bạn có thể chủ động cài đặt hệ điều hành Ubuntu và triển khai Ansible dễ dàng để quản lý và vận hành server một cách hiệu quả nhất.

Đội ngũ tư vấn giàu kinh nghiệm của Vietnix sẽ hỗ trợ bạn trong quá trình lựa chọn gói dịch vụ tối ưu nhất phù hợp với nhu cầu của bạn. Hãy liên hệ với Vietnix ngay hôm nay để được hỗ trợ tốt nhất.

## Các bước tiến hành

### Bước 1: Cài đặt Ansible

Cài đặt phần mềm Ansible trên máy tính như là một Ansible Control Node để có thể sử dụng Ansible để làm nhiệm vụ quản lý server.

Từ Control Node, chạy lệnh sau để bao gồm những official project’s PPA (personal package archive) vào danh sách tài nguyên hệ thống trên máy:

    sudo apt-add-repository ppa:ansible/ansible

Nhấn **ENTER** khi được bảo chấp nhận thêm PPA.

Tiếp theo, làm mới lại package index của hệ thống trên máy để đảm bảo rằng những packages mới của PPA có thể sử dụng được.

    sudo apt update

Sau khi cập nhật, các bạn có thể cài đặt phần mềm Ansible với lệnh sau:

    sudo apt install ansible

Ansible Control Node trên máy giờ đã có được tất cả các phần mềm cần thiết để có thể quản trị được host của các bạn. Tiếp theo sẽ tìm hiểu về cách làm thế nào để thêm host vào Inventory File (kho tài liệu lưu trữ) của Control Node để có thể quản lý chúng.

### Bước 2: Cài đặt Inventory File

Inventory File chứa thông tin về các host mà các bạn sẽ quản lý bằng Ansible. Các bạn có thể include từ một đến hàng nghìn server bên trong Inventory File và các hosts có thể được sắp xếp vào trong các nhóm hoặc nhóm nhỏ hơn. 

Bên cạnh đó, Inventory File cũng thường được sử dụng để thiết lập các biến chỉ hợp lệ cho một vài hosts hoặc nhóm cụ thể, nhằm có thể được sử dụng trong các playbooks và templates. Một vài biến có thể tác động tới cách mà một playbook chạy, ví dụ như biến `ansible_python_interpreter` mà một lát sau chúng ta sẽ tìm hiểu.

Để chỉnh sửa nội dung của Inventory mặc định trên Ansible Control Node của các bạn, hãy mở file `/etc/ansible/hosts` bằng một editor mà các bạn thường dùng:

    sudo nano /etc/ansible/hosts

**Lưu ý:** Dù mặc định Ansible thường sẽ tạo một Inventory File mặc định ở etc/ansible/hosts, nhưng các bạn có thể tự tạo Inventory File ở bất kỳ nơi nào mà các bạn thấy phù hợp với nhu cầu của mình. Trong trường hợp này, các bạn cần cung cấp đường dẫn vào file tự tạo đó bằng tham số -i khi chạy lệnh Ansible và playbooks. Việc sử dụng Inventory File cho mỗi project khác nhau là rất có ích vì sẽ giảm được nguy cơ việc một playbook chạy nhầm trên một nhóm các servers.

Inventory File mặc định được cung cấp bởi Ansible chứa một số các ví dụ mà các bạn có thể sử dụng để tham khảo khi tự viết hay thiết lập một Inventory File. Sau đây, là một ví dụ định nghĩa một nhóm có tên là `[servers]` với ba server khác nhau ở bên trong, mỗi server được định danh bằng tên server1, server 2 và server3. Hãy thay đổi địa chỉ IP được in đậm thành địa chỉ IP của Ansible hosts của các bạn.

```
[servers]
server 1 ansible_host=203.0.113.111
server 2 ansible_host=203.0.113.111
server 3 ansible_host=203.0.113.111

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

Ở đây, một subgroups có tên là `[all:vars]` đã thiết lập tham số của host `ansible_python_interpreter` sẽ được xem là hợp lệ cho tất cả các host nào mà đã include file Inventory này. Tham số này giúp đảm bảo remote server sử dụng đường dẫn `/usr/bin/python3` (Python 3) để thực thi thay vì `/usr/bin/python` (Python 2.7), phiên bản hiện không có mặt trên các phiên bản của Ubuntu.

Sau khi đã thiết lập xong, hãy lưu và đóng file lại bằng cách nhấn **CTRL+X**, sau đó *Y* và **ENTER** để xác nhận thay đổi.

Để kiểm tra lại file Inventory, các bạn có thể nhập lệnh:

    ansible-inventory --list -y

Các bạn sẽ thấy output tương tự như thế này, nhưng sẽ chứa thông tin của server của chính các bạn như đã được định nghĩa.

```
Output:
all:
  chidlren:
   servers
     hosts:
       server1:
           ansible_host: 203.0.113.111
           ansible_python_interpreter: /usr/bin/python3
       server2:
           ansible_host: 203.0.113.112
           ansible_python_interpreter: /usr/bin/python3
       server3:
           ansible_host: 203.0.113.113
           ansible_python_interpreter: /usr/bin/python3
   ungrouped: {}
```

Đến đây, bạn đã cấu hình xong file Inventory. Bây giờ đã có thể kiểm tra kết nối đến các Ansible hosts của các bạn.

### Bước 3: Kiểm tra kết nối

Sau khi đã thiết lập file Inventory chứa các servers, chúng ta sẽ kiểm tra kết nối của Ansible đến các server đó và chạy các lệnh thông qua SSH.

Trong bài hướng dẫn này, chúng ta sử dụng tài khoản root của Ubuntu bởi vì đó thường là tài khoản có sẵn trên bất kì một servers nào mới được tạo. Nếu Ansible hosts của các bạn đã có sẵn một sudo user thì các bạn nên sử dụng tài khoản đó.

Sử dụng tham số `-u` để chỉ định user cho remote system. Nếu không chỉ định user, Ansible sẽ kết nối bằng quyền của user hiện trên hệ thống Control Node.

Trên máy tính local của các bạn hoặc Ansible Control Node, chạy lệnh sau:

    ansible all -m ping -u root

Lệnh này sẽ sử dụng ping module được xây dựng sẵn trong Ansible để chạy kiểm tra kết nối trên tất cả các node từ Inventory mặc định của các bạn và sẽ kết nối như quyền root. ping module sẽ kiểm tra các thông tin sau:

- Có thể truy cập đến hosts được hay không.

- Các bạn có chứng chỉ SSH hợp lệ hay không.

- Host có thể chạy các modules trong Ansible bằng Python hay không.

Các sẽ nhận được output tương tự như này:

```
Output:
server1 | SUCCESS => {
   "changed": false,
   "ping": "pong"
}
server2 | SUCCESS => {
   "changed": false,
   "ping": "pong"
}
server3 | SUCCESS => {
   "changed": false,
   "ping": "pong"
}
```

Nếu đây là lần đầu tiên kết nối đến các servers này thông qua SSH, các bạn sẽ được yêu cầu xác thực tính authen của các host đang kết nối bằng Ansible. Khi được yêu cầu, hãy nhập `yes` và sau đó nhấn **ENTER** để xác nhận.

Khi các bạn nhận được “pong” phản hồi ngược lại từ một host, điều đó có nghĩ là bạn đã có thể chạy các lệnh Ansible và playbooks trên server đó.

**Lưu ý:** Nếu các bạn không nhận được phản hồi trở lại đã kết nối thành công từ server, hãy kiểm tra lại các credential của mình và thử các lệnh để kết nối khác của Ansible.

### Bước 4: Chạy lệnh Ad-Hoc (Không bắt buộc)

Sau khi xác nhận rằng Ansible Control Node đã có thể tương tác với các host, các bạn có thể bắt đầu chạy lệnh ad-hoc và playbooks trên những server đó.

Bất kì command nào mà thường có thể thực thi được trên remote server qua SSH thì có thể chạy được với Ansible trên những servers được chỉ định trong file Inventory. Ví dụ, các bạn có thể kiểm tra được dung lượng ổ cứng trên tất cả các servers bằng lệnh:

    ansible all -a "df -h" -u root

```
Output:

server1 | CHANGED | rc = 0 >>
Filesystem   Size   Used   Avail   Use%   Mounted on
udev         3.9G   0      3.9G    0%     /dev
tmpfs        798M   624K   798M    1%     /run
/dev/vda1    155G   2.3G   153G    2%     /
tmpfs        3.9G   0      3.9G    0%     /dev/shm
tmpfs        5.0M   0      5.0M    0%     /run/lock
tmpfs        3.9G   0      3.9G    0%     /sys/fs/cgroup
/dev/vda15   105M   3.6M   101M    4%     /boot/efi
tmpfs        798M   0      798M    0%     /run/user/0
....
```

Dòng lệnh được bôi đậm "df -h" có thể được thay thế bằng bất kỳ lệnh nào mà các bạn muốn.

Các bạn cũng có thể thực thi các modules của Ansible thông qua các lệnh ad-hoc, tương tự với cái đã làm trước đó với ping module để kiểm tra kết nối. Ví dụ, ở đây chúng ta sẽ sử dụng `apt` module để cài đặt phiên bản mới nhất của `vim` trên tất cả các servers trong file Inventory:

    ansible all -m apt -a "name=vim state=latest" -u root

Các bạn cũng có thể nhắm mục tiêu các host cũng như các groups và subgroups khi chạy các lệnh Ansible. Chẳng hạn như sau đây là cách để chúng ta kiểm tra `uptime` của mỗi host trong `servers` (chỉ thị [servers] mà ta đã thiết lập trong file Inventory ) group.

    ansible servers -a "uptime" -u root

Chúng ta cũng có thể chỉ định nhiều host bằng cách tách chúng ra bằng dấu hai chấm.

    ansible server1:server2 -m ping -u root


## Lời kết

Qua bài viết trên, bạn đã biết cách cài đặt Ansible và file Inventory để thực thi các lệnh ad-hoc từ một Ansible Control Node. Khi đã xác nhận là có thể kết nối đến được servers của mình từ Ansible, bạn có thể thực thi các lệnh bất kỳ hoặc playbook mà mình muốn trên các host đó. Qua đó đã nắm cơ bản cách sử dụng Ansible và có thể áp dụng vào trong những project thực tế mà các bạn đang phát triển. Nếu thấy bài viết hướng dẫn về cấu hình và cài đặt Ansible trên Ubuntu 20.04 hay và hữu ích, các bạn hãy chia sẻ với mọi người nhé.

Nguồn: [Vietnix.vn](https://vietnix.vn/cai-dat-ansible-tren-ubuntu-20-04/)

## Tham khảo bài viết mới

1. [Cách dời thư mục dữ liệu MySQL tới vị trí mới trên Ubuntu 20.04](https://github.com/vietnix-vn/Chu-de-Ubuntu/blob/Vietnix/C%C3%A1ch%20d%E1%BB%9Di%20th%C6%B0%20m%E1%BB%A5c%20d%E1%BB%AF%20li%E1%BB%87u%20MySQL%20t%E1%BB%9Bi%20v%E1%BB%8B%20tr%C3%AD%20m%E1%BB%9Bi%20tr%C3%AAn%20Ubuntu%2020.04.md)

2. [Hướng dẫn cách thêm và xóa User trên Ubuntu 20.04](https://github.com/vietnix-vn/Chu-de-Ubuntu/blob/Vietnix/H%C6%B0%E1%BB%9Bng%20d%E1%BA%ABn%20c%C3%A1ch%20th%C3%AAm%20v%C3%A0%20x%C3%B3a%20User%20tr%C3%AAn%20Ubuntu%2020.04.md)

3. [Hướng dẫn cài đặt Python 3 trên Ubuntu 20.04 chi tiết nhất](https://github.com/vietnix-vn/Chu-de-Ubuntu/blob/Vietnix/H%C6%B0%E1%BB%9Bng%20d%E1%BA%ABn%20c%C3%A0i%20%C4%91%E1%BA%B7t%20Python%203%20tr%C3%AAn%20Ubuntu%2020.04%20chi%20ti%E1%BA%BFt%20nh%E1%BA%A5t.md)