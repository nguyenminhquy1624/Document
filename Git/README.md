1. Git là công cụ quản lý mã nguồn
![](https://www.google.com/url?sa=i&url=https%3A%2F%2Fdev.to%2Fmollynem%2Fgit-github--workflow-fundamentals-5496&psig=AOvVaw2lDQH4NribOz4LF7aqW4Jn&ust=1700494241487000&source=images&cd=vfe&opi=89978449&ved=0CBIQjRxqFwoTCNCMyOuw0IIDFQAAAAAdAAAAABAm)

+ committed : file ở thư mục làm việc (working directory) đã đồng nhất với file lưu trong local repository
+ Modified : file đang thay đổi 
+ Staged : file thay đổi, được chuẩn bị để commit vào local repostory. Nếu commit thành công thì file chuyển về trạng thái commited

# Các khái niệm
+ Repository : là nơi chứa tất cả những thông tin cần thiết để duy trì và quản lý các sửa đổi và lịch sử của toàn bộ project. Trong repo có 2 cấu trúc dữ liệu chính là oject store và index. Tất cả dữ liệu của repo đều được chứa trong thư mục ta đang làm việc dưới dạng folder .git dạng ẩn.

+ Repository của git chia làm 2 loại:
	- Remote repository : :là repo dùng để chia sử giữa nhiều người và bố trí trên server
	- Local repository : Là repo ở trên máy tính local của bản thân
+ Các lệnh trong git
	- Lệnh git init : Tạo một repository trong local
	- Lệnh git status : Xem trạng thái của repository
	- Lệnh add: Để đẩy file từ working directory vào staging area để chuẩn bị cho việc commit. Với " git add "name_file"" dùng để add một file chỉ định. "git add ." hoặc "git add --all" dùng để add tất cả các file.
	- Lệnh git commit -m "message" : Ghi lại thay đổi tại máy local và khi ta save nhiều lần 1 file nhưng khi ta commit thì trạng thái của lần save cuối mới được lưu lại.
	- Lệnh git log : Để xem lịch sử chi tiết các lần commit.
	- Lệnh git remote add origin https://github.com/nguyenminhquy/repo : Dùng để đồng bộ repository ở trên mạng và trên máy 
	- Lệnh push -u origin master : thao tác để đẩy mã nguồn từ branch master từ máy lên github
	- Lệnh pull : thao tác lấy mã nguồn từ một hoặc nhiều nhánh cụ thể nào đó ở remote server nào đó về local repository trên máy tính cá nhân. Dùng để đồng bộ hóa code trên github và máy tính cá nhân.
	- Lệnh git branch "name branch" : Tạo 1 branch mới để viết chức năng của repo
	- Lệnh git checkout "name branch" : Chuyển code sang branch mới
	- Lệnh git push --set-upstream origin "name branch": Dùng để tạo branch và push file lên github. 
	- Lệnh git merge " name branch" : Khi ta muốn merge một branch nào đó và master. Ta sẽ checkout về master và sử dụng lệnh trên để merge file vào.
	- Lệnh git reset "name file" : Khi ta sử dụng lệnh git add mà muốn remove ra khỏi staging thì ta sử dụng lệnh trên để reset nhưng mà vẫn hiển thị ở trên working directory.
	- Lệnh git diff "commit 1" "commit 2" : Dùng để so sánh sự khác nhau giữa 2 commit.
	- Lệnh clone : thao tác tải mã nguồn từ một remote server về máy tính cá nhân và chỉ tải về máy local nhánh master
	- Lệnh fetch : cập nhật thay đổi từ repository server về repository local
	- Lệnh fork : là hành động người dùng khác copy một bản sao của repo về kho của họ. Trước khi tham gia vào dự án của người khác thì ta sẽ phải fork repo của họ vào kho của riêng mình nếu như người khác chưa cho phép trở thành thành viên chính của dự án.
	
