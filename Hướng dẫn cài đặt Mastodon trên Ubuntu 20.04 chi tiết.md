# Hướng dẫn cài đặt Mastodon trên Ubuntu 20.04 chi tiết

Mastodon là một ứng dụng mạng xã hội microblogging sử dụng mô hình phi tập trung, có tính chất open-source và có thể được triển khai bởi bất kỳ ai trên bất kỳ server nào. Mỗi phiên bản có các thiết lập và chính sách riêng. Trong bài viết này sẽ hướng dẫn bạn cách cài đặt Mastodon trên Ubuntu 20.04.

## Mục lục

- [Điều kiện để cài đặt Mastodon trên Ubuntu 20.04](#1)

- [Bước 1: Tạo các User và các role PostgreSQL trong Mastodon](#2)

- [Bước 2: Cài đặt các dependency và clone Mastodon](#3)
    - [Tiến hành clone Mastodon](#3.1)

    - [Cài đặt Ruby và các dependency Ruby của Mastodon](#3.2)

    - [Cài đặt các dependency JavaScript của Mastodon](#3.3)

- [Bước 3: Tạo file cấu hình Mastodon với thiết lập tương tác (Interactive Setup)](#4)
    - [Cấu hình các Database](#4.1)

    - [Cấu hình vùng nhớ](#4.2)

    - [Cấu hình outbound cho email](#4.3)

    - [Hoàn thiện cấu hình của bạn](#4.4)

- [Bước 4: Cấu hình Nginx cho Mastodon với Certbot và Let's Encrypt](#5)

- [Bước 5: Thiết lập Dịch vụ Mastodon](#6)

- [Lời kết](#7)

-------
<a name="1"></a>
## Điều kiện để cài đặt Mastodon trên Ubuntu 20.04

Trước khi đi vào cài đặt, hãy chắc rằng bạn đã đáp ứng những điều kiện sau:

- Một máy Ubuntu server 20.04 có firewall và bạn cần một tài khoản người dùng có quyền `sudo`. 

- Bạn đã cài đặt PostgreSQL, Node.js phiên bản 16.xx trở lên và Nginx.

- Một tên miền (domain name) và một DNS với tên miền trỏ đến địa chỉ IP public của server.

Để triển khai Mastodon, bạn cần phải sở hữu một máy chủ để cài đặt ứng dụng này. Các gói VPS tốc độ cao của Vietnix có thể giúp bạn triển khai Mastodon một cách dễ dàng và thuận tiện. Với VPS của Vietnix, bạn có thể tự cài đặt hệ điều hành Ubuntu và triển khai Mastodon theo nhu cầu của mình. Liên hệ ngay với Vietnix để được tư vấn chi tiết hơn.

<a name="2"></a>
## Bước 1: Tạo các User và các role PostgreSQL trong Mastodon

Mastodon mặc định rằng server của bạn sẽ được quản lý bằng cách sử dụng user có tên là `mastodon`. Điều này hiện được mã hóa cứng trong nhiều phần của Mastodon. Bài này sẽ tuân theo đề xuất là tạo và sử dụng user tên là `mastodon`, ở cả cấp độ hệ thống và là một role trong PostgreSQL.

Trước hết thì bạn cần một tài khoản user trong quá trình thiết lập server ban đầu, sau đó bạn sử dụng lệnh `adduser` để tạo người dùng `mastodon`:

    sudo adduser mastodon

Tiếp theo, thêm người dùng Mastodon của bạn vào `sudo` group để cấp quyền admin:

    sudo usermod -aG sudo mastodon

Với user hệ thống tên là `mastodon` đã được tạo ra, tiếp theo hãy tạo một role phù hợp trong cơ sở dữ liệu của bạn. PostgreSQL sử dụng xác thực ngang hàng, liên kết các role đã tạo với user hệ thống phù hợp. Tạo một user có tên phù hợp với `mastodon`:

    sudo -u postgres createuser --interactive

Cờ `interactive` sẽ hiển thị lời nhắc sau nơi bạn có thể chọn tên cho role mới của mình, ở đây là `mastodon`. Tiếp theo là nhập `y` để đặt role mới của bạn thành superuser:

```
Output
Enter name of role to add: mastodon
Shall the new role be a superuser? (y/n) y
```

Bây giờ bạn có thể đăng nhập vào user `mastodon` mới được tạo của mình:

    su - mastodon

Đối với phần còn lại của bài viết này, bạn sẽ chạy tất cả các lệnh với user là `mastodon`.

<a name="3"></a>
## Bước 2: Cài đặt các dependency và clone Mastodon

Phần lớn cấu hình của Mastodon được xử lý thông qua thiết lập tương tác (interactive setup) mà bạn sẽ thực hiện ở bước sau. Trước tiên, bạn cần cài đặt các dependency cần thiết. Mastodon là một dự án chủ yếu dựa trên Ruby và Node.js khi hoạt động trong môi trường Linux, dẫn đến việc cần phải có ba chương trình quản lý gói: APT ở mức hệ thống của Linux, Bundler cho Ruby gems và gói Yarn dành cho Node.js. Để cài đặt từ source code, bạn cần clone repository của Mastodon. Quá trình sẽ diễn ra theo trình tự sau:

- Cài đặt dependency APT.

- Clone Mastodon.

- Cài đặt Ruby và các dependency liên quan của Mastodon.

- Cài đặt các dependency của Node.js.

Bước đầu tiên là cài đặt các dependency thông qua `apt`, bao gồm: Redis làm cơ sở dữ liệu chính, Certbot để xử lý mã hóa TLS/SSL và cuối cùng là các thư viện khác để tiến hành build, compile và xử lý image. Để cài đặt những thứ này, hãy sử dụng `apt update` trước:

    sudo apt update

Sau đó cài đặt các gói với `apt install`:

```
sudo apt install \
  imagemagick ffmpeg libpq-dev libxml2-dev libxslt1-dev \
  libprotobuf-dev protobuf-compiler pkg-config \
  redis-server redis-tools \
  certbot python3-certbot-nginx libidn11-dev libicu-dev libjemalloc-dev
```

<a name="3.1"></a>
### Tiến hành clone Mastodon

Bây giờ server của bạn đã sẵn sàng clone Mastodon từ repository chính thức. Bài này sẽ sử dụng phiên bản v4.0.2 của Mastodon.

Đầu tiên, tiến hành clone repository của Mastodon tới server của bạn và cho vào một thư mục mới có tên live:

    git clone https://github.com/mastodon/mastodon.git live

Quy ước đặt tên thư mục này được sử dụng trong toàn bộ tài liệu của Mastodon và được khuyến khích sử dụng để đảm bảo tính nhất quán. Tiếp theo, di chuyển tới thư mục bạn đã tạo:

    cd live

Bây giờ bạn có thể sử dụng `git` để check out phiên bản mới nhất v4.0.2:

    git checkout v4.0.2

<a name="3.2"></a>
### Cài đặt Ruby và các dependency Ruby của Mastodon

Với mã nguồn Mastodon sẵn có, bây giờ bạn có thể tiến hành cài đặt Ruby và dependency Ruby của Mastodon. Bạn sẽ cài đặt rbenv, công cụ quản lý phiên bản mà bạn sẽ sử dụng để cài đặt ngôn ngữ lập trình Ruby. Vì `rbenv` được cài đặt ở cấp độ user thường chứ không phải cấp độ hệ thống, cho nên bước này phải thực hiện tại đây sau khi bạn tạo và chuyển sang user `mastodon` của mình. Tiếp theo, bạn sẽ cài đặt Ruby on Rails bằng `rbenv`.

Các dependency Ruby ​​được quản lý bởi Bundler. Bundler cài đặt tất cả các gems Ruby theo yêu cầu của Mastodon, nhưng trước tiên bạn cần phải cấu hình trước để có thể cài đặt. 

Dùng lệnh `bundle config` từ Bundler để giao tiếp với hệ thống cấu hình và đặt `deployment thành true`:

    bundle config deployment 'true'

Tiếp theo, sử dụng `without` để đảm bảo các dependency cho quá trình phát triển và thử nghiệm sẽ không được cài đặt:

    bundle config without 'development test'

Bây giờ bạn có thể cài đặt các dependency Ruby của mình với lệnh `install`. Quá trình cài đặt này có thể mất một chút thời gian và một vài lần tạm dừng:

    bundle install

```
Output
Fetching gem metadata from https://rubygems.org/.........
Fetching https://github.com/ClearlyClaire/webpush.git
Fetching rake 13.0.6
Installing rake 13.0.6
Fetching concurrent-ruby 1.1.10
Installing concurrent-ruby 1.1.10
. . .
```

Sau khi quá trình này kết thúc, có rất nhiều output trông có vẻ như cần được xử lý. Tuy nhiên, bạn có thể làm tiếp các bước khác một cách an toàn mà không cần làm thêm hành động nào.

<a name="3.3"></a>
### Cài đặt các dependency JavaScript của Mastodon:

Khi các dependency Ruby của Mastodon đã cài đặt xong, bạn có thể chuyển sang phần cài đặt dependency của JavaScript. Mastodon dựa vào `yarn` để quản lý và cài đặt các gói JavaScript chứ không phải `npm`. Với Node.js phiên bản 16.10 trở lên thì theo mặc định `yarn` có sẵn trong corepack.

Để có quyền truy cập vào `yarn`, hãy enable `corepack` đi kèm của Node.js:

    sudo corepack enable

Yarn đã có lịch sử phát triển khác nhau giữa phiên bản 1.0, còn được gọi là `classic` và phiên bản 2.0. Mastodon yêu cầu phiên bản `yarn classic`, bạn có thể thực hiện bằng lệnh:

    yarn set version classic

Bây giờ, bạn có thể cài đặt các dependency JavaScript của Mastodon bằng cách sử dụng `yarn install`. Sẽ có flag bổ sung vào để đảm bảo rằng tệp `yarn.lock` không được tạo ra:

    yarn install --pure-lockfile

```
Output
yarn install v1.22.19
[1/6] Validating package.json...
[2/6] Resolving packages...
[3/6] Fetching packages...
[4/6] Linking dependencies...
warning Workspaces can only be enabled in private projects.
[5/6] Building fresh packages...
[6/6] Cleaning modules...
Done in 50.84s.
```

Lúc này bạn đã sẵn sàng để tạo một tệp cấu hình Mastodon tùy chỉnh cho server của mình.

<a name="4"></a>
## Bước 3: Tạo file cấu hình Mastodon với thiết lập tương tác (Interactive Setup)

Mastodon thay đổi file `.env` để biến đổi file này thành file cấu hình. Có sẵn một file mẫu bạn có thể xem tại `~/live/.env.production.sample`, nhưng thay vì sao chép file đó, bạn sẽ sử dụng quy trình interactive setup. Sau khi hoàn thành, một file custom sẽ được tạo ra tại `~/live/.env.production`. Quá trình interactive setup sẽ bao gồm các thông tin cơ bản của server, thiết lập kết nối cho PostgreSQL và Redis, thiết lập lưu trữ đám mây và xử lý email, biên dịch nội dung CSS và JavaScript cũng như tạo tài khoản quản trị viên trong Mastodon.

Trước khi bắt đầu quy trình thiết lập gồm nhiều bước, hãy đảm bảo rằng firewall của bạn cho phép lưu lượng SMTP đi qua port phù hợp. Port SMTP mặc định cho các kết nối secure TLS là `port 587` và điều này sẽ được giải thích trong phần sau. Bây giờ, ta sẽ tiến hành cho phép truy cập vào cổng này trong firewall của bạn bằng lệnh:

    sudo ufw allow 587

Để bắt đầu interactive setup, hãy sử dụng lệnh `bundle exec rake` để thực thi các thiết lập của Mastodon, đồng thời đặt biến môi trường cho product:

    RAILS_ENV=production bundle exec rake mastodon:setup

Nhập vào tên miền của bạn. Lưu ý rằng rất khó để thay đổi điều này sau khi thiết lập, vì vậy hãy kiểm tra kỹ thông tin nhập vào của bạn để đảm bảo độ chính xác:

```
Output
Your instance is identified by its domain name. Changing it afterward will break things.
Domain name: your_domain
```

Mastodon có một mode đó là single user mode, trong đó trong đó việc đăng ký bị vô hiệu hóa. Đối với server được thiết lập với mục đích tạo không gian chia sẻ với những người khác, bạn không nên bật chế độ single user mode lên:

```
Output
Single user mode disables registrations and redirects the landing page to your public profile.
Do you want to enable single user mode? No
```

Bạn sẽ trả lời no cho lời nhắc về việc sử dụng Docker, vì hướng dẫn này đề cập đến cách cài đặt thông thường trên server vật lý:

```
Output
Are you using Docker to run Mastodon? no
```

<a name="4.1"></a>
### Cấu hình các Database

Tất cả các thiết lập cho PostgreSQL có thể giữ nguyên ở dạng mặc định bằng cách nhấn Enter cho mỗi lời nhắc. Cơ sở dữ liệu của bạn không thực hiện xác thực bằng mật khẩu, mà thay vào đó là sử dụng xác thực ngang hàng, điều này chúng ta sẽ thảo luận sau. Hiện tại, phần password có thể để trống:

```
Output
PostgreSQL host: /var/run/postgresql
PostgreSQL port: 5432
Name of PostgreSQL database: mastodon_production
Name of PostgreSQL user: mastodon
Password of PostgreSQL user:
Database configuration works! 
```

Ta cũng có thể cài đặt Redis với các giá trị mặc định và với phần password được để trống. Bạn sẽ không thiết lập mật khẩu vì cơ sở dữ liệu này chỉ có thể truy cập cục bộ trên server này. Bạn có thể nhấn Enter cho mỗi lời nhắc:

```
Output
Redis host: localhost
Redis port: 6379
Redis password:
Redis configuration works!
```

<a name="4.2"></a>
### Cấu hình vùng nhớ

Tiếp theo, bạn sẽ thiết lập về việc file lên bộ nhớ cloud. Bộ nhớ Mastodon có thể được thiết lập bằng bộ nhớ cục bộ hoặc bộ nhớ cloud thông qua một số nhà cung cấp. Nếu bạn muốn sử dụng bộ nhớ cục bộ hoặc nếu bạn chưa có sẵn giải pháp lưu trữ đám mây ngay lập tức, thì hãy trả lời `no` khi đến bước cài đặt về việc upload file.

Tuy nhiên, đây không phải là giải pháp lưu trữ lâu dài và bền vững. Để phục vụ cho mục đích là hướng tới các sản phẩm production thì giải pháp lưu trữ đối tượng (object storage solution) mới là giải pháp được khuyến khích sử dụng. Mastodon có hỗ trợ một số giải pháp lưu trữ đối tượng phổ biến như MinIO và các giải pháp S3-compatible khác. Như vậy, các bước thiết lập để upload file là:

```
Do you want to store uploaded files on the cloud? yes
Provider your_provider_spaces
Space name: your_spaces_name
Space region: your_spaces_region
Space endpoint: your_spaces_endpoint
Space access key: your_spaces_access_key
Space secret key: your_spaces_secret_key
Do you want to access the uploaded files from your own domain? No
```

Bạn cũng có thể truy cập các file được tải lên Mastodon của mình bằng các link trên domain thay vì sử dụng các link trực tiếp đến bộ nhớ cloud. Bạn có thể chọn tùy chọn này nếu muốn, trong bài này sẽ lựa chọn `no`. Bạn có thể quay lại sau và chỉnh sửa file cấu hình đã tạo nếu bạn thay đổi ý định về việc lưu trữ.

<a name="4.3"></a>
### Cấu hình outbound cho email

Mastodon yêu cầu quyền truy cập vào email server SMTP như một phương thức để gửi email. Bạn nên sử dụng nhà cung cấp dịch vụ email bên ngoài thay vì chạy dịch vụ của riêng bạn. Mastodon thì sử dụng Mailgun. Để có thể duy trì một email server an toàn và đáng tin cậy là một công việc khó khăn, vì vậy bạn nên dựa vào nhà cung cấp dịch vụ email bên thứ ba bên ngoài. Tuy nhiên, bạn nên lưu ý rằng hầu hết các nhà cung cấp dịch vụ email bên thứ ba đều tính phí dựa trên chất lượng của dịch vụ, bạn hãy cân nhắc dựa vào tình hình thực tế.

**Lưu ý:** Mặc dù bài viết này có thể được hoàn thành mà không cần SMTP ngay lập tức, bằng cách để trống các trường thông tin xác thực hoặc để mặc định, làm như thế sẽ cản trở chức năng đầy đủ của Mastodon và không được khuyến khích. Bạn có thể tùy chọn thiết lập tài khoản với nhà cung cấp email và thêm thông tin đăng nhập vào file cấu hình của mình sau này. Tuy nhiên, điều này có nghĩa là email của Mastodon sẽ không được gửi cho đến khi bạn thực hiện việc này và một số chức năng như theo dõi các tài khoản khác sẽ không khả dụng.

```
Output
Do you want to send e-mails from localhost? No
```

Với tất cả các nhà cung cấp dịch vụ email, bạn phải tạo và cấu hình tài khoản. Sự khác nhau thì tùy thuộc vào nhà cung cấp. Rất có thể sau khi đăng ký, bạn cần xác minh miền của mình với nhà cung cấp để cho phép gửi email trong quá trình liên quan đến việc thêm DNS record vào domain của bạn. Ngoài ra, bạn cần lấy hoặc tạo khóa API làm thông tin xác thực để xác thực an toàn.

Hướng dẫn này sử dụng Mailgun làm ví dụ, mặc dù các lựa chọn nhà cung cấp dịch vụ email đều được. Mailgun thì sẽ tự động tạo thông tin đăng nhập SMTP cho các domain được thêm vào. Một lần nữa, điều này sẽ khác nhau tùy thuộc vào dịch vụ email mà bạn sử dụng và bạn nên kiểm tra tài liệu tương ứng của nhà cung cấp dịch vụ mail:

```
Output
SMTP server: smtp.mailgun.org
SMTP port: 587
SMTP username: your_smtp_username
SMTP password: your_smtp_password
SMTP authentication: plain
SMTP OpenSSL verify mode: peer
Enable STARTTLS: auto
E-mail address to send e-mails "from": Mastodon <notifications@your_domain>
```

Bài viết này sẽ để giá trị của SMTP authentication là `plain`, các tùy chọn thông thường cho SMTP authentication được các nhà cung cấp dịch vụ email hỗ trợ bao gồm `plain` và `login`, cả hai đều được mã hóa Base64. Nếu chỉ sử dụng mã hóa Base64 thì việc bảo mật sẽ chưa đầy đủ. 

Vì thế, điều quan trọng là gửi yêu cầu tới nhà cung cấp dịch vụ email của bạn thông qua kết nối TLS/SSL secure. Kết nối SMTP trên port 587 là mặc định cho kết nối TLS với nhà cung cấp dịch vụ email. Mặc dù lựa chọn này an toàn khi TLS được bảo mật đúng cách, nhưng việc tham khảo tài liệu chính thức của nhà cung cấp dịch vụ email của bạn sẽ cung cấp cho bạn câu trả lời chi tiết hơn và cụ thể hơn về cách thiết lập với các dịch vụ SMTP của họ một cách an toàn.

Gửi thử một email để kiểm tra và đảm bảo rằng thông tin đăng nhập SMTP của bạn đang hoạt động, sau đó chỉ định email người nhận mà bạn có quyền truy cập, chẳng hạn như email cá nhân của bạn:

```
Output
Send a test e-mail with this configuration right now? yes
```

Email test này có thể mất một lúc để đến, tùy thuộc vào dịch vụ email của bạn. Hãy nhớ rằng bạn có thể quay lại sau để thay đổi thông tin đăng nhập SMTP trong tệp cấu hình Mastodon đã tạo của mình.

<a name="4.4"></a>
### Hoàn thiện cấu hình của bạn

Tiếp theo, bạn được thông báo về việc lưu cấu hình của mình. Mastodon sau đó sẽ tạo cơ sở dữ liệu của bạn:

```
Output
This configuration will be written to .env.production
Save configuration? Yes

Now that configuration is saved, the database schema must be loaded.
If the database already exists, this will erase its contents.
Prepare the database now? Yes
Running `RAILS_ENV=production rails db:setup` ...

Created database 'mastodon_production'
Done!
```

Sau đó, bạn được nhắc biên dịch nội dung CSS và JavaScript của Mastodon. Sẽ xuất hiện lưu ý cảnh báo về thời gian và mức tiêu thụ tài nguyên:

```
Output
The final step is compiling CSS/JS assets.
This may take a while and consume a lot of RAM.
Compile the assets now? Yes
Running `RAILS_ENV=production rails assets:precompile` ...

yarn install v1.22.19
[1/6] Validating package.json...
[2/6] Resolving packages...
[3/6] Fetching packages...
[4/6] Linking dependencies...
. . .

All done! You can now power on the Mastodon server 
```

Server Mastodon của bạn hiện đã sẵn sàng, nhưng sẽ chưa chạy. Có một thông báo khác để tạo tài khoản quản trị viên để đăng nhập vào server Mastodon của bạn:

```
Output
Do you want to create an admin user straight away? Yes
Username: mastodon
E-mail: your_email
You can login with the password: your_generated_password
You can change your password once you login.
```

**Lưu ý:** Bạn sẽ không thể đặt mật khẩu cho tài khoản quản trị viên của mình ngay bây giờ. Bạn sẽ được cung cấp một mật khẩu và bạn nên ghi chú ở nơi khác. Khi bạn có quyền truy cập vào giao diện người dùng quản trị, bạn sẽ có thể thay đổi mật khẩu của mình.

Mở file cấu hình Mastodon đã tạo của bạn để xác minh nội dung bằng `nano` hoặc text editor ưa thích của bạn:

    nano ~/live/.env.production

Nội dung sẽ tương tự như sau:

```
# Generated with mastodon:setup on 2022-11-22 18:22:25 UTC

# Some variables in this file will be interpreted differently whether you are
# using docker-compose or not.

LOCAL_DOMAIN=your_domain
SINGLE_USER_MODE=false
SECRET_KEY_BASE=your_generated_key_base
OTP_SECRET=your_generated_secret
VAPID_PRIVATE_KEY=your_generated_private_key
VAPID_PUBLIC_KEY=your_generated_public_key
DB_HOST=/var/run/postgresql
DB_PORT=5432
DB_NAME=mastodon_production
DB_USER=mastodon
DB_PASS=
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=
S3_ENABLED=true
S3_PROTOCOL=https
S3_BUCKET=your_spaces_name
S3_REGION=your_spaces_region
S3_HOSTNAME=your_spaces_endpoint
S3_ENDPOINT=https://your_spaces_endpoint
AWS_ACCESS_KEY_ID=your_spaces_access_key
AWS_SECRET_ACCESS_KEY=your_spaces_secret_key
SMTP_SERVER=smtp.mailgun.org
SMTP_PORT=587
SMTP_LOGIN=your_smtp_username
SMTP_PASSWORD=your_smtp_password
SMTP_AUTH_METHOD=plain
SMTP_OPENSSL_VERIFY_MODE=peer
SMTP_ENABLE_STARTTLS=auto
SMTP_FROM_ADDRESS='Mastodon <notifications@test.do-community.com>'
```

Bạn có thể chỉnh sửa file này theo cách thủ công sau quá trình interactive setup. Khi bạn đã kiểm tra xong, hãy lưu và đóng tệp. Nếu bạn đang sử dụng nano, bạn có thể thực hiện việc này bằng **CTRL + X**, tiếp theo là nhấn **Y** rồi **Enter**.

<a name="5"></a>
## Bước 4: Cấu hình Nginx cho Mastodon với Certbot và Let's Encrypt

Server Mastodon của bạn hiện chỉ có thể truy cập local. Để cho phép truy cập qua internet, bạn cần expose bằng reverse proxy. Nginx được khuyến nghị để làm reverse proxy cho ứng dụng Mastodon của bạn.

Repository của Mastodon đã có sẵn file cấu hình Nginx mặc định mà bạn có thể sử dụng. Tuy nhiên, bạn cần thực hiện một số thay đổi để khớp file cấu hình đó với miền của riêng bạn và bảo mật file bằng Certbot và Let's Encrypt. 

Mặc dù file Nginx do Mastodon cung cấp được điền đầy đủ các block và các lệnh cần thiết để triển khai đầy đủ, việc cài đặt SSL/TLS mặc định sẽ ngăn chặn một số thao tác với Certbot. Bước này sẽ thực hiện chỉnh sửa file cấu hình Nginx 2 lần để vượt qua giới hạn này, trước và sau khi sử dụng Certbot.

Trước tiên, hãy điều chỉnh cài đặt firewall của bạn để cho phép lưu lượng HTTP và HTTPS đến server Nginx của bạn:

    sudo ufw allow 'Nginx Full'

Điều này là cần thiết để người dùng truy cập trang Mastodon của bạn, nhưng cũng cần thiết để Certbot thực hiện các xác minh cần thiết cho chứng chỉ của bạn. Tiếp theo, sao chép cấu hình Nginx được cung cấp từ repository Mastodon:

    sudo cp /home/mastodon/live/dist/nginx.conf /etc/nginx/sites-available/mastodon

Nginx sử dụng một phương pháp phổ biến được gọi là symbolic links hoặc symlink để theo dõi server block nào của bạn được bật. Tạo một symlink cũng giống như tạo shortcut trên đĩa, để sau này bạn có thể xóa shortcut khỏi thư mục `sites-enabled` trong khi vẫn giữ server block trong `sites-available` nếu bạn muốn bật nó. Tạo một link với file mới tạo của bạn:

    sudo ln -s /etc/nginx/sites-available/mastodon /etc/nginx/sites-enabled/mastodon

Bây giờ Nginx sẽ biết các cài đặt trong file cấu hình này nên được sử dụng để phục vụ trang web Mastodon của bạn. Tuy nhiên, bạn chưa thực hiện các thay đổi cần thiết đối với cấu hình Nginx của mình. Giờ hãy mở file cấu hình đã sao chép của bạn:

    sudo nano /etc/nginx/sites-enabled/mastodon

Bạn cần đảm bảo rằng lệnh `server_iname` trỏ tới domain của bạn và trỏ tới phiên bản `www`. của bạn. Có hai loại `server_name`, một là server block lắng nghe trên port 80 cho HTTP và thứ hai là block lắng nghe trên cổng 443 cho HTTPS. Đảm bảo rằng cả hai lệnh `server_name` đều trỏ đến domain của bạn.

Ngoài ra, bạn cần comment các dòng lệnh `listen` trong server block thứ hai bằng cách thêm ký hiệu # vào đầu dòng. Hiện tại chứng chỉ SSL của bạn chưa được thiết lập, việc có tham số `ssl` cho lệnh `listen` này sẽ khiến Nginx phát hiện cấu hình này là không hợp lệ. Điều này có nghĩa là Certbot sẽ không tạo chứng chỉ cho bạn. Để khắc phục điều này, hãy tạm thời comment lệnh này và kích hoạt lại nó sau khi bạn tạo chứng chỉ của mình:

```
. . .
server {
  listen 80;
  listen [::]:80;
  server_name your_domain www.your_domain;
  root /home/mastodon/live/public;
  location /.well-known/acme-challenge/ { allow all; }
  location / { return 301 https://$host$request_uri; }
}

server {
   # listen 443 ssl http2;
   # listen [::]:443 ssl http2;
  server_name your_domain www.your_domain;
. . .
```

Khi bạn hoàn thành, lưu và đóng file. Bây giờ bạn đã sẵn sàng để tạo chứng chỉ của mình để truy cập SSL/TLS một cách an toàn. Bởi vì bạn đang sử dụng file cấu hình Nginx mặc định của Mastodon, sau đó Certbot của bạn được gọi bằng lệnh `certonly`. Điều này cho phép bạn có thể tự thiết lập file cấu hình. Mặt khác, Certbot sẽ đưa ra giả định về môi trường Nginx của bạn không tương thích với mastodon:

    sudo certbot certonly --nginx -d your_domain -d www.your_domain

```
Output
. . .
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/your_domain/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/your_domain/privkey.pem
. . .
```

Bạn hãy ghi chú lại đường dẫn của chứng chỉ và các tệp quan trọng. Bạn sẽ cần thêm đường dẫn đó vào file cấu hình. Mở lại file cấu hình của bạn để tiến hành chỉnh sửa:

    sudo nano /etc/nginx/sites-enabled/mastodon

Bây giờ bạn có thể bỏ comment cho các lệnh `listen` trước đây cho cổng 443. Trước đây, các dòng này sẽ gây ra lỗi do không có chứng chỉ. Với chứng chỉ mới được tạo của bạn thông qua Certbot, các dòng này đã hợp lệ.

Ngoài ra, gỡ comment cho các lệnh `ssl_certificate` và `ssl_certificate_key` trong cùng một khối. Đảm bảo rằng nó phù hợp với các đường dẫn file với các chứng chỉ được tạo trước đó bởi Certbot:

```
. . .
server {
   listen 443 ssl http2;
   listen [::]:443 ssl http2;
  server_name your_domain www.your_domain;
. . . 
  # Uncomment these lines once you acquire a certificate:
   ssl_certificate 	/etc/letsencrypt/live/your_domain/fullchain.pem;
   ssl_certificate_key /etc/letsencrypt/live/your_domain/privkey.pem;
. . .
```

Lưu file của bạn và thoát text editor. Ta sẽ xác minh rằng file cấu hình Nginx có hiệu lực bằng lệnh sau:

    sudo nginx -t

Cuối cùng, khởi động lại Nginx để áp dụng các thay đổi:

    sudo systemctl reload nginx

Server Mastodon của bạn hiện đang được chạy bởi Nginx, nhưng bạn vẫn cần thiết lập một số dịch vụ trước khi kiểm tra trang web của mình.

<a name="6"></a>
Bước 5: Thiết lập Dịch vụ Mastodon

Mastodon bao gồm ba service hệ thống để duy trì chức năng và thời gian hoạt động: `mastodon-web`, `mastodon-sidekiq`, và `mastodon-streaming`. Điều này cho phép Mastodon luôn restart, giữ cho ứng dụng web hoạt động và thực hiện lập lịch tác vụ không đồng bộ (asynchronous task scheduling). Mastodon sẽ luôn đi kèm với các service này, bạn cần thiết lập chúng đúng cách và kích hoạt chúng.

Đầu tiên, sao chép các service từ source của Mastodon:

    sudo cp /home/mastodon/live/dist/mastodon-*.service /etc/systemd/system/

Các file này giả định rằng mọi thứ được cài đặt mặc định, chẳng hạn như sử dụng `mastodon` làm tài khoản user của bạn. Nếu bạn đã thiết lập thứ gì khác với mặc định hoặc khác với các đề xuất của bài viết này, bạn có thể mở các file ra và kiểm tra lại các tên user và các đường dẫn cần thay đổi:

    sudo nano /etc/systemd/system/mastodon-*.service

Nếu bạn làm theo các bước trong bài này, bạn sẽ không cần phải thay đổi bất cứ điều gì. Sau khi bạn đã kiểm tra các file của mình, hãy đóng các file và tiến hành kích hoạt các service. Đầu tiên, bạn cần gọi `daemon-reload`:

    sudo systemctl daemon-reload

Sau đó, bạn có thể bật các service mới được kích hoạt của mình:

    sudo systemctl enable --now mastodon-web mastodon-sidekiq mastodon-streaming

```
Created symlink /etc/systemd/system/multi-user.target.wants/mastodon-web.service → /etc/systemd/system/mastodon-web.service.
Created symlink /etc/systemd/system/multi-user.target.wants/mastodon-sidekiq.service → /etc/systemd/system/mastodon-sidekiq.service.
Created symlink /etc/systemd/system/multi-user.target.wants/mastodon-streaming.service → /etc/systemd/system/mastodon-streaming.service.
```

Việc cài đặt Mastodon của bạn đã hoàn tất. Bạn có thể mở Mastodon trong trình duyệt của mình bằng cách truy cập: *https: // your_domain*. 

**Lưu ý rằng** lần đầu tiên của bạn mở Mastodon trong trình duyệt của bạn sẽ kích hoạt một số khởi tạo ở phía back-end, vì vậy có thể sẽ dẫn đến việc không hoàn tất việc load trang. 

Khi bị lỗi như vậy thì trên màn hình có thể hiển thị "We’re sorry, but something went wrong on our end". Trong trường hợp này, hãy đợi trong vài giây, sau đó refresh trình duyệt trong khi giữ shift để thực hiện làm mới trang web. Điều này sẽ giải quyết vấn đề và chỉ xảy ra lần đầu tiên Mastodon được tải.

Bạn có thể sử dụng user quản trị viên và mật khẩu đã tạo từ trước để đăng nhập và bắt đầu thao tác trên đây. Ngoài ra, bạn nên thay đổi mật khẩu và cài đặt chức năng quản trị trên server Mastodon mới của bạn.

Bạn cũng có thể tham khảo thêm về hướng dẫn cài đặt Ruby on Rails với rbenv trên Ubuntu 20.04 để có thêm các tùy chọn kiến thức về Ubuntu 20.04 hiện có trên website Vietnix.

<a name="7"></a>
Lời kết

Trong bài viết này, chúng ta đã thiết lập một server Mastodon riêng. Điều này bao gồm thiết lập cơ sở dữ liệu và web server với reverse proxy, cùng với tất cả các yêu cầu cần thiết và các dependency cụ thể cho Mastodon. Trong Mastodon, bạn đã thiết lập email, không gian lưu trữ và các file, trước khi tạo tài khoản quản trị viên đầu tiên của bạn. Cuối cùng, bạn đã bảo mật thiết lập của mình với SSL/TLS bằng Certbot và Let's Encrypt. Cảm ơn bạn đã theo dõi bài viết.

Nguồn: [Vietnix.vn](https://vietnix.vn/cai-dat-mastodon-tren-ubuntu-20-04/)