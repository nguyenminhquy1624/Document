# 1. Giới thiệu
- Apache pinot là kho dữ liệu OLAP phân tán theo thời gian thực được xây dựng nhằm mục đính phân tích độ trễ thấp, thông lượng cao và hoàn hảo cho khối lượng công việc phân tích mà ngừoi dùng đối mặt.
- Apache pinot là kho dữ liệu xử lý phân tích trực tuyến (OLAP) theo thời gian thực. Sử dụng Pinot để nhập và truy vấn ngay dữ liệu từ các nguồn phát hoặc hàng loạt.
- APache Pinot có ưu điểm:
	- Độ trễ thấp với thông lượng cao
	- Lưu trẽ dữ liệu dạng cột với một số kỹ thuật smart index và kỹ thuật tổng hợp thông minh
	- Mở rộng quy mô không có giới hạn
	- Hiệu suất ổn định dựa trên kích thước cụm và ngưỡng truy vấn
- Phân tích thời gian thực hướng người dùng:
	- Dữ liệu mới : Hệ thống cần có khả năng nhập dữ liệu theo thời gian thực và cung cấp dữ liệu đó để truy vấn
	- Hỗ trợ dữ liệu tốc độ cao : nhiều chiều từ hành động khác nhau và từ nguồn khác nhau
	- Độ trễ thấp
	- Độ tin cậy và tính sẵn sàng cao
	- Khă năng mở rộng
	- Chi phí thấp để phục vụ
- Pinot hỗ trợ:
	- Phân tích theo thời gian thực hướng tới ngừoi dùng
	- Bảng điều khiển thời gian thực cho số liệu kinh doanh
	- Doanh nghiệp kinh doanh thông minh
	- Phát triển ứng dụng doanh nghiệp
	
# 2. Kiến trúc
## 2.1 Tenant
- Hỗ trợ việc cách ly dữ liệu và quản lý tài nguyên hiệu quả trong cụm pinot
- Mỗi bảng được liên kết với một tenant hoặc một không gian logic giới hạn vị trí cụm xử lý các truy vấn trên bảng. Tenant trên pinot có định dạng text tag tên logic tenant . Tài nguyên phần cứng được liên kết với tenant tag trong không gian tenant. Các bảng của một tag tenant sẽ được chỉ định được lên lịch để lưu trữ và xử lý truy vấn trên các tài nguyên phần cứng thuộc cùng một tenannt tag. Điều này cho phép ngừoi pinot chỉ định khối lượng công việc được chỉ định cho một số tài nguyên phần cứng nhất định ngăn không cho dữ liệu trong các khối lượng công việc riêng biệt được lưu trữ hoặc xử lý trên cùng một phần cứng vật lý.
 - Mặc định tất cả các bảng được broker và server đều thuộc một tenant có tên Defaulttenant nhưng bạn có thể định cấu hình nhiều tenant trong cùng một cụm
- Để hỗ trợ nhiều tenant, pinot có dịch vụ hỗ trợ dành cho multi tenant. Mỗi bảng được liên kết với một đối tượng server tenant và mộ broker tenant điều khiển các nút được bảng sử dụng làm server và broker. Multi-tenant cho phép pinot nhóm tất cả các bảng thuộc một trường hợp sử dụng cụ thể dứoi một tên tenant duy nhất
- Khái niệm về tenant rất quan trọng khi có nhiều trường hợp sử dụng pinot và cần phải cung cấp hạn ngạch hoặc một số hình thức tách biệt giữa các tenant.
- Ví dụ ta có 2 bảng A và B. Chúng ta có thể cấu hình bảng A với server tenant A và bảng B với server tenant B. Chúng ta có thể gắn thẻ server node cho tenant A và tenant B. Điều này đảm bảo rằng các ơhân đoạn của bảng A chỉ nằm trên các máy chủ được gắng thẻ A và phân đoạn bảng B chỉ nằm trên các máy chỉ B được gắn thẻ cho tenant B. Có thể đạt được sự cô lặp tương tự ở cấp độ broker bằng cách cấu hình tenant của broker cho các bảng.
 
## 2.2 Server 
- Server pinot cung cấp toàn bộ lưu trữ chính cho các phân đoạn và thực hiện tính toán cần thiết để thực hiện truy vấn. Một cụm product pinot chứa nhiều server. Càng có nhiều máy chủ thì cụm càng có thể giữ lại nhiều dữ liệu trong bảng, độ trễ mà cụm có thể phân phối trên các truy vấn càng thấp và cụm có thể xử lý càng nhiều truy vấn đồng thời

### 2.2.1 Offline
- Offline server chịu trách nhiệm tải xuống các phân đoạn từ segment store để lưu trữ và phân phát các truy vấn. Khi một segment mới được tải lên coltroller, controller sẽ quyết định các máy chủ sẽ được lưu trữ segment mới và thông báo cho chúng tải xuống các segment đó từ segment store. Khi nhận được thông báo này, server sẽ tải xuống tệp segment và tải segment đó lên server truy vấn chúng.
 
### 2.2.2 Realtime 
- Realtime server nhập trực tiếp từ luồng realtime như kafka. Theo định kỳ, họ tạo các segment dữ liệu được nhập vào bộ nhớ, dựa trên các ngưỡng nhất định. Segment này sau đó được lưu trữ trên các segment store.
- Pinot server được mô hình hóa các participant Helix, lưu trữ các bảng pinot. Các segment của bảng được mô hình hóa dứoi dạng phân vùng Helix. Do đó, các pinot server lưu trữ một hoặc nhiều phân vùng helix của một hoặc nheièu tài nguyên tức là một hoặc nhiều phân đoạn của một hoặc nhiều bảng

