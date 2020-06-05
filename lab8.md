# Lab 8 - Advanced feature - Timeouts

Trước khi vào Lab, ta phải tạo thư mục trước
```
mkdir -p lab8OF && cd lab8OF
```

# Kéo dài thời gian với `read_timeout`

Quản lý timeout bằng biến môi trường

- Function timeout
- `read_timeout` - thời gian cho phép đọc request function qua HTTP
- `write_timeout` - thời gian cho phép ghi response function qua HTTP
- `exec_timeout` - thời gian tối đa mà function chạy trước khi bị tắt

Kiểm soát thời gian bằng function, trong đó API Gateway cho thời gian mặc định là 20 giây

```
faas-cli new --lang python3 sleep-for --prefix="1amtr"
```

Sửa `handler.py`

```
import time
import os

def handle(req):
    """handle a request to the function
    Args:
        req (str): request body
    """

    sleep_duration = int(os.getenv("sleep_duration", "10"))
    preSleep = "Starting to sleep for %d" % sleep_duration
    time.sleep(sleep_duration)  # Sleep for a number of seconds
    postSleep = "Finished the sleep"
    return preSleep + "\n" + postSleep
```

Sửa `sleep-for.yml` file và thêm biến môi trường

```
provider:
  name: openfaas
  gateway: http://127.0.0.1:8080

functions:
  sleep-for:
    lang: python3
    handler: ./sleep-for
    image: 1amtr/sleep-for:latest
    environment:
      sleep_duration: 10
      read_timeout: "5s"
      write_timeout: "5s"
      exec_timeout: "5s"
```

Giờ up function và gọi

```
faas-cli up -f sleep-for.yml

echo | faas-cli invoke sleep-for
```

![](/screenshot/sleep-for-1.png)

Bạn sẽ thấy function không in ra thông báo, do `sleep_duration` cao hơn `timeout values`

Bây giờ, set `sleep_duration` lại thành 2. Up function

```
faas-cli up -f sleep-for.yml

echo | faas-cli invoke sleep-for
```

![](/screenshot/sleep-for-2.png)

- API Gateway

Cập nhật gateway value, set `gateway.readTimeout` và `gateway.writeTimeout` trong `values.yaml` hoặc sử tham số trong Helm `--set gateway.readTimeout=value` và `--set gateway.writeTimeout=value`

Hết. Qua [lab9](lab9.md)