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
```
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
```from django.db import models  
class Employee(models.Model):  
    first_name = models.CharField(max_length=30)  
    last_name = models.CharField(max_length=30)  
```
- Sau đó sử dụng câu lệnh:  ```python manage.py makemigration <name app>``` nó sẽ tạo ra bản migrations với thêm một trường dữ liệu nữa là id.
- Sau đó để cập nhật vào CSDL ta sử dụng : ```python manage.py migrate```

# 5. Views

- Ví dụ trong file views.py trong thư mục mytime:
```
import datetime  
# Create your views here.  
from django.http import HttpResponse  
def index(request):  
    now = datetime.datetime.now()  
    html = "<html><body><h3>Now time is %s.</h3></body></html>" % now  
    return HttpResponse(html)    # rendering the template in H
```

- Tạo urls.py tại thư mục mytime:

```
from django.urls import path  
from . import views
urlpatterns = [  
    path('', views.index),  
]  
```
- Sau đó vào urls.py tại dự án thêm vào urlpatterns:
```
urlpatterns = [
    ...
    path('time/', include('mytime.urls')),  
]
```
- Sau đó chạy server lên : ```python manage.py runserver```


# 6. HTTP Decorator

- Tại file views.py

```
from django.shortcuts import render
from django.http import HttpResponse, HttpResponseNotFound
from django.views.decorators.http import require_http_methods

@require_http_methods(["GET"])  
def show(request):  
    return HttpResponse('<h1>This is Http GET request.</h1>')
```

- Tương tự thêm vào urls và khởi động lên


#7. Django Template

- Config tại file setting trong dự án : ```'DIRS': [os.path.join(BASE_DIR,'templates')]```
- Tạo folder templates trong app. Sau đó tạo ra file HTML
- Viết trong file views.py
```
from django.shortcuts import render  
from django.template import loader  
from django.http import HttpResponse  

def index(request):  
   template = loader.get_template('index.html')
   return HttpResponse(template.render())
```
- Tương tự trong các file urls.py
- Ngoài ra ta có thể truyền vào các biến để hiển thị.
- Mã hóa csrf_token sử dụng trong phương thức POST : ```{% csrf_token %}```
- Câu lệnh if viết trong HTML :
```
{% if user.is_authenticated %}  
    Hello, {{ user.username }}.  
{% endif %}  
```
# 8. Django Static File
- Tạo ra folder static ở trong app chứa các file css và js
- Tại file base thì sử dụng ```{% load static %}```
- Tại vị trí nào sử dụng static file thì {% static 'path' %}

# 9. Model Form
- Tại model.py
```
from __future__ import unicode_literals  
from django.db import models  
  
class Student(models.Model):  
    first_name = models.CharField(max_length=20)  
    last_name  = models.CharField(max_length=30)  
    class Meta:  
        db_table = "student"  
```

- File form.py
```
from django import forms  
from myapp.models import Student  
  
class EmpForm(forms.ModelForm):  
    class Meta:  
        model = Student  
        fields = "__all__" 
```

- File views.py
```
from django.shortcuts import render  
from myapp.form import StuForm  
  
def index(request):  
    stu = EmpForm()  
    return render(request,"index.html",{'form':stu})  
```
- Trong file html : Sử dụng csrf_token trong form method POST và {{form.as_p}} để lấy tất cả các trường dữ liệu form
- Sau đó tạo urls như trên.


# 10. Form Validation

- Tạo models.py
- Trong forms.py tạo ra class Meta kế thừa Class trong models
- Tại file views.py định nghĩa hàm is_valid()
```
def emp(request):  
    if request.method == "POST":  
        form = EmployeeForm(request.POST)  
        if form.is_valid():  
            try:  
                return redirect('/')  
            except:  
                pass  
    else:  
        form = EmployeeForm()  
    return render(request,'index.html',{'form':form})  
```


# 11. Create, Read, Update, Delete

## 1. Tại file models.py:
```
from django.db import models  
class Employee(models.Model):  
    eid = models.CharField(max_length=20)  
    ename = models.CharField(max_length=100)  
    eemail = models.EmailField()  
    econtact = models.CharField(max_length=15)  
    class Meta:  
        db_table = "employee"  
```
## 2. Tại file forms.py :
```
from django import forms  
from employee.models import Employee  
class EmployeeForm(forms.ModelForm):  
    class Meta:  
        model = Employee  
        fields = "__all__"  
```
## 3. Tại file views.py :
```
from django.shortcuts import render, redirect  
from employee.forms import EmployeeForm  
from employee.models import Employee  

#Create
def emp(request) :
	if request.method=="POST":
		form = EmployeeForm(request.POST)
		if form.is_valid():
			try:
				form.save()
				return redirect('/')
			exception:
				pass
		else:
			form = EmployeeForm()
		return render(request, 'index.html',{'form':form})
# Read
def show(request):
	employees = Employee.objects.all()
	return render(request, 'show.html', {'employees':employees})

# Update
def edit(request, id):
	employee = Employee.object.get(id=id)
	return (request, 'edit.html',{'empoyee':employee})
def update(request, id):
	employee = Employee.object.get(id=id)
	form = EmployeeForm(request.POST, instance= employee)
	if form.is_valid():
		form.save()
		return redirect('show/')
	return render(request,'edit.html',{'employee':employee})
	
# Delete
def delete(request, id):
	employee = Employee.object.get(id=id)
	employee.delete()
	return redirect('show')
```

## 4. Tại file urls.py:
- Config trong app
```
from django.contrib import admin  
from django.urls import path  
from employee import views  
urlpatterns = [  
    path('emp/', views.emp),  
    path('show/',views.show),  
    path('edit/<int:id>', views.edit),  
    path('update/<int:id>', views.update),  
    path('delete/<int:id>', views.destroy),  
]  
```
- Config urls.py trong dự án






