## 2.3 Controller
- Controller cho phép quản truy vấn và truy xuất dữ liệu hiệu quả
- Pinot controller lên lịch và sắp xếp lại các tài nguyên trong cụm pinot khi metadata thay đổi hoặc một nút bị lỗi. Với tư các là controller điều khiển Helix. Pinot controller lên lịch các tài nguyên bao gồm cụm và điều phối các kết nối giữa các quy trình bên ngoài nhất định và các thành phần cụm. Controller pinot có thể được triển khai dứoi dạng một quy trình duy nhất trên server của chính nó haowjc dứoi dạng một nhóm máy server trong cấu hình chủ động/ thụ động
- Pinot controller hiển thị điểm cuối REST API cho các hoạt động quản trị trên toàn cụm cũng như bảng điều khiển truy vấn dựa trên web để thực thi các truy vấn SQL tương tác và thực hiện các tác vụ quản trị đơn giản. Pinot controller thực hiện các việc sau:
	- Duy trì metata global của hệ thống với sự trợ giúp của Zookeper được sử dụng làm kho lưu trữ metadata liên tục.
	- Lưu trữ bộ điều khiển Helix và quản lý các thành phần pinot khác.
	- Duy trì ánh xạ máy chủ nào chịu trách nhiệm cho segment nào. Ánh xạ này được server sử dụng để tải xuống phần segment mà chúng chịu trách nhiệm. Ánh xạ này cũng được các broker sử dụng để quyết định máy chủ nào sẽ định tuyến các truy vấn tới.
	- Cung cấp các endpoint cho quản trị viên xem tạo, cập nhật và xóa cấu hình được sử dụng để quản lý và vận hành cụm.
	- Cung cấp endpoint để tải segment lên được sử dụng trong quá trình đẩy dữ liệu offline. Chịu trách nhiệm khởi tạo mức tiêu thụ theo thời gian thực và điều phối các segment realtime đang tồn tại vào segment store theo định kỳ.
	- Thực hiện các hoạt động quản lý khác như quản lý việc lưu trữ các phân đoạn, xác nhận      
	- Để dự phòng, có thể có nhiều phiên bản của pinot controller. Pinot hi vọng rằng tất cả các bộ điều khiển được cấu hình với cùng một hệ thống lưu trữ phía sau để chúng có chế độ xem chung về các phân đoạn. Pinot có thể sử dụng các hệ thống lưu trữ khác như HDFS hoặc ADLS.

## 2.4 Broker
- Tối ưu hóa các xử lý truy vấn và truy xuất dữ liệu và nâng cao các ứng dụng dựa trên dữ liệu.
- Các pinot broker nhận yêu cầu truy vấn từ các quy trình của client, phân tán chúng đến các máy chủ hiện hành, thu thập kết quả và trả kết quả cho khách hàng. Controller chia sẻ metadata với các broker cho phép các broker tạo kế hoạch thực hiện truy vấn liên quan đến một tập hợp con máy chủ tối thiểu có dữ liệu nguồn và khi được yêu cầu, các máy chủ khác sẽ xáo trộn và hợp nhất kết quả.
- Một cụm pinot quản lý nhiều broker. Càng có nhiều broker thì cụm cnàg có thể có nhiều truy vấn đồng thời và độ trễ có thể phân phối trên các truy vấn càng thấp.
- Các pinot broker được mô hình hóa như khán giả của Helix. Họ cần biết vị trí của từng segment của bảng và định tuyến các yêu cầu đến server thích hợp lưu trữ các segment của bảng đang được truy vấn.
- Broker đảm bảo rằng tất cả các client được truy vấn chính xác một lần để trả kết quả chính xác nhất quán cho một truy vấn. Các broker có thể tối ưu háo để cắt bớt một số phân khúc miễn không ảnh hưởng đến độ chính xác.
- Helix cung cấp khuôn khổ để ngừoi xem có thể tìm hiểu vị trí mà mỗi segment của tài nguyên. Các broker sử dụng cơ chế này để tìm hiểu các server các segment cụ thể của bảng.
- Trong trường hợp bảng kết hợp các broker đảm bảo rằng sự trùng lặp giữa dữ liệu segment realtime và offline được truy vấn chính xác một lần, bằng cách thực hiện liên kết offline và realtime.

## 2.5 Minion
- Trao quyền cho việc di chuyển dữ liệu hiệu quả và tạo phân khúc trong các cụm.
- Là một thành phần tùy chọn thực thi các tác vụ nền dựa trên dữ liệu bảng ngoài các quy trình truy vấn được thực hiện bởi broker và verver. Minion chạy trên các tài nguyên phần cứng độc lập và chịu trách nhiệm thực hiện các nhiêmh vụ của minion theo chỉ dẫn của controller.
- Ví dụ về các tác vụ nhỏ bao gồm chuyển đổi dữ liệu hàng loạt từ định dạng chuẩn JSON thành các tệp segment để tải vào bảng offline và viết lại các tệp segment hiện có để lcọ các bản ghi theo yêu cầu của luật bảo mật dữ liệu. Nhiệm vụ của minion có thể chạy một lần hoặc lên lịch chạy theo định kỳ.

## 2.6 Table
- Một khối xây dựng cơ bản để tổ chức và quản lý dữ liệu trong cụm pinot cho phép xử lý và phân tích dữ liệu hiệu quả.
- Pinot lưu trữ dữ liệu trong bảng. Pinot table về mặt khái niệm giống với cơ sở dữ liệu quan hệ có hàng và cột. Các cột có cùng tên và kiểu dữ liệu được gọi là schema của bảng.
- Các pinot schema được xác định trong tệp JSON. Bởi vì định nghĩa schema đó nằm trong tệp riêng của nó nên nhiều bảng có thể chia sẻ một schema duy nhất. Mỗi bảng có thể có một tên duy nhất, chiến lược index, partition, nguồn dữ liệu và metadata.
- Pinot table bao gồm:
	- real-time : Nhập dữ liệu từ streaming source như Kafka
	- offline : Tải dữ liệu từ một batch source
	- hybrid : tải dữ liệu từ cả batch source lẫn streaming source.
- Pinot chia bảng thành nhiều segment và lưu trữ các segment này trong deep-store như HDFS cũng như pinot server.
- Trong cụm pinot một bảng được mô hình hóa dưới dạng tài nguyên Helix và mỗi segment của bảng được mô hình hóa dạng Helix partition
- Cấu hình của bảng được sử dụng để xác định các thuộc tính của bảng như name, type, indexm routing, retention.
- Các thuộc tính sau cho bảng:
	- Segment 
	- Index
	- Tenant
### 2.6.1 Segment 
- Một bảng bao gồm các chunk data gọi là segment:
	- Với offlien table các segment được tạo bên ngoài pinot và được tải lên bằng trình thực thi phân tán như Spark hoặc Hadoop.
	- Với các real-time table các segment được tạo theo một khaonrg thời gian cụ thể bên trong pinot.
