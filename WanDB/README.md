## 1. Cài đặt 
- ```pip install wandb```
- ```wandb login``` : Login và đăng nhập bằng API key lấy từ trang web của wandb bằng cách tạo project.
- Tạo project đơn giản:
```
import random

total_runs = 5
for run in range(total_runs):
    wandb.init(
      project="basic-intro",
      name=f"experiment_{run}",
      config={
          "learning_rate": 0.02,
          "architecture": "CNN",
          "dataset": "CIFAR-100",
          "epochs": 10,
      }
    )

    epochs = 10
    offset = random.random() / 5
    for epoch in range(2, epochs):
        acc = 1 - 2 ** -epoch - random.random() / epoch - offset
        loss = 2 ** -epoch + random.random() / epoch + offset

        wandb.log({"acc": acc, "loss": loss})

    wandb.finish()
```
- Cấu hình bao gồm:
	- Bước 1 : Khởi tạo wandb bằng ```wandb.init()``` sẽ lưu trữ cấu hình huấn luyện của mô hình. Việc này giúp cho việc phân tích các thử nghiệm và tái sử dụng mô hình dễ dàng hơn.
	- Bước 2 : Trong quá trình huấn luyện, cứ qua 1 epoch ta sẽ bọc kết quả của mô hình bằng wandb.log().
	- Bước 3 : Sau khi huấn luyện xong thì kết thúc wandb bằng wandb.finish()
	- Để xem được chi tiết và trực quan nhất ta có thể vào wandb.ai trên trình duyệt để xem.
	- Chi tiết các thí nghiệm ta có thể xem vào tên của thực nghiệm sẽ cung cấp cho ta về acc, loss dưới dạng biểu đồ, lượng tài nguyên đã sử dụng.


## 2. WanDB trên Notebook
- Cài đặt và login wandb
```!pip install wandb -qqq
import wandb
wandb.login()
```
- Khởi tạo
```
wandb.init(project="jupyter-projo",
           config={
               "batch_size": 128,
               "learning_rate": 0.01,
               "dataset": "CIFAR-100",
           })
```
- Xem W&B trên notebook bằng 
```%wandb evernight/uncategorized```

















	
