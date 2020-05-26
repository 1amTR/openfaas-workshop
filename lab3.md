# Lab 3 - Introduction to functions

- Build, push và deploy function

Trước khi thực hiện, tạo một thư mục mới
```
mkdir -p lab3OF && cd lab3OF
```

## Tạo 1 function mới từ mẫu (template) có sẵn

Mẫu có sẵn trên [Github của OpenFaaS](https://github.com/openfaas/templates)
```
faas-cli template pull
```

Kết quả
```
root@ubuntu:~/lab3OF# faas-cli template pull
Fetch templates from repository: https://github.com/openfaas/templates.git at master
2020/05/26 22:23:28 Attempting to expand templates from https://github.com/openfaas/templates.git
2020/05/26 22:23:32 Fetched 19 template(s) : [csharp csharp-armhf dockerfile go go-armhf java11 java11-vert-x java8 node node-arm64 node-armhf node12 php7 python python-armhf python3 python3-armhf python3-debian ruby] from https://github.com/openfaas/templates.git
root@ubuntu:~/lab3OF# 
```

Liệt kê các ngôn ngữ có hỗ trợ
```
faas-cli new --list
```

Kết quả
```
root@ubuntu:~/lab3OF# faas-cli new --list
Languages available as templates:
- csharp
- csharp-armhf
- dockerfile
- go
- go-armhf
- java11
- java11-vert-x
- java8
- node
- node-arm64
- node-armhf
- node12
- php7
- python
- python-armhf
- python3
- python3-armhf
- python3-debian
- ruby
```

Hoặc, bạn có thể tự tạo cho mình bằng cách tạo 1 thư mục sao đó thêm Dockerfile vào

> `faas-cli new --lang python3` là sử dụng cho Python3

## Hello world in Python

Dùng lệnh để tạo
```
faas-cli new --lang python3 hello-openfaas --prefix="1amtr"
```

Với --prefix="" là tên tài khoản Dockerhub, nếu không thêm ta chỉ cần sửa trong YAML file

Giờ xem cấu trúc của hello-openfaas
```
.
├── hello-openfaas
│   ├── handler.py
│   ├── __init__.py
│   └── requirements.txt
├── hello-openfaas.yml
```

Trong đó: YAML file có chức năng build, push và deploy, tóm lại là config tất cả

```
version: 1.0
provider:
  name: openfaas
  gateway: http://127.0.0.1:8080
functions:
  hello-openfaas:
    lang: python3
    handler: ./hello-openfaas
    image: 1amtr/hello-openfaas:latest
```

- Tên của function nằm dưới dòng function: hello-openfaas
- Ngôn ngữ: lang
- Thư mục dùng để build được gọi là handler, đây là thư mục không phải file
- Tên image được sử dụng cũng là cái được đẩy lên Dockerhub

> Ta có thể sửa gateway bằng cách sửa YAML file này, hoặc trên CLI thì dùng --gateway và `$OPENFAAS_URL`

Vào trong thư mục, hello-openfaas:

File handler.py
```
def handle(req):
    """handle a request to the function
    Args:
        req (str): request body
    """

    return req
```

Hàm này chỉ trả về cái mình nhập vào khi gọi hàm, để sửa thành Hello OpenFaaS
```
    return "Hello OpenFaaS
```

Giờ tiến hành quá trình tạo ra 1 function

- Build

```
faas-cli build -f hello-openfaas.yml
```

- Push

```
faas-cli push -f hello-openfaas.yml
```

![](/screenshot/push.png)

- Deploy

```
faas-cli deploy -f hello-openfaas.yml
```

> Có thể kết hợp 3 lệnh này thành: `faas-cli up -f hello-openfaas.yml`

> Note: có thể lỗi do bạn chưa đăng nhập Dockerhub. Hãy đăng nhập Dockerhub bằng `*docker login*`

> Tips: bạn có thể đổi tên `hello-openfaas.yml` thành `stack.yml` để không cần dùng `-f`

Giờ function này chỉ được gọi qua phương thức `GET` và `POST`

Test bằng `faas-cli invoke`

```
echo | faas-cli invoke hello-openfaas
```

## Một ví dụ hay: astronaut-finder

Chúng ta sẽ tạo ra một function `astronaut-finder` cho phép gọi tên một người nào đó trên Tram Vũ Trụ (ISS)

Bắt đầu
```
faas-cli new --lang python3 astronaut-finder --prefix="1amtr"
```

Thêm vào `astronaut-finder/requirements.txt`
```
requests
```

Update `handler.py`
```
import requests
import random

def handle(req):
    r = requests.get("http://api.open-notify.org/astros.json")
    result = r.json()
    index = random.randint(0, len(result["people"])-1)
    name = result["people"][index]["name"]

    return "%s is in space" % (name)
```

Giờ tạo hàm
```
faas-cli up -f astronaut-finder.yml
```

Invoke hàm
```
echo | faas-cli invoke astronaut-finder
```

![](/screenshot/astronaut-finder.png)

## Troubleshooting: xem logs của container

Bạn có thể xem logs của container chứa hàm đó qua
```
kubectl logs deployment/astronaut-finder -n openfaas-fn
```

![](/screenshot/astronaut-finder-logs.png)

## Troubleshooting: bật chức năng `write_debug`

Để debug kĩ hơn, bạn phải thêm dòng `write_debug`. Sửa trong YAML file `astronaut-finder.yml`

```
version: 1.0
provider:
  name: openfaas
  gateway: http://127.0.0.1:8080
functions:
  astronaut-finder:
    lang: python3
    handler: ./astronaut-finder
    image: 1amtr/astronaut-finder:latest
```

Thêm phần "environment"
```
version: 1.0
provider:
  name: openfaas
  gateway: http://127.0.0.1:8080
functions:
  astronaut-finder:
    lang: python3
    handler: ./astronaut-finder
    image: 1amtr/astronaut-finder:latest
    environment:
      write_debug: true
```

Giờ ta phải deploy lại hàm
```
faas-cli deploy -f astronaut-finder.yml
```

Xem logs lần nữa
```
kubectl logs deployment/astronaut-finder -n openfaas-fn
```

## Quản lý nhiều function

Trong CLI cho phép gộp nhiều function lại thành một, rất tiện lợi khi bạn có nhiều function muốn gộp

Tạo function đầu tiên
```
faas-cli new --lang python3 first
```

Tạo function thứ 2 qua flag `--append`

```
faas-cli new --lang python3 second --append=./first.yml
```

Để tiện, ta đổi tên `first.yml` thành `example.yml`
```
mv first.yml example.yml
```

Giờ xem trong YAML file `example.yml`

```
version: 1.0
provider:
  name: openfaas
  gateway: http://127.0.0.1:8080
functions:
  first:
    lang: python3
    handler: ./first
    image: first:latest

  second:
    lang: python3
    handler: ./second
    image: second:latest
```

Để build 2 function, ta làm như sau
```
faas-cli build -f ./example.yml --parallel=2
```

Build function tùy chọn
```
faas-cli build -f ./example.yml --filter=second
```

## Tùy chọn templates

- Bạn có thể tải template từ 1 link nào đó
```
faas-cli template pull https://github.com/openfaas-incubator/python3-debian
```

Kiểm tra danh sách
```
faas-cli new --list
```

Các mẫu này sẽ được lưu trong thư mục `./templates/`

- Mẫu trên Store

Liệt kê các template đang có, flag `-v` để xem thêm các thông số
```
faas-cli template store list
faas-cli template store list -v
```

Để xem chi tiết một function nào đó, ta dùng `describe`
```
faas-cli template store describe go

root@ubuntu:~/lab3OF# faas-cli template store describe go

Name:              go
Platform:          x86_64
Language:          Go
Source:            openfaas
Description:       Classic Golang template
Repository:        https://github.com/openfaas/templates
Official Template: true

```

Pull template về từ Store
```
faas-cli template store pull go
```

Hết. Qua [lab4](lab4.md)