### 2.6.2. Flush
- Pinot real-time comsumer ingest data, tạo các segment rồi chuyển các segment và trong đĩa. Pinot cho phép cấu hình thời điểm xóa segment theo yêu cầu sau:
	-  Số lượng hàng đã sử dụng : Sau khi sử dụng số lượng hàng chỉ định từ luồng, Pinot sẽ segment đoạn đó vào đĩa.
	- Số lượng hàng trên mỗi segment : Pinot tìm hiểu và sau đó ước tính số lượng hàng cần sử dụng. Giai đoạn bắt đầu bằng cách đặt số lượng hàng thành 100K và điều chỉnh nó để đạt được kích thước segment thích hợp. Vì pinot điều chỉnh ước lượng trong quá trình thực hiện nên kích thước segment có thể tăng đáng kể so với kích thước chính xác trong giai đoạn học. Gía trị này nên được đặt để tối ưu hóa hiệu suất truy vấn.
	- Khoảng thời gian chờ tối đa : Pinot consumer đợi khoảng thời gian được cấu hình sau đó các segment được đưa vào đĩa.
- Replicas : Một segment có thể có nhiều bản sao để cung cấp tính sẵn sàng cao. Ta có thể cấu hình bằng CLI để có số lượng các bản sao.
- Complêt mode : Nếu phân đoạn trong bộ nhớ ở server không hoàn thành với segment đã cam kết thì server sẽ xây dựng và thay segment đó. Nếu segment có sẵn không tương đương với segment đã cam kết, server chỉ tải xuống các segment đã cam kết từ controller. Tuy nhiên, trong một số trường hợp nhất định việc xây dựng segment có thể tốn nhiều bộ nhớ. Trong trường hợp này ta có muốn buộc các server không cam kết chỉ tải xuống segment từ controller thay vì xây dựng lại segment đó.
- Download Schema : Pinot server có thể không tải xuống được các segment từ deep store như HDFS sau khi hoàn thành. Tuy nhiên, ta có thể cấu hình server để tải xuống các sgemtn này từ server nganh hàng thay vì deep store. Hiện tại chỉ hỗ trợ các chương trình tài xuống HTTP và HTTPS.
### 2.6.3 Pre-aggregation
- Tổng hợp real-time khi nó được sử dụng để giảm kích thước segment. CHúng ta thêm các cột chỉ số của tất cả các hàng có cùng giá trị cho tất cả các cột thứ nguyên và thời gian, đồng thời tạo một hàng duy nhất trong segment. Tính năng này chỉ khả dụng trên các bảng real-time.

### 2.6.4 Tenants
- Mỗi bảng được liên kết với một tenant. Một segment nằm trên server có cùng tenant với chính nó.
- Tùy chọn ghi đè bảng nếu bảng di chuyển đến server có tenant khác dựa trên trạng thái segment.
### 2.6.5 Hybrid table
- Hybrid table là một bảng bao gồm 2 bảng offline và real-time có cùng tên. Trong hybrid table các segment online có thể được đẩy theo định kỳ. Múc lưu giữ trên bảng offline có thể được đặt thành giá trị cao vì các segment được đưa vào định kỳ trong khi các mức lưu giữ trên phần real-time có thể nhỏ.
- Sau khi segment offline được đẩy để bao gồm một khoảng thời gian gần đây, broker sẽ tự động chuyển sang sử dụng bảng offline cho các segment trong khoảng thời gian đó và chỉ sử dụng bảng real-time cho dữ liệu không có sẵn bảng offline.
- Trường hợp sử dụng điển hình của hybrid table là đẩy dữ liệu đã được dọn sạch, trùng lặp vào bảng offline hẳng ngày trong khi vẫn sử dụng dữ liệu theo thừoi gian thực khi dữ liệu đó đến. Dữ liệu có thể tồn tại trong các bảng offline trong vài năm trong khi dữ liệu real-time sẽ được làm sạch vài ngày một lần.

## 2.7 Segment
- Segment trong pinot để lưu trữ và truy vấn dữ liệu hiệu quả trong các cụm pinot cho phép xử lý và phân tích dữ liệu được tối ưu hóa.
- Các bảng pinot được lưu trữ trong một hoặc nhiều segment độc lập được gọi là segment. Một bảng nhỏ có thể được chứa bởi một segment duy nhất nhưng pinot cho phép các bảng phát triển thành số lượng segment không giới hạn. Có nhiều quy trình khác nhau để tạo segment. Các segment có các phân vùng dữ liệu bảng dựa trên thời gian và được lưu trữ trên các máy chủ pinot có quy mô theo chiều ngang khi cần thiết cho cả lưu trữ và tính toán.
- Pinot làm được điều này bằng cách chia dữ liệu thành các phần nhỏ hơn gọi là segment. Các segment có thể được xem là phân vùng dựa trên thời gian.
- Segment là một đoạn nằm ngang biểu thị một đoạn dữ liệu bảng có môt số hàng. Segment lưu trữ dữ liệu cho tất cả các cột của bảng. Mỗi segment đóng gói dữ liệu theo kiểu cột cùng với dictionary và index cho các cột. Segment được trình bày theo cột có thể ánh xạ trực tiếp vào bộ nhớ để phục vụ các truy vấn.
- Các cột có thể là một hoặc nhiều giá trị : String, boolean, int, long, float,.....
- Các cột có thể được khai báo là metrics hoặc dimension. trong schema. Các cột có thể là null hoặc default.
- Pinot sử dụng mã hóa dictionary để lưu trưc các giá trị dạng ID. Các cột có thể được cấu hình thành cột no-dictionary trong trường hợp các giá trị thô được lưu trữ. ID dictionary được mã hóa thành số bit tối thiểu để lưu trữ hiệu quả.
- Forward index được xây dựng cho mỗi cột và được nén để sử dụng bộ nhớ hiệu quả. Ngoài ra ta có thể tùy chỉnh các cấu hình inverted indices. cho bất kỳ tập hợp cột nào. Các invert indices chiến nhiều dung lượng bộ nhớ hơn nhưng cải thiện hiệu suất truy vấn. Các chỉ mục chuyên dụng như star-tree cũng được hỗ trợ.

## 2.8 Deep store
- Pinot quản lý và lưu trữ dữ liệu quy mô lớn một cách hiệu quả cho phép xử lý và phân tích dữ liệu hiệu quả.
- Nó được sử dụng cho các hoạt động sao lưu và khôi phục. Các server node trong một cụm sẽ lấy xuống một bản sao của các tệp segment từ deep store. Nếu các tệp segment cục bộ trên server bị hỏng theo cách nào đó, một bản sao mới sẽ được kéo xuống từ deep store khi khởi động lại server.
- Deep store lưu trữ phiên bản nén của các tệp segment và nó thường không bao gồm bất kỳ chỉ mục nào.
- Bản thân deep store không đủ cho các hoạt động khôi phục. Pinot lưu trữ metadata như cấu hình bảng, lược đồ, metadata trong Zookeper. Đối với các hoạt động khôi phục cần có cả metadata Deep store cũng như zookeper.
### Các segment đi vào deep store
- Đối với các table offline, tác vụ batch ingest job sẽ ghi nhận segment vào deep store. Sau đó, công việc ingest sẽ gửi thông báo về phân đoạn mới tới controller từ đó điều khiển sẽ thông báo này cho máy chủ thích hợp để kéo phân đoạn đó xuống.
- Với table real-time :  Theo mặc định một segment là một bộ nhớ tích hợp đầu tiên của server. Sau đó được tải lên controller chính, controller này sẽ ghi segment vào deep store.
- Việc tất cả các segment đều đi quan controller có thể trở thành nút cổ chai của hệ thống khi tải nặng. Trong trường hợp này ta cần sử dụng chính sách download ngang hàng trong controller điều khiển tách rời khỏi đường dẫn data.
- Trong khi sử dụng cấu hình này, server sẽ trực tiếp ghi một phân đoạn hoàn thành vào deep store.

