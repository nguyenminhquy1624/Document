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
-CMD ['yarn', 'start']
```

- Tạo ra file .dockerignore sẽ bỏ qua các package như node_module
- docker build -t "node_image" .
- docker run -d -p 4000:4000 --name "node_app" "node_image"


## 5. Docker compose
- Tạo file docker-compose.yml 


























