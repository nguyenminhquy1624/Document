# 1. Shell
- Shell là chương trình người dùng cung cấp giao diện cho người dùng các dịch vụ hệ điều hành.
- Shell chấp nhận các lệnh có thể đọc được từ người dùng và chuyển đổi chúng thành thứ mà kernel có thể hiểu được.
- Nó là một trình thông dịch ngôn ngữ lệnh thực thi các lệnh được đọc từ các thiết bị đầu cuối.
# 2. Shell Script
- Các lệnh shell được viết vào trong 1 file và chúng có thể thực thi trong shell tránh các công việc lặp lại.

- Các shell script tương tự như batch file trong MS-DOS. Mỗi shell script được lưu với phần mở rộng tệp .sh
- Một shell script có cú pháp giống bất kỳ ngôn ngữ lập trình khác.
- Shell script bao gồm các phần sau:
	- Shell Keywords - if, else, break,...
	- Shell commands - cd,ls,echo,pwd, touch,...
	- functions
- Ưu điểm của shell script :
	- Lệnh cú pháp giống trong dòng lệnh
	- Viết nhanh hơn
	- Tích hợp với debugging
- Nhược điểm :
	- Dễ xảy ra lỗi gây  thiệt hại lớn
	- Tốc độ thực thi chậm
	- Không phù hợp với task lớn
	- Cung cấp ít loại cấu trúc dữ liệu

#3. Lập trình với Shell Script
## 1.Lệnh set:
- Dùng để set hoặc unset giá trị của shell và 1 số tham số
- set -e : thoát khỏi shell nếu trong file có 1 lệnh lỗi và lệnh thường để ở đầu file
- set -x : bật chế độ debug
- set -t : Thoát sau khi đọc và thực thi một lệnh

## 2. Lệnh export:
- Dùng để xuất một biến hoặc hàm tới môi trường của tất cả các tiến trình con đang chạy trong shell hiện tại
- Ví dụ:
- ```export CLIENT_DIR="$(pwd)/client"```

## 3.Lệnh echo
- Dùng để in ra một biến hoặc giá trị nào đó ra cửa sổ dòng lệnh
- ```echo "Hello World"```
## 4. Vòng lăp for
```
for i in 1 2 3 4 5 6
do
	echo "Loop number $i"
done
```
## 5. Lệnh if
```
if [ <điều kiện> ]
then
	Lệnh
fi
```