## 2.9 Schema
- Schema cho phép xử lý và phân tích dữ liệu hiệu quả.
- Các cột trong schema pinot có thể được chia thành 3 loại :
	- Dimension : thường được sử dụng trong các hoạt động cắt lát để trả lời truy vấn.(GROUPBY, WHERE)
	- Metric : Các cột này đại diện cho dữ liệu định lượng của bảng. Các cột như này được sử dụng để tổng hợp. Các toán tử sử dụng : SUM, COUN, AVG, MIN, MAX, WHERE.
	- DateTime : Cột này đại diện cho các cột thời gian trong dữ liệu. Có thể có nhiều cột thời gian trong một bảng nhưng chỉ một số chúng có thể được coi là cột chính. Cột datetime chính là cột có thể cấu hình segment. Cột primary time được pinot sử dụng để duy trì ranh giới thời gian giữa dữ liệu offline và real-time trong bảng hybrid cũng như để quản lý lưu giữ. Cột thời gian chính là bắt buộc nếu loại push của bảng là APPEND tùy chọn nếu loại push là REFRESH.
- Pinot không thực thi các quy tắc nghiêm ngặt về cột danh mục nào trong số các cột danh mục này thay vào đó các danh mục có thể được coi là gợi ý để pinot thực hiện tối ưu nội bộ.
- Các danh mục cũng có liên quan khi thực hiện hợp nhất và tổng hợp segment. Pinot sử dụng các trường dimension và thời gian để xác định các bản ghi để áp dụng merge.
- Tổng hợp số liệu là một ví dụ trong pinot sử dụng dimension và time là key và tự động tổng hợp các giá trị cho cột số liệu.

#3. Indexes

## 3.1 Bloom filter 
- Khi một cột được cấu hình để sử dụng bộ lọc này. Pinot sẽ tạo một bộ lọc bloom cho mỗi segment. Bộ lọc bloom giúp cắt bớt các segment không chứa bất kỳ bản ghi nào khớp với EQUALITY
- Bloom filter là một cấu trúc dữ liệu xác suất sử dụng để xác định nếu một phần từ có ở trong dataset hay không nhưng nó không thể được sử dụng để xác định xem phần tử có hiện diện trong tập dữ liệu hay không. Hạn chế này phát sinh do bloom filter có thể tạo ra kết quả dương tính giả nhưng không bao giờ mang lại kết quả false negative.
- Một khía cạnh hấp dẫn của các bộ lọc này là sự tồn tại của một công thức toán học thiết lập mối quan hệ giữa kích thước của chúng, số lượng của tập dữ liệu mà chúng lập index và tỷ lệ false positive.
- Trong pinot số lượng phần tử này tương ứng với số lượng giá trị duy nhất được mong mỗi segment. Nếu cần, tỷ lệ false positve và kích thước index có thể được cấu hình.
- Nếu fpp càng thấp thì thì độ chính xác của bloom filter càng cao nhưng việc giảm fpp sẽ dẫn đến tăng kích thước index. Điều quan trọng cần lưu ý là maxSizeInBytes được ưu tiên hơn fpp. Nếu maxSizeInBytes được đặt thành giá trị lớn hơn 0 và kích thước được tính toán của bộ lọc bloom dựa trên dpp đã chỉ định, vượt quá giới hạn này. Pinot sẽ điều chỉnh fpp để đảm bảo rằng kích thước bộ lọc Bloom vẫn nằm trong giới hạn đã chỉ định.

## 3.2 Dictionary index
- Khi xử lý các tập dữ liệu mở rộng các giá trị thường được lặp lại nhiều lần. Để nâng cao hiệu quả lưu trữ và giảm độ trễ truy vấn, chúng tôi sử dụng dictionary index cho dữ liệu lặp lại. Đây là lý do pinot mã hóa dictionary theo mặc định. Mặc dù ta nên disable tính năng này với các cột có số lượng số cao.
### Heuristically enable dictionaries
- Hầu hết các chuyên gia về miền tạo bảng đều biết liệu dictionary có hữu ích hay không. Ví dụ một cột có giá trị ngẫu nhiên hoặc IP public có thể sẽ có số lượng lớn vì vậy chúng có thể được nhắm mục tiêu ngay lập tức dưới dạng mã hóa thô trong khi các cột như mã nhân viên sẽ có số lượng nhỏ và do đó có thể dễ dàng được công nhận là ứng viên dictionary tốt. Nhưng đôi khi quyết định có thể không rõ ràng. Để trợ giúp trong tính huống này, pinot có thể được cấu hình để tạo từ điển theo phương pháp heuristic tùy thuộc vào giá trị thực tế và hệ số quan hệ.
- Khi phương pháp phỏng đoán này được enabled, pinot sẽ tính toán hệ số cho candidate. Yếu tố này là tỷ lệ giữa kích thước forward index được mã hóa dưới dạng thô và cùng một chỉ mục được mã hóa dưới dạng từ điển. Nếu hệ số lưu cho một candidate nhỏ hơn tỷ lệ saving ratio thì từ điển sẽ không được tạo.
-  Để coi là một candidate cho phương pháp heuristic thì một cột phải :
	- Được đánh dấu là đã mã hóa dictionary.
	- Có giá trị đơn
	- Có loại kích thước cố định như int, long, ... Các loại kích thước thay đổi như json, string hoặc byte không được heuristic xem xét.
	- Không được lập text index theo Json index.
- Lựa chọn này chỉ được áp dụng cho metrics columns và dừng dimension columns.
- Hàm này được kích hoạt trong đối tượng indexingConfig trong bảng cấu hình. Các tham số chi phối heuristic :
	- optimizeDictionary : Enable heuristic với tất cả các cột và kích hoạt với một số quy tắc bổ sung.
	- optimizeDictionaryForMetrics : Enable heuristic cho cột
	- noDictionarySizeRatioThreshold : Saving ratio được sử dụng trong heuristic.
