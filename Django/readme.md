# 1. Django
- Là framework viết bằng python.
- Phiên bản ổn định nhất là 2.0.3
- Cài đặt : ```pip install django```
- Tạo dự án với django : ```django-admin startproject <djangoapp>```
- Cấu trúc của dự án:
	- manage.py : Là file cho phép chúng ta tương tác với dự án theo nhiều cách khác nhau và được sử dụng để quản lý một ứng dụng.
	- djangoapp nằm bên trong là các package ứng dụng. Tên nó là package python mà chúng ta cần sử dụng để import các module bên trong ứng dụng.
	- __init__.py: Đây là khởi tạo trống.
	- settings.py : Tệp này sử dụng để cấu hình cài đặt ứng dụng như kết nối CSDL, liên kết các tệp static,...
	-  ulrs.py : Tệp này chứa các URL được liệt kê của ứng dụng. Trong tệp này chúng ta có thể thêm các url và các hành động tương ứng để thực hiện tác vụ và hiển thị chế độ xem
	- wsgi.py : Đây là khởi đầu cho các máy chủ web tương thích với wsgi để phục vụ dự án django

- Chạy dự án Django: ```python manage.py runserver``` sẽ khởi tạo mặc định với port 8000
- Chúng ta nên tạo một env riêng biệt để chạy dự án Django.
# 2.Django admin
- Django cung cấp module tích hợp để sử dụng CRUD trên mô hình.
- ```django.contrib.admin``` được bật theo mặc định và đã được thêm vào INSTALLED_APPS của phần cài đặt
- Để truy cập vào ta cần vào ```localhost:8000/admin/```
- Để tạo ra tài khoản admin : ```python manage.py createsuperuser```
- Khởi động máy chủ : ```python manage.py runserver```
 
# 3. Tạo app Django
- Để tạo ra một ứng dụng : ```python manage.py startapp <app>```

- Tại file views.py:
```
from django.shortcuts import render 
from django.http import HttpResponse  
def hello(request):  
    return HttpResponse("<h2>Hello, Welcome to Django!</h2>")  
```

- Tạo urls.py tại thư mục app:

```
from django.urls import path  
from . import views
urlpatterns = [  
    path('', views.hello),  
]  
```
- Sau đó vào urls.py tại dự án thêm vào urlpatterns:
``
urlpatterns = [
    ...
    path('home/', include('app.urls')),  
]
```
- Sau đó chạy server lên : ```python manage.py runserver```

#4. Django Model

- Model Django là lớp con của django.db.models.Model và mỗi trường của lớp mô hình đại diện cho một trường CSDL
- Model được xác định trong tệp models.py. Tệp này có thể chứa nhiều mô hình
- Ví dụ về model :
```
from django.db import models  
  
class Employee(models.Model):  
    first_name = models.CharField(max_length=30)  
    last_name = models.CharField(max_length=30)  
```
- Sau đó sử dụng câu lệnh:  ```python manage.py makemigration <name app>``` nó sẽ tạo ra bản migrations với thêm một trường dữ liệu nữa là id.
- Sau đó để cập nhật vào CSDL ta sử dụng : ```python manage.py migrate```

# 5. Views























