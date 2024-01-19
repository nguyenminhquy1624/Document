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


#6 Vim
- Vim có 7 chế độ : normal, insert, visual, replace, Ex-mode, select, command-line
- Chế độ normal : Cho phép sử dụng câu lệnh được bắt đầu bằng ```:``` hoặc các phím tắt.
- Chế độ Insert : Cho phép sửa, thao tác với văn bản tương tự như trình soạn thảo khác bằng gõ các ký tự. Để chuyển từ chế độ normal sang chế độ Insert có thể dùng phím ```A``` hoặc ```i```, ```I```, ```a```, ```o```,```0```.
- Visual : Tương tự như normal nhưng cho phép bôi đen đoạn văn bản để thao tác. Để chuyển sang chế độ này có thể dùng phím ```v```.
### 6.1 Mở file với vim
- Cách 1 :```vim 'path-file' ```
- Cách 2 : khởi động với câu lệnh ```vim``` sau đó sử dụng 1 trong các câu lệnh ```:edit```, ```:open```, ```:tabedit```, ```:split```, ```:vsplit```
## 6.2 Các thao tác trong file
- Di chuyển con trỏ: h(trái), j(dưới), k(trên), l(phải).
- Di chuyển với phím tắt:
	- ```:<số dòng>``` : Di chuyển con trỏ tới dòng tương ứng
	- ```0:``` : Di chuyển con trỏ tới đầu dòng hiện tại
	- ```:0``` : Di chuyển con trỏ tới dòng đầu tiên của file
	- ```$``` : Di chuyển con trỏ tới cuối dòng hiện tại
	- ```:$``` : DI chuyển chuyển con trỏ tới dòng cuối cùng của file
	- ```:/<từ cần tìm> : Di chuyển con trỏ tới vị trí tự khóa cần tìm trong file
	- ```v + phím điều hướng``` : Bôi đen đoạn text
	- ```y``` : copy đoạn text đang bôi đen
	- ```p``` : paste đoạn text đang bôi đen
- Chèn:
	- ```i``` : Con trỏ sẽ ở vị trí hiện tại
	- ```I``` : con trỏ sẽ ở đầu dòng hiện tại
	- ```a``` : con trỏ sẽ ở ngay vị trí hiện tại 1 ký tự
	- ```A``` : con trỏ ở cuối dòng hiện tại
	- ```o``` : thêm một dòng bên dứoi dòng hiện tại và đặt con trỏ tại dòng này
	- ```0``` : thêm 1 dòng bên trên dòng hiện tại
- Xóa
	- ```x``` : XÓa 1 ký tự ở vị trí hiện tại
	- ```dw``` : xóa 1 từ tính từ vị trí con trỏ hiện tại
	- ```d``` hoặc ```dd``` : XÓa cả dòng hiện tại
	- ```d$``` : Xóa hết dòng tính từ vị trị trí hiện tại
	- ```d{số dòng}``` : Xóa số dòng bằng giá trị {số dòng} tính từ dòng hiện tại
- Undo : Vim sử dụng phím ```u``` hoặc ```Ctrl + R```
- Lưu và thoát file
	- ```:w``` : lưu mà không thoát file
	- ```:wq``` hoặc ```:x``` : lưu và thoát file
	- ```:q``` : thoát file
	- ```:q!``` : thoát mà không lưu file
	- ```:qa``` : thoát tất cả các file đang mở 













