- optimizeDictionary được ưu tiên hơn optimizeDictionaryForMetric.

### Parameter
- Dictionary có thể được cấu hình với :
	- onHeap : Chỉ định liệu index có nên được tải trên heap hay không
	- useVarLengthDictionary : Xác định độ dài giá trị biến ở store.
- Dictionary luôn được lưu trữ off-heap. Tuy nhiên, trong trường hợp số nhỏ và mức sử dụng bộ nhớ trên heap có thể chấp nhận được ta có thể sao chép chúng vào bộ nhớ bằng cách đặt tham số onHeap thành true. Kho các dictionary trên heap chúng có thể mang lại hiệu suất được cải thiện và có thể thực hiện được các tối ưu hóa bổ sung.
- Tham số useVarLengthDictionary chỉ tác động đến các cột có giá trị khác nhau về số byte mà chúng chiếm giữ. Điều này bao gồm các laoị cột yêu cầu số byte thay đổi chẳng hạn như string, byte hoặc số thập phân lớn và các trường hợp trong đó không phải tất cả các giá trị trong một segment đều chiếm cùng một số byte. Ví dụ các chuỗi nói chung cũng yêu cầu lưu trữ số byte thay đổi nếu cùng một segment chỉ chứa các giá trị "a", "b", "c" pinot sẽ xác định rằng các giá trị trong segment có thể được biểu diễn bằng cùng số byte.
- Mặc định useVarLengthDictionary là false có nghĩa pinot sẽ tính toán length cho chuỗi dài nhất với segment và được áp dụng cho tất cả cho các giá trị. Cách tiếp cận này đảm bảo rằng tất cả các giá trị có thể được lưu trữ hiệu quả mang lại khả năng truy cập nhanh hơn và bố cục nén hơn khi độ dài của các giá trị tương tự nhau.
- Nếu tập dữ liệu bao gồm một vài gía trị rất lớn và vô số giá trị nhỏ thì hướng dẫn pinot sử dụng mã hóa có length thay đổi bằng cách đặt useVarLengthDictionary thành true. Khi mã hóa biến được sử dụng, pinot được yêu cầu lưu trữ độ dài của mỗi mục nhập. Do đó, chi phí lưu trữ một mục nhập sẽ trở thành kích thước thực tế của nó với 4 byte cho phần bù.


## 3.3 Forward index
- Là cơ ché pinot sử dụng để ánh xạ giá trị của mỗi cột. Ở cấp độ khái niệm, forward index có thể được coi là ánh xạ từ document ID đến giá trị cột thực tế của mỗi hàng.
- Forward index được bật theo mặc định nghĩa là các cột sẽ có forward index trừ khi bị tắt rõ ràng. Việc tắt forward index có thể tiết kiệm dung lượng lưu trữ khi các index khác đáp ứng đầy đủ các mẫu dữ liệu cần thiết.

### 3.3.1 Dictionary encoded với raw value
- Khi encodeing được đặt thành RAW thì forward index được triển khai dưới dạng một mảng trong đó các index tương ứng với document ID và các giá trị biểu thị giá trị hàng thực tế. 
- Trong trường hợp DICTIONARY encode, forward index không lưu trữ các giá trị hàng thực tế mà thay đó lưu trữ ID dictionary. Điều này đưa ra một mức độ gián tiếp bổ sung khi đọc các giá trị nhưng nó cho phép bố trí vật lý hiệu quả hơn khi số lượng giá trị duy nhất trong cột nhỏ hơn đáng kể so với số lượng hàng.
- DICTIONARY encode hiệu quả hơn nếu segment được sắp xếp theo cột được lập index. 
- Khi muốn sử dụng dictionary encode hay raw value encoding thì phải xem bảng so sánh mà ta chọn.
### 3.3.2 Dictionary encode forward index with bit compression
- Cách tiếp cận này, mỗi giá trị duy nhất trong một cột giá trị một ID và một dictionary được xây dựng để ánh xạ các ID này trửo lại các giá trị ứng với chúng. Thay vì lưu trữ các giá trị thực tế forward index thì mặc định lưu trữ các ID được nén bit. Phương pháp này đặc biệt hiệu quả khi xử lý các cột chứa ít giá trị duy nhất vì nó cải thiện đáng kể hiệu suất.
- Khi xử dụng forward index được encode cho dictionary cho cột nhiều giá trị để nén thêm forward index cho toàn bộ nhiều giá trị lặp lại ta sẽ enable cho mã hóa nén MV_ENTRY_DICT để thêm một mức nén từ dict vào multi-value entries. Điều này có thể hữu ích. Ví dụ trong trường hợp pre-join một bảng thực tế với dimension trong đó các mục multi-value trong bảng dimension được lặp lại sau khi nối với bảng thực tế. 
 
### 3.3.3 Sorted forward index with run-length encoding
- Khi một cột được sắp xếp vật lý, pinot sử dụng forward index được sắp xếp với encode run-length được xây dựng dựa trên mã hóa dictionary. Thay vì lưu trữ dictionary cho mỗi document ID, phương pháp này lưu trữ các cặp document ID bắt đầu và kết thúc cho mỗi giá trị duy nhất.
- Forward index được sắp xếp mang lại lợi ích khi nén và định vị dữ liệu hiệu quả và cũng có thể đóng vai trò là một inverted index. CHúng hoạt động khi đáp ứng 2 điều kiện : segment được sắp xếp theo cột và dictionary điwọc bật cho cột đấy.
- Khi xử lý multi-segment điều quan trọng là phải đảm bảo rằng dữ liệu được sắp xếp trong từng segment. Việc sắp xếp theo các segment là không cần thiết.
- Để đảm bảo rằng một segment được sắp xếp theo một cột cụ thể:
	- Đới với real-time thì sử dụng thuộc tính tableIndexConfig.sortedColumn. Nếu có chính xác một cột được chỉ định trong mảng đó, pinot sẽ sắp xếp segment theo cột đó khi cam kết.
	- Đối với các bảng offline, ta phải sắp xếp trước dữ liệu theo cột được chỉ định trước khi nhập dữ liệu vào pinot.
