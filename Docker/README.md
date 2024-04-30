# I. Install Docker
## 1. Install Docker
```
sudo apt-get update

sudo apt-get install \
	ca-certificates \
	curl \
	gnupg \
	lsb-release

sudo mkdir -m 0755 -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 2. Install Docker Compose
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```

# II. Docker
## 1. Các lệnh Docker
- docker --version : Kiểm tra version của docker
- docker pull nginx : download image nginx về
- docker run -it -p 8080:80 nginx : Chạy app với docker với port ở trong nginx này là 80 và khởi chạy trên máy ta là cổng 8080.
- docker ps : Xem tất cả các container đang chạy
- docker images : Xem tất cả các image có trên máy
- docker ps -a : Liệt kê các docker đã dừng
- docker rm "container_id" : Xóa container
- docker rmi "image id" : Xóa image
- docker run -d -p 8080:80 nginx : Chạy dưới dạng background và ta sẽ tiếp tục sử dụng terminal hiện tại và nó sẽ chỉ in ra id của container hiện tại và không hiện quá trình chạy
- docker stop "name id" : Sẽ dừng lại container đang chạy
- docker start "name id" : Sẽ khởi chạy lại container.
- docker run -d -p 8080:80 --name "my name" "my image" : Sử dụng để đặt tên cho container 
- docker exec -it "name container" bash : Nó sẽ đi vào bên trong container và ta sẽ thao tác lệnh ở trong đó.
- docker run -d -p 8080:80 --name "name" -v "path file muốn add":"path file gốc" "image": Dùng để add folder vào để thay thế mặc định trong container.
- docker run -d -p 8080:80 --name "myname" -v "path local":"path container" -v "path log local":"path log container" : Sẽ tạo ra một container lấy log từ trong container và cập nhật chạy vào trong local.

## 2. Dockerfile
- Là hướng dẫn để cho Docker biết đường để tạo cho ta 1 container từ image gốc và có nội dung nào đấy để thay đổi
- Viết vào Dockerfile

```
FROM nginx  

COPY "html" "/usr/share/nginx/html"
```

- Chạy lệnh ```docker build -t "my_nginx"[:version] .``` : Sẽ tự build tạo ra mọi thứ
- Chạy lệnh ```docker run -d -p 8080:80 --name "name container" "name image"   

## 3. Dockerhub
- docker login : để login vào docker hub
- docker tag "name image" username/name_image[:tag] :  
- docker push username/image[:tag]
- docker pull "repo image"

## 4. Project node với DOcker

- Tạo project node như bình thường
- Sau đó tạo ra 1 file Dockerfile ở trong project:

```
FROM node:slim
WORKDIR /usr/src/app
ENV W_C_M="Welcome to VN"
COPY package.json .
COPY yarn.lock .
RUN yarn install COPY . .
EXPOSE 4000
CMD ['yarn', 'start']
```

- Tạo ra file .dockerignore sẽ bỏ qua các package như node_module
- docker build -t "node_image" .
- docker run -d -p 4000:4000 --name "node_app" "node_image"


## 5. Docker compose

### 5.1 Chạy với 1 image
- Tạo file docker-compose.yml và điền các thông tin:
```
version : '3'
services:
	node_image:
		build:
			context: .
			dockerfile: Dockerfile
		container_name : node_app
		ports:
			- '4000:4000'
