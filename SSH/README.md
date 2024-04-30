# 1.Cấu hình dịch vụ SSH
- OpenSSH là phần mềm mã nguồn mở nó cho phép tạo dịch vụ SSH Server và các chương trình SSH client. Cài đặt mặc định trong các máy CentOS và Ubuntu. Nếu chưa có ta sẽ cài đặt như sau:
- Cài đặt OpenSSH Server trên ubuntu : ```sudo apt-get install openssh-server openssh-client```
- Chạy dịch vụ SSH Server: 
	- ```systemctl start sshd```
	- ```systemctl enable sshd```
- Mặc định SSH dùng cổng 22 nên phải mở cổng này nếu có firewall
	- ```firewall-cmd --add-port=22/tcp --permanent```
	- ```firewall-cmd --reload```
- File cấu hình của OpenSSH Server tại : ```/etc/ssh/sshd_config``` ta có thể mở và chỉnh sửa theo nhu cầu. Sau đó cần khởi động lại SSH Server: ```systemctl restart sshd```
- Kiểm tra lại trạng thái của SSH Server : ```systemctl status sshd```
- Cấu hình dịch vụ OpenSSH Server ta mở file lên và sửa cấu hình: ```/etc/ssh/sshd_config```
	- Đổi cổng : ```Port xxxx```
	- Chỉ cho phép một số User kết nối với SSH : ```AllowUsers abc quy nlplab```
	- Cấm một số User : ```DenyUsers user2 nmq```
	- Bật chế độ xác thực SSH Key : ```PubkeyAuthentication yes```
	- Chỉ ra đường dẫn lưu Public key tại ```.ssh/authorized_keys``` : AuthorizedKeysFile .ssh/authorized_keys
	- Tắt xác thực bằng Password : ``` PasswordAuthentication no```
	- Không cho xác thực bằng password rỗng : ```PermitEmptyPasswords no```
	- Không cho tài khoản root đăng nhập : ```PermitRootLogin no```

# 2. Kết nối SSH Server với OpenSSH Client
- ```ssh -p number_port username@remoteserver``` : Kết nối với number_port 
- ```ssh -p number_port -vvv username@remoteserver``` : kết nối hiện chi tiết quá trình kết nối
## Sử dụng Public/Private Key để xác thực
- ssh client khi chạy, thực hiện các kết nối nó sẽ tìm file cấu hình tại đường dẫn ```~/.ssh/config```. Nếu có file này nó dùng file này để thiết lập các thông tin bổ sung kết nối.
- Trên linux user có tên là nlplab thì ```~/.ssh/config``` có đường dẫn đầy đủ là ```/home/nlplab/.ssh/config```. Nếu trên hệ thống chưa có thì cần phải tạo file này.
```
Host nlplab
    Port 2222
    HostName 192.168.1.52
    User testuser
    PreferredAuthentications publickey
    IdentityFile "/home/minhquy/id_rsa"
```
# 3. Lệnh SCP
- Sử dụng scp để tải các file và thư mục từ máy remote về với giao thức SSH hoặc upload file, thư mục máy local lên server. Cú pháp :
```
- scp [Option] user_src@src_host: src_file user@dest_host: dest_file
```
Trong đó
- ```user_src@src_host:src_file``` : Là file, thư mục nguồn. Ví dụ ```abcuser@192.168.1.55:/home/file1.txt``` là ```file1.txt``` lưu tại máy ```abcuser@192.168.1.55```. Nếu máy local thì không cần chỉ ra user tức loại đi ```abcuser@192.168.1.55```
- ```user@dest_host : dest_file``` : Là đường dẫn file, thư mục đích muốn copy 
- Option : tham số -r dùng để quy copy tất cả các file trong thư mục
- Download file từ server về máy :
	- ```scp nlplab@192.168.1.99:/home/data/test.txt /mycode/test.txt : Copy file từ ```nlplab@192.168.1.99``` về máy local.
	- ```scp -r root@192.168.1.99:/home/data /mycode/data01``` : Download copy 1 thư mục từ server về máy
- Upload copy file, thư mục từ local lên server : 
	- ```scp /mycode/test.txt root@192.168.1.99:/home/data/test.txt```
	- ```scp -r /mycode/data root@192.168.1.99:/home/data```

# 4. SSH Key và xác thực kết nối SSH bằng Public/Private Key
- Ngoài cơ chế xác thực bằng mật khẩu còn có cơ chế sử dụng SSH Key để xác thực. Để tạo nên xác thực này cần có 2 file: Private Key và Public Key
	- Public Key : Khóa chung là 1 file text nó lưu lại ở phía server SSH nó dùng để khi client gửi private key lên để xác thực thì kiểm tra phù hợp giữa Private Key và Public Key này.
	-  Private Key : Khóa riêng là một file text bên trong nó chứa mã riêng để xác thực. Máy khách kết nối với máy chủ phải chỉ ra file này khi kết nối SSH thay vì nhập mật khẩu.
- Tạo SSH Key : ```ssh-keygen -t rsa```
- Private key lưu ở : ~/.ssh/id_rsa.
- Public key lưu ở : ~/.ssh/id_rsa.pub.

- Cấu hình Public Key cho SSH server : Mở file /etc/ssh/sshd_config . Đảm bảo nó chứa 2 dòng sau : ``` PubkeyAuthentication yes``` và  ```AuthorizedKeysFile .ssh/authorized_keys```
- Trong đó .ssh/authorized_keys cho biết Public key lưu tại .ssh/authorized_keys.
- Cấu hình quyền : 
	- ```/home/nlplab`` : chmod 700
	- ```/home/nlplab/.ssh```: chmod 700
	- ```/home/abc/.ssh/authorized_keys``` : chmod 600
- Cấu hình private key cho ssh client:
```
Host nlplab
    Port 2222
    HostName 192.168.1.52
    User testuser
    PreferredAuthentications publickey
    IdentityFile "/home/minhquy/id_rsa"
```

# 5. Sftp truyền file an toàn
- sftp là giao thức truyền file an toàn. Khi cài đặt OpenSSH nó đã có sẽn sftp-server. Ta chỉ chỉnh việc sử dụng một trình ftp client có hỗ trợ giao thức SFTP để kết nối, duyệt file, download, upload giữa máy trạm và server.
- Để kết nối sftp : ```sftp nlplab@192.168.1.52```. Lúc này ```nlplab@192.168.1.52``` được kết nối ssh. Nó kết nối và làm việc với hệ thống file giữa server và local.
- Lệnh : ```ls``` liệt kê file trên server và ```lls``` dùng để liệt kê file thư mục của máy local
- Lệnh : ```cd``` dùng để di chuyển thư mục làm việc trên server và ```lcd``` dùng để di chuyển thư mục làm việc trên máy local
- Lệnh : ```pwd`` để xem vị trí trên server và ```lpwd``` để xem vị trí trên local
- ```rename oldpath newpath``` : Đổi tên file trên server
- ```rm path``` : Xóa file, thư mục
- Tải file, thư mục remote về thư mục local hiện tại. ```get -r data```
- Tải lên từ local lưu vào thư mục hiện tại của server. ```put -r mycode```
- Lệnh ```exit``` để thoát

