- Khi segment real-time các hàng sẽ được sắp xếp theo cột sắp xếp và nó sẽ chuyển thành offline segment.
- Trong quá trình tạo offline segment điều này cũng áp dụng real-time segment, pinot sẽ quét hết dữ liệu của từng cột. Nếu phát hiện ra giá trị trong một cột được sắp xếp theo thứ tự tăng dần thì pinot sẽ kết luận rằng segment được sắp xếp dựa trên cột cụ thể. Trong trường hợp này xảy ra trên nhiều cột, tất cả chúng đều được coi là cột sắp xếp. Do đó, việc segment có được sắp xếp theo cột hay không chỉ phụ thuộc vào việc phân bố dữ liệu thực tế trong segment đó và hoàn toàn không quan tâm đến giá trị của thuộc tính sortColumn. Cách tiếp cận này cũng ngụ ý rằng 2 phân đoạn thuộc cùng một bảng có thể có số cột sắp xếp khác nhau. Trong trường hợp cực đoan khi một phân đoạn chỉ chứa 1 hàng, pinot sẽ coi tất cả các cột trong phân đoạn đó là các cột sắp xếp.
 	
### 3.3.4 Raw value forward index
- Raw value forward index lưu trữ các giá trị thực tế thay vì ID. Điều này có nghĩa nó loại bỏ nhu cầu tra cứu từ điển khi nạp các giá trị, điều này có thể giúp cải thiện hiệu suất truy vấn. Forward index đặc biệt hiệu quả đối với các cột có số lượng lớn gía trị duy nhất trong đó mã hóa dictionary không mang lại lợi ích đáng kể.

### 3.3.4 Disable the forward index
- Theo truyền thống thì forward index được áp dụng cho tất cả các cột của file trong ổ đĩa. Tuy nhiên, một số cột nhất định chỉ có thể được sử dụng làm bộ lọc trong mệnh đề WHERE cho tất cả các truy vấn. Trong trường hợp như vậy, forward index là không cần thiết vì về cơ bản các forward index và cấu trúc khác trong các segment có thể cung cấp hàm truy vấn SQL cần thiết. Forward index chỉ chiếm dung lượng lưu trữ cho các tường hợp như vậy là lý tưởng nhất có thể được giải phóng. Do đó, để tiết kiệm dung lượng bộ nhớ lưu trữ, thì sử dụng disable để tắt forward index.
- Forward index trên một hoặc nhiều cột trong bảng Pinot có thể bị vô hiệu hóa với nhưng hạn chế:
	- Chỉ được hỗ trợ cho các offline segment.
	- Nếu cột có ở trong range thì cột phải thuộc giá trị đơn và sử dụng index version 2.
	- Các cột MV có các bản sao trong một hàng sẽ mất các mục trùng lặp khi tái tạo forward index. Thứ tự của dữ liệu với hàng MV cũng có thể thay đổi khi tái tạo. Cần phải điền các trường hợp như vậy.
	- Nếu cần hỗ trợ tạo forward index khi tải lại thì dictionary và inverted index phải được bật trên cột cụ thể.
	
- Forward index cũng có thể được tạo lại cho một cột bị vô hiệu hóa bằng cách bật indexvà reload lại segment. Chỉ có thể được tạo lại forward index nếu dictionary và inverted index đã được enable cho cột. Nếu một trong 2 đã bị disabled thì cách duy nhất để lấy lại forward index là tạo lại các segment thông qua offline và re-push hoặc refresh data.


## 3.4 Inverted index
- Chúng ta có thể xác định forward index dưới dạng ánh xạ document ID tới các giá trị. Tương tự một inverted index thiết lập ánh xạ từ các giá trị tới một tập hợp document ID biến nó thành phiên bản inverted của forward index. Khi ta thường xuyên sử dụng một cột cho các hoạt động lọc việc kết hợp inverted index có thể nâng cao đáng kể hiệu suất.
- Pinot hỗ trợ 2 loại inverted index riêng biệt : bitmap inverted và sorted inverted index. Bitmap inverted được biểu thị loại inverted index thực tế trong khi sorted sẽ tự động có sẵn khi cột được sorted. Cả 2 loại index đều yêu cầu enable dictionary cho cột tương ứng.
   
### 3.4.1 Bitmap inverted index
- Khi một cột không được sắp xếp và inverted index được enable thì pinot sẽ ánh xạ từ mỗi giá trị sang bitmap của hàng. Thiết kế này đảm bảo rằng các hoạt động tra cứu giá trị mất thừoi gian liên tục cung cấp khả năng truy vấn hiệu quả.
- Khi mà inverted index được enabled cho một cột, pinot sẽ duy trì mapping từ mỗi giá trị đến bitmap của các hàng điều này khiến cho việc tra cứu giá trị mất nhiều thời gian. Nếu có một cột thường được sử dụng để lọc, việc thêm inverted index sẽ cải thiện hiệu suất rất nhiều. Ta có thể tạo inverted index trên cột nhiều giá trị.

### 3.4.2 Sorted inverted index
- Một cột được sắp xếp vừa trang bị dictionary sẽ được mã hóa theo cách chuyên biệt phục vụ mục đích triển khai cả forward index và inverted index. Do đó, khi các điều kiện này được đáp ứng, inverted index sẽ được tạo một cách hiệu quả mà không cần cấu hình bổ sung ngay cả khi cấu hình đề xuất khác. Phiên bản được sắp này forward index cung cấp độ phức tạo về thời gian tra cứu là log(n).
- Inverted index được sắp xếp mang lại hiệu quả vượt trội so với bitmap inverted index nhưng chỉ áp dụng với các cột được sắp xếp. Trong trường hợp hiệu suất truy vấn có inverted index thông thường không đạt yêu cầu đặc biệt khi phần lớn truy vấn liên quan đến vệc lọc trên cùng một cột, việc sử dụng index được sắp xếp có thể nâng cao hiệu suất truy vấn.
 