```

- Giải thích : node_image là tên của image, context là nơi mà nó tìm thấy tất cả các file để nó build, dockerfile là tên của file Dockerfile để nó tìm, container_name đặt tên cho container để build, port là cổng chạy trên máy và trên container
- Chạy lệnh ```docker-compose up -d``` để build image và container
- Chạy lệnh ```docker-compose down``` để dừng và xóa container


### 5.2 Chạy với nhiều image
- docker/entrypoint.sh : Liệt kê các câu lệnh sau khi bật container
- Dockerfile : quy định docker image được khởi tạo từ đầu gồm những gì
- docker-compose.yml : Dùng khai báo và điều phối hoạt động của container trong dự án
- Viết docker-compose.yml:
```
version : '3.5'
services :
	mysql:
		image : mysql:5.7
		container_name: mysql
		restart: always
		environment:
			MYSQL_ROOT_PASSWORD: root
		volumes:
			- docker/database:/var/lib/mysql
	app:
		container_name: app
		build : 
			- context : .
			- dockerfile : Dockerfile
		volumes:
			- .:/my_app
		ports:
			-"3000:3000"
		environment:
			DATABASE_HOST: mysql
			DATABASE_USER_NAME: root
			DATABASE_PASSWORD: root
```

- Với image là tên image chỉ định để khởi động container, container_name là chỉ định tên container ta đặt, restart với giá trị mặc định là no còn ta đặt là always thì sẽ khởi động lại nếu mã thoát cho biết lỗi không thành công, enviroment là các biến môi trường, volumes là chia sẻ dữ liệu giữa container và host hoặc giữa các container với nhau
- build là ta xây dựng container từ Dockerfile chứ không có sẵn nữa, ports là cấu hình cổng kết nối (HOST:CONTAINER), environment là các biến môi trường

- Viết trong file Dockerfile

```
# Image cơ sở
FROM ruby:2.5.1

# Cài đặt các phần mềm cho máy ảo
RUN apt-get update && \
  apt-get install -y nodejs nano vim
  
# Set timezone cho máy ảo
ENV TZ=Asia/Ho_Chi_Minh
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Chỉ định thư mục làm việc
ENV APP_PATH /my_app
WORKDIR $APP_PATH

# Cài đặt framework cần thiết cho dự án
COPY Gemfile Gemfile.lock $APP_PATH/
RUN bundle install --without production --retry 2 \
  --jobs `expr $(cat /proc/cpuinfo | grep -c "cpu cores") - 1`
  
# Copy tất cả dữ liệu từ máy host vào container
COPY . $APP_PATH

# Cấu hình file entrypoint.sh
COPY docker/entrypoint.sh /usr/bin/
RUN chmod +x /usr/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]
EXPOSE 3000

# Thiết lập mặc định sau khi khởi động container
CMD ["rails", "server", "-b", "0.0.0.0"]
```


- Cấu hình file entrypoint.sh
```
#!/bin/bash
set -e

# Remove a potentially pre-existing server.pid for Rails.
rm -f /my_app/tmp/pids/server.pid

# Then exec the container's main process use CMD (what"s set as CMD in the Dockerfile).
exec "$@"
```
- Sau đó chạy các câu lệnh sau:
```
docker-compose run app rails new . --force --no-deps --database=mysql
```

## 6. Một số khái niệm
- RUN : sử dụng để thực thi một câu lệnh nào đó trong quá trình build images
- CMD : Để thực thi một câu lệnh trong quá trình bật container. Nếu có nhiều câu lệnh CMD thì nó chỉ chạy câu lệnh CMD cuối
- ENTRYPOINT : Để thực thi một số câu lệnh trong quá trình start container được viết trong file .sh
- EXPOSE : Container sẽ lắng nghe trên các cổng mạng được chỉ định khi chạy
- ADD : Copy file, thư mục, remote file thêm chúng vào filesystem của image
- COPY : Copy file, thư mục từ host và image
- WORKDIR : Định nghĩa directory cho CMD
- VOLUME : Mount thư mục từ máy host vào container



## 7. Makefile

- Viết vào Makefile:
```
up: 
	docker-compose up -d mysql redis worker
dev:
	docker-compose run --rm -p 3000:3000 app rails s
```
- Sau đó dùng make up để start các container background và make dev để start main container

# II Docker tutorial

- ```docker``` : sẽ hiển thị các lệnh nhỏ bên trong docker
- ```docker image --help``` : sẽ hướng dẫn về các lệnh con trong ```docker image```
- ```docker images``` :  xem tất cả các images có trên máy
- ```docker pull``` : 


















