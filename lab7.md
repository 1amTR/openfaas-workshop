# Lab 7 - Asynchronous functions

Trước khi vào Lab, chúng ta cần có một thư mục
```
mkdir -p lab7OF && cd lab7OF
```

## Function synchronously (đồng bộ) và asynchronously (không đồng bộ)

Synchronously (đồng bộ): sẽ giữ cho kết nối và dành toàn bộ thời gian cho việc kết nối, các function khác sẽ không hoạt động cho đến khi function hiện tại hoàn thành

- URL có dạng `/function/<function_name>`
- Phải đợi cho đến hàm thực hiện nhiệm vụ xong
- Nhận được kết quả sau khi call
- Biết được thành công hay thất bại khi chạy function

Asynchronous (bất đồng bộ) cũng vậy nhưng khác

- URL có dạng `/async-function/<function_name>`
- Client được nhận phản hồi `202 Accepted` ngay
- Function được gọi sau khi sử dụng `queue-worker` 
- Kết quả mặc định bị loại bỏ

Làm thử một cái demo nhỏ

```
faas-cli new --lang dockerfile long-task --prefix="1amtr"
```

Sửa `long-task/Dockerfile` và thay đổi  fprocess thành `sleep 1`

![](/screenshot/fprocess-long-task.png)

Giờ up cho function và test 10 lần
```
echo -n "" | faas-cli invoke long-task
echo -n "" | faas-cli invoke long-task
echo -n "" | faas-cli invoke long-task
echo -n "" | faas-cli invoke long-task
echo -n "" | faas-cli invoke long-task
echo -n "" | faas-cli invoke long-task
echo -n "" | faas-cli invoke long-task
echo -n "" | faas-cli invoke long-task
echo -n "" | faas-cli invoke long-task
echo -n "" | faas-cli invoke long-task
```

Invoke function 10 lần bất đồng bộ
```
echo -n "" | faas-cli invoke long-task --async
echo -n "" | faas-cli invoke long-task --async
echo -n "" | faas-cli invoke long-task --async
echo -n "" | faas-cli invoke long-task --async
echo -n "" | faas-cli invoke long-task --async
echo -n "" | faas-cli invoke long-task --async
echo -n "" | faas-cli invoke long-task --async
echo -n "" | faas-cli invoke long-task --async
echo -n "" | faas-cli invoke long-task --async
echo -n "" | faas-cli invoke long-task --async
echo -n "" | faas-cli invoke long-task --async
```

Kết quả: với `Synchronously` chúng ta sẽ nhận được kết quả ngay sau 1 giây. Còn với `Asynchronous` sẽ bị hoãn lại khi chúng ta echo 10 lần liên tiếp, lần lượt các lần echo sẽ nằm trong hàng đợi và phải đợi cho echo đầu tiên thực hiện xong mới qua echo thứ 2

## Xem logs của queue-worker

Ngăn xếp (stack) của OpenFaaS sử dụng NATS Streaming để thực hiện bất động bộ. Xem logs
```
kubectl logs deployment/queue-worker -n openfaas
```

## Sử dụng X-Callback-Url

- Thử X-Callback-Url trên requestbin

Truy cập https://requestbin.com/

> Lưu ý khi tạo, tắt `Private` để không cần đăng nhập

Lưu lại endpoint có dạng là https://enpcdblqxt6d.x.pipedream.net/

```
echo -n "LaterIsBetter" | faas-cli invoke figlet --async --header "X-Callback-Url=https://enpcdblqxt6d.x.pipedream.net/"
```

Với câu lệnh này sẽ in ra `LaterIsBetter` dạng figlet. Sau đó sẽ gửi lên requestbin.

![](/screenshot/figlet-requestbin.png)

Logs lab7: https://gist.github.com/1amTR/268d8b2a27b771811428c5360c5f32ce

- Thử X-Callback-Url trên PostBin

Truy cập https://postb.in/

```
echo -n "LaterIsBetter" | faas-cli invoke figlet --async --header "X-Callback-Url=https://postb.in/1590796436110-3719567898660"
```

> Ở postbin không hỗ trợ xem RAW nên không thấy được, nhưng ta vẫn thấy các thông số cần thiết

![](/screenshot/postbin-long-task.png)

Hết. Qua [lab8](lab8.md)