## 3.5 Star tree index
- Star-tree hoạt động trên nhiều cột và sử dụng các kết quả được tổng hợp trước để giảm đáng kể 
số lượng giá trị cần xử lý, dẫn đến hiệu suất truy vấn được cải thiện.
- Thách thức lớn nhất trong hệ thống OLAP real-time là đạt được và duy trì SLA chặt chẽ về độ trễ và thông lượng trên các tập dữ liệu lớn. Các kỹ thuật hiện có như indexx được sắp xếp hoặc inverted index giúp cải thiện độ trễ truy vấn nhưng việc tăng tốc vẫn bị hạn chế bởi số lượng tài liệu cần được xử lý để tính toán kết quả. Mặt khác, việc tổng hợp trước các kết quả đảm bảo giới hạn trên không đổi về độ trễ truy vấn nhưng có thể dẫn đến bùng nổ không gian lưu trữ.
- Sử dụng star-tree để sử dụng các tài liệu được tổng hợp trước nhằm đạt được độ trễ truy vấn thấp và sử dụng hiệu quả không gian lưu trữ cho các truy vấn tổng hợp và theo nhóm.
### 3.5.1 Sorted index
- Theo phương pháp này, dữ liệu được sắp xếp theo primary key , key này có thể xuất hiện dưới dạng bộ lọc trong hầu hết các truy vấn trong bộ truy vấn.
- Điều này giảm thời gian tìm kiếm document theo primary key nhất định từ duyệt tuyến tính sang duyệt tìm kiếm nhị phân và cũng giữ vị trí phù hợp cho các tài liệu được chọn.
- Mặc dù đây là một cải tiến đáng kể so với duyệt tuyến tính nhưng vẫn còn một số vấn đề :
	- Mặc dù sắp xếp trên một cột không gian yêu cầu thêm dung lượng nhưng việc sắp xếp trên các cột bổ sugn yêu cầu thêm dung lượng lưu trữ để lập index lại các bản ghi cho các thứ tự sắp xếp khác nhau.
	- Mặc dù thời gian tìm kiếm từ O(n) xuống O(log(n)) độ trễ tổng thể vẫn là hàm của tổng số document cần được xử lý để trả lời một truy vấn.
	
### 3.5.2 Inverted index
- Cách tiếp cận này mỗi giá trị của một cột, chúng ta sẽ duy trì một danh sách document ID.
- Ví dụ nếu ta muốn lấy tất cả các tài liệu như "Browser" hay "FireFox" thì chúng tôi có thể tra cứu index của "Browser" và xác định rõ nó xuất hiện ở trong tài liệu [1,5,6].
- Khi sử dụng inverted index chúng ta có thể giảm thời gian tìm kiếm xuống O(1). Tuy nhiên độ trễ của truy vấn vẫn là một hàm tăng theo số lượng của document cần dược xử lý để trả lời truy vấn.
### 3.5.3 Tiền tổng hợp (Pre-aggregation)
- Ở trong kỹ thuật này, chúng tôi tiền tính toán câu trả lời cho mỗi truy vấn nhất định được thiết lập trước.
- Với cách tiếp cận này, việc trả lời truy vấn về tổng impression cho mỗi nước là một tra cứu giá trị vì chúng ta đã loại bỏ như cầu xử lý số lượng lớn tài liệu. Tuy nhiên để có thể trả lời các truy vấn có nhiều vụ từ có nghĩa là ta cần tổng hợp trước các kết hợp khác nhau của các kích thước khác nhau, điều này làm cho không gian lưu trữ tăng theo cấp số nhân.

### 3.5.4 Star-tree solution
- Chúng ta có các kỹ thuật đánh index giúp cảu thiện thời gian tìm kiếm với mức tăng không gian có giới hạn nhưng không đảm bảo giới hạn trên đối với độ trễ truy vấn. Ta có các kỹ thuật tổng hợp trước cung cấp giới hạn trên cho độ trễ truy vấn nhưng gặp phải sự bùng nổ không gian bộ nhớ theo cấp số nhân.
- Start-tree là cấu trúc cung cấp sự cân bằng có thể cấu hình giữa không gian và thời gian đồng thời cho phép chúng ta đạt được giới hạn trên cố định về độ trễ truy vấn trong một trường hợp sử dụng nhất định.

#### Tree - structure
- Star-tree là kho lưu trữ dữ liệu với các thuộc tính:
	- Root node : Là node root duy nhất, từ đó duyệt qua phần còn lại của cây
	- Leaf node : Một node lá có thể chứa tối đa T bản ghi trong đó T có thể cấu hình được.
	- None leaf node : Các node có nhiều hơn T bản ghi sẽ được chia làm các node con.
	- Star node : Các node không phải là lá cũng có thể có một node con đặc biệt gọi là star node. Node này chứa các bản ghi được tổng hợp từ trước sau khi loại bỏ dimension mà dữ liệu được phân chia cho cấp độ này.
	- Dimension split order ([D1,D2]) : Các node ở một cấp độ nhất định trong cây được chia thành các node con trên tất cả các giá trị của một dimension cụ thể. Thứ tự phân chia dimension là danh sách dimension được sử dụng để xác định dimension cần phân chia cho một cấp độ nhất định trong cây.
	
#### Node properties :
- Dimension : là chiều mà node được phân chia trên đó
- Start/ end document ID : Phạm vi tài liệu mà node này trỏ tới.
- Aggregated document ID : một document là kết quả của tổng hợp tất cả các tài liệu được trỏ bởi node này.

### 3.5.5 Index generation
- Star-tree index được đánh theo các bước sau:
	- Dữ liệu đầu tiên được chia theo dimensionsSplitOrder. Chỉ các kích thước từ thứ tự phân chia được bảo lưu, các kích thước khác sẽ bị loại bỏ. Đối với mỗi sự kết hợp duy nhất của các dimension dành riêng, số liệu được tổng hợp sao cho mỗi cấu hình. Các document tổng hợp được ghi vào một tệp và được dùng làm tài liệu star-tree ban đầu.
	- Sắp xếp các star-tree document dựa trên kích thước của dimensionsSplitOrder. Nó được sắp xếp chính theo dimension đầu tiên trong danh sách này sau đó được sắp xếp thứ cấp trên các dimension còn lại dựa trên thứ tự của chúng trong danh sách. Mỗi node trogn cây trỏ đến một phạm vi trong các tài liệu đã được sắp xếp.
- Cấu trúc của cây có thể được tạo đệ quy:
	- Nếu một node có nhiều hơn T bản ghi nó sẽ được chia thành nhiều node con mỗi node wnsg với một giá trị của dimension theo thứ tự phân chia tương ứng với cấp độ hiện tại trong cây.
	- Ta có thể tạo một node cho node hiện tại bằng cách loại bỏ dimension đang được phân chia và tổng hợp số liệu cho các hàng chứa dimension có giá trị giống hệt nhau. Các document tổng hợp này được thêm vào cuối document của star-tree.
	- Nếu chỉ có một giá trị cho dimension hiện tại thì node star sẽ không được tạo vì các document bên dưới node star giống hệt với node đơn.
	
### 3.5.6 Aggregation
-  Tổng hợp là cấu hình dưới dạng một cặp hàm tổng hợp và cột để áp dụng tập hợp.
- Các loại hàm tổng hợp có kết quả trung gian có kích thước giới hạn đều được hỗ trợ: SUM, MIN, MAX, COUNT, AVG, ....

