# Các lệnh cơ bản trong linux:
- pwd : Hiển thị vị trí hiện tại đang đứng
- cd "path" : Di chuyển đến vị trí khác
- ls : Liệt kê các thư mục, tệp tại vị trí đang đứng
	- ls -R : liệt kê tất cả các file bao gồm cả các thư mục bên trong
	-ls -a : liệt kê những file ẩn
	-ls -al : liệt kê tất cả các file và thư mục chi tiết với thông tin phân quyền, kích thước, chủ sở hữu, ...
- cat > "name file" : Để tạo ra 1 file mới
- cp "path 1" "path 2" : Tạo bản sao mới từ path 1 sang path 2
- mv "path 1" "path 2" : Di chuyển path 1 sang path 1
- mkdir "name" : Tạo folder mới
- rmdir "name folder" : Dùng để xóa thư mục trống
- rm -r " name folder" : Sử dụng để xóa thư mục và file bên trong nó
- touch "name file" : Tạo ra 1 file trống
- grep "text" "name file" : Sử dụng để tìm kiếm text chứa trong name file
- find . "folder" : Dùng để tìm file ở trong thư mục hiện tại
- df -m : Để hiển thị dung lượng lưu trữ
- du -h : Kiểm tra dung lượng của file hoặc thư mục
- head -n "so dong" "ten file" : Hiển thị số dòng đầu tiên của file
- tail -n "so dong" "ten file" : Hiển thị số dòng cuối cùng của file
- wget "path" : Sử dụng để tải file từ trên internet về máy
- ping "path" : Kiểm tra kết nối internet với 1 web nào đó
- history : Hiển thị các lệnh command đã nhập trước đó
- echo "nhap text vao" >> "name file" : Sử dụng để viết dữ liệu vào 1 file
- zip -r folder.zip folder : Dùng để nén 1 folder
- unzip folder.zip : Dùng để giải nén 
- gzip "name file" : Sử dụng để nén file đuôi .gz
- gunzip "name file" : Sử dụng để giải nén file đuôi .gz
- clear : Để xóa ternminal hiển thị
- Ctrl + A : Di chuyển đến đầu dòng
- Ctrl + E : Di chuyển tới cuối dòng
- command 1 && command 2 && command 3 : Sử dụng để chạy tuần từ các lệnh command
- free -h : Dùng để kiểm tra bộ nhớ RAM của máy tính.
# Quản lý các tiến trình :
- top : hiển thị danh sách các tiến trình đang chạy và dung lượng CPU
- ps -A: hiển thị tất cả các tiền trình chạy
- kill pid : Sẽ dừng thực thi chương trình có mã pid


# Cấp quyền : CHMOD
- 0 : Không có quyền
- 1 : Quyền thực thi
- 2 : Quyèn viết
- 3 : Quyền thực thi và viết
- 4 : Quyền đọc
- 5 : Quyền đọc và chạy
- 6 : Quyền đọc và viết
- 7 : Tất cả các quyền
- Để cấp quyền 7 cho tệp demo.txt : chmod 777 demo
- sudo chown "user 1" "name file" : Chuyển quyền chủ sở hữu file sang user 1
- Cấp lại quyền folder: 
	- ```sudo chown -R $USER:$USER```


# SSH
## 1. Login với password :
- ssh username@host
## 2. Tạo cặp khóa

- ```ssh-keygen -t rsa``` : Dùng để tạo ra một cặp public/private key mới tại ~/.ssh với 2 file id_rsa-private-key và id_rsa.pub. Ta thường sử dụng file id_rsa.pub để thêm public key của ta vào server mà t muốn remote
- ```ssh-copy-id -i ~/.ssh/new_key test@remote``` : ssh-copy-id để thực hiện sao chép public key từ máy local đến server. Lệnh trên dùng để tạo 1 cặp public/private key với tên new_key ở thư mục ~/.ssh và muốn cài đặt cho user test tới server remote.
- ```ssh uer@host``` : Lệnh này để remote tới server mà ta sử dụng lệnh

## 3. SSH config
- Ta có thể cấu hình cho các kết nối ssh sử dụng các tên thay thế ngắn hơn cho hostname sử dụng file ~/.ssh/config hoặc file /etc/ssh/ssh_config.
- Ta muốn ssh một server với lệnh sau nhưng chưa sử dụng ssh_config:
- ```ssh -p 12345 foo@bar.baz.edu -i ~/.ssh/custom_key```
- Ta có thể thêm vào file ~/.ssh/config như sau:
	- Host bar
	- User foo
	- Port 12345
	- IndentityFile ~/.ssh/custom_key
	- HostName bar.baz.edu
- Sau đó ta có thể sử dụng lệnh ``` ssh bar``` để thực hiện ssh với tương tự như câu lệnh dài ở trên.

## 4. Mounting filesystems
- sshfs dùng để chia sẻ dữ liệu trên các máy linux. Nó thực hiện mount một thư mục của máy remote vào một thư mục trên máy local. Ví dụ để mount thư mục home của user test trên máy ssh-host vào thư mục /mnt/test ta sử dụng lệnh sau:
- ```sshfs test@ssh-host: /mnt/test```

## 5. Thay đổi cổng SSH
- Để thay đổi port ssh ta mở file /etc/ssh/sshd_config và tìm dòng ghi ```Port 22``` thay đổi thông số thành port ta mong muốn và khởi động lại ssh:
- ```/etc/init.d/ssh restart```


## 6. SSH github
- Cài git sudo apt-get install git
- Thiết lập:
	- ```git config --global user.name "nguyenminhquy1624"```
	- ```git config --global user.email "minhquy1624@gmail.com"```
	- ```git config --global core.editor nano```
- Xem các thiết lập : ```git config --list```
- Liên kết github bằng ssh
	- ```ssh-keygen -t rsa```
	- Add SSH key ```ssh-keygen -t -rsa -C minhquy1624@gmail.com```
	- ```ssh-agent -s```
	- ```ssh-add ~/.ssh/id_rsa```
	- ```cat ./ssh//id_rsa.pub```
	- Sau đó copy đoạn mã hiển thị sau khi gõ lệnh ở trên rồi vào https://github.com/settings/ssh chọn New SSH key rồi thêm key vào đó.
	- Sau đó vào terminal chạy ```ssh git@github.com```

## 7. Port ubuntu
- Kiểm tra các port đang hoạt động : ```netstat -paten```
- Kiểm tra thông tin của port đang sử dụng : ```sudo lsof -i :"$PORT$"
- Dừng toàn bộ process đang nghe ở cổng "$PORT$" : ```sudo kill -9 "$PID$"```






	
	
	