### 3.5.7 Index generation configuration
- dimensionsSplitOrder : 
	- danh sách các tên của dimension có thể được chỉ định để xác định cấu hình thứ tự phân chia. Chỉ các dimension trong danh sách này được lưu trong document aggregation. Các node sẽ phân chia dựa trên thứ tự của danh sách này. Việc phân chia ở cấp độ i được thực hiện trên các giá trị dimension tại index i trong danh sách. 
	- Dimension của star-tree không nhất thiết phải là cột dimension trong bảng mà cũng có thể là cột thời gian, cột ngày giờ hoặc cột số liệu nếu cần.
	- Cột dímension star-tree này phải được mã hóa dictionary để tạo index cho star-tree.
	- Tất cả các cột trong bộ lọc và nhóm theo truy vấn phải được đưa vào trong danh sách này để sử dụng index star-tree.
	 
- Cấu hình mặc định của index star-tree:
	- Một index star-tree mặc định có thể được thêm vào một segment bằng cách sử dụng cấu hình boolean trong enableDefaultStarTree trong tableIndexConfig.
	- Tất cả các dimension được mã hóa từ dictionary có số lượng nhỏ hơn hoặc bằng ngưỡng 10K sẽ được bao gồm trong dimensionSplitOrder được sắp xếp theo số lượng của chúng theo thứ tự giảm dần.
	- Tất cả các cột time/datetime được mã hóa dictionary sẽ được thêm vào dimensionSplitOrder theo dimension được sắp xếp theo số lượng của chúng theo thứ tự giảm dần. Ở đây, các cột thời gian sẽ được đưa vào hầu hết các truy vấn dưới dạng cột lọc phạm vi theo cột do đó để có hiệu suất tốt hơn chúng tôi luôn đưa chúng làm phần tử cuối cùng trong dimensionSplitOrder.
	- Bao gồm cả COUNT(*) và SUM cho tất cả các số liệu trong functionColumnPair.
	- Sử dụng maxLeafRecord là 10K

### 3.5.8 Query excution
- Để thực hiện truy vấn, ý tưởng đầu tiên là kiểm tra metadata để xác định xem truy vấn có thể được giải quyết bằng document star-tree hay không sau đó duyệt qua star-tree để xác định document thỏa mãn tất cả các vị từ. Sau khi áp dụng bất kỳ vị từ còn lại nào bị bỏ sót khi duyệt qua star-tree tới các tài liệu xác định sau đó áp dụng aggregation/ groupby theo các tài liệu đủ điều kiện.
- Thuật toán duyệt cây: ( predicate là một biểu diễn 1 node cha có 2 node left và right)
	- Bắt đầu từ node root
	- Với mỗi level các node con cần chọn tùy thuộc vào việc có bất kỳ predicate hoặc nhóm nào trên dimension thỏa mãn cho cấp độ truy vấn hay không.
		- Nếu không có predicate hoặc nhóm nào theo diemension thì chọn node star nếu tồn tại hoặc tất cả các node con để duyệt tiếp.
		- Nếu có các predicate trên chiều phân chia ta sẽ chọn các node con thỏa mãn các predicate đó.
		- Nếu không có các predicate nhưng có groupby thì chọn tất cả các node con ngoại trừ star-node
	- Lặp lại đệ quy bước trước đó cho đến khi đạt được tất cả các node leaf hoặc tất cả các predicate đều được thỏa mãn.
	- Thu thập tất cả các document được trỏ bởi các node đã chọn.
		- Nếu tất cả các predicate và nhóm đều được thỏa mãn hãy chọn tài liệu tổng hợp duy nhất từ mỗi node đã chọn.
		- Nếu không thu thập tất cả các document trong phạm vi từ mỗi node đã chọn.
	

## 3.6 Timestamp index
- Timestampt để tăng tốc truy vấn time với mức độ cho tiết khác nhau
- Kiểu dữ liệu timestamp lưu trữ giá trị dưới dạng giá trị dài một phần nghìn giây (ms) 
- Thông thường người dùng sẽ không cần mức độ chi tiết thấp này vì cho các truy vấn phân tích. QUét dữ liệu và chuyển đổi giá trị thời gian có thể tốn kém đối với dữ liệu lớn.
- Mẫu truy vấn phổ biến cho các cột dấu thời gian và lọc theo phạm vi thời gian rồi nhóm lại bằng cách sử dụng các mức độ chi tiết về thời gian khác nhau (dd/mm/yyyy.......)
- Thông thường điều này yêu cầu người thực thi truy vấn trích xuất các giá trị áp dụng các hàm biến đổi rồi thực hiện filter/groupBy mà không cần sử dụng từ điển hoặc index.

### Index timestamp
- Ta có thể cài đặt cấu hình mức độ chi tiết cho cột loại dữ liệu timstamp. Sau đó:
	- Pinot sẽ tạo trước một cột mỗi lần chi tiết bằng cách sử dụng forward index và range index. Quy ước đặt tên là $${ts_column_name}$${ts_granularity} trong đó cột thời gian ts có độ chi tiết DAY, MONTH sẽ có thêm cột được tạo  $ts$DAY và $ts$MONTH
	- Truy vấn ghi đè predicate và selection/groupby: GROUP BY : Các hàm dateTrunc('DAY', ts) sẽ được dịch để sử dụng cột bên dưới $ts$DAY để tìm nạp dữ liệu. PREDICATE : range index được tạo tự động cho tất cả các cột cho tiết.
	

## 3.7 Range index
- Range index cho phép có được hiệu suất tốt hơn các truy vấn liên quan đến việc lọc trên một phạm vi.
- Nó hữu ích cho một truy vấn dạng : (SELECT COUNT(*)  FROM baseballStats  WHERE hits > 11)
- Range index là một biến thể của inverted index trong đó thay vì ánh xạ từ giá trị sang cột, ta ánh xạ phạm vi giá trị tới cột.


##3.8 JSON index
- JSON index có thể được áp dụng cho cột chuỗi JSON để tăng tốc độ tra cứu và lọc giá trị cho cột.
- JSON string có thể được sử dụng để biểu diễn mảng, bản đồ và các trường lồng nhau mà không cần phải có một schema cố định. Mặc dù các JSON string rất linh hoạt nhưng việc lọc trên các JSON string rất tốn kém.
- Nếu không có JSON index để tra cứu và lọc các bản ghi dựa trên value thì pinot sẽ phải quét hết và xây dựng lại JSON object từ JSON string cho mỗi bản ghi tra cứu key rồi so sánh giá trị.
- JSON index được thiết kế để tăng tốc quá trình lọc trên các JSON string mà không cần quét và xây dựng lại tất cả các JSON object.
































