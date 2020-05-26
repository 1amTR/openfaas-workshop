# Lab 4 - Go deeper with functions

Trước khi vào lab4, ta cần
```
cp -r lab3OF lab4OF && cd lab4OF
```

## Biến môi trường

**Khi deploy**

Chúng ta đã làm ở [lab 3](lab3.md) - Troubleshooting: bật chức năng `write_debug`.

> Tìm hiểu thêm về các tham số trong environment

**Sử dụng HTTP context - querystring / headers**

- Sử dụng querystring và HTTP headers

Được thực hiện thông qua faas-cli hoặc curl

Tất cả `Http_` và `-` được thay thế bằng `_`

- Deploy một function in các biến environment sử dụng BusyBox command

```
faas-cli deploy --name env --fprocess="env" --image="functions/alpine:latest"
```

Invoke function bằng querystring

```
root@ubuntu:~# echo "" | faas-cli invoke env --query workshop=1
Handling connection for 8080
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=env-5fb9768c77-cvqt6
fprocess=env
KUBERNETES_SERVICE_HOST=10.43.0.1
ASTRONAUT_FINDER_SERVICE_HOST=10.43.78.124
ASTRONAUT_FINDER_PORT_8080_TCP_PORT=8080
NODEINFO_SERVICE_PORT_HTTP=8080
BASE64_SERVICE_PORT=8080
MARKDOWN_PORT_8080_TCP_ADDR=10.43.217.129
ENV_SERVICE_PORT=8080
ENV_PORT=tcp://10.43.38.118:8080
HELLO_OPENFAAS_SERVICE_PORT_HTTP=8080
HUBSTATS_SERVICE_PORT_HTTP=8080
HUBSTATS_PORT_8080_TCP_PROTO=tcp
HUBSTATS_PORT_8080_TCP_ADDR=10.43.158.67
KUBERNETES_PORT_443_TCP=tcp://10.43.0.1:443
MARKDOWN_SERVICE_PORT_HTTP=8080
NODEINFO_SERVICE_PORT=8080
HELLO_OPENFAAS_PORT_8080_TCP_PROTO=tcp
ECHOIT_SERVICE_HOST=10.43.24.236
ECHOIT_PORT_8080_TCP_ADDR=10.43.24.236
WORDCOUNT_PORT_8080_TCP_ADDR=10.43.177.75
BASE64_SERVICE_HOST=10.43.159.2
MARKDOWN_SERVICE_PORT=8080
ASTRONAUT_FINDER_PORT_8080_TCP_PROTO=tcp
ENV_SERVICE_PORT_HTTP=8080
ENV_PORT_8080_TCP=tcp://10.43.38.118:8080
ENV_PORT_8080_TCP_ADDR=10.43.38.118
HUBSTATS_PORT_8080_TCP=tcp://10.43.158.67:8080
ECHOIT_PORT_8080_TCP_PROTO=tcp
BASE64_PORT=tcp://10.43.159.2:8080
KUBERNETES_PORT_443_TCP_ADDR=10.43.0.1
ASTRONAUT_FINDER_PORT_8080_TCP_ADDR=10.43.78.124
ENV_PORT_8080_TCP_PORT=8080
HELLO_OPENFAAS_PORT_8080_TCP=tcp://10.43.240.44:8080
ECHOIT_PORT_8080_TCP=tcp://10.43.24.236:8080
ECHOIT_PORT_8080_TCP_PORT=8080
KUBERNETES_PORT_443_TCP_PROTO=tcp
MARKDOWN_PORT=tcp://10.43.217.129:8080
MARKDOWN_PORT_8080_TCP=tcp://10.43.217.129:8080
NODEINFO_SERVICE_HOST=10.43.84.173
NODEINFO_PORT_8080_TCP_PORT=8080
WORDCOUNT_SERVICE_PORT_HTTP=8080
WORDCOUNT_PORT_8080_TCP_PORT=8080
KUBERNETES_SERVICE_PORT_HTTPS=443
HELLO_OPENFAAS_SERVICE_HOST=10.43.240.44
HELLO_OPENFAAS_PORT_8080_TCP_ADDR=10.43.240.44
KUBERNETES_SERVICE_PORT=443
ASTRONAUT_FINDER_SERVICE_PORT_HTTP=8080
HELLO_OPENFAAS_PORT=tcp://10.43.240.44:8080
HUBSTATS_SERVICE_PORT=8080
ECHOIT_PORT=tcp://10.43.24.236:8080
NODEINFO_PORT_8080_TCP_ADDR=10.43.84.173
ECHOIT_SERVICE_PORT=8080
WORDCOUNT_SERVICE_PORT=8080
BASE64_PORT_8080_TCP_PROTO=tcp
BASE64_PORT_8080_TCP_ADDR=10.43.159.2
MARKDOWN_PORT_8080_TCP_PROTO=tcp
ENV_SERVICE_HOST=10.43.38.118
ENV_PORT_8080_TCP_PROTO=tcp
WORDCOUNT_SERVICE_HOST=10.43.177.75
WORDCOUNT_PORT=tcp://10.43.177.75:8080
BASE64_SERVICE_PORT_HTTP=8080
KUBERNETES_PORT_443_TCP_PORT=443
ASTRONAUT_FINDER_SERVICE_PORT=8080
ASTRONAUT_FINDER_PORT=tcp://10.43.78.124:8080
BASE64_PORT_8080_TCP_PORT=8080
HELLO_OPENFAAS_SERVICE_PORT=8080
HUBSTATS_SERVICE_HOST=10.43.158.67
BASE64_PORT_8080_TCP=tcp://10.43.159.2:8080
MARKDOWN_PORT_8080_TCP_PORT=8080
HELLO_OPENFAAS_PORT_8080_TCP_PORT=8080
HUBSTATS_PORT=tcp://10.43.158.67:8080
HUBSTATS_PORT_8080_TCP_PORT=8080
WORDCOUNT_PORT_8080_TCP_PROTO=tcp
KUBERNETES_PORT=tcp://10.43.0.1:443
NODEINFO_PORT=tcp://10.43.84.173:8080
NODEINFO_PORT_8080_TCP_PROTO=tcp
MARKDOWN_SERVICE_HOST=10.43.217.129
ASTRONAUT_FINDER_PORT_8080_TCP=tcp://10.43.78.124:8080
NODEINFO_PORT_8080_TCP=tcp://10.43.84.173:8080
ECHOIT_SERVICE_PORT_HTTP=8080
WORDCOUNT_PORT_8080_TCP=tcp://10.43.177.75:8080
HOME=/home/app
Http_User_Agent=Go-http-client/1.1
Http_Accept_Encoding=gzip
Http_Content_Type=text/plain
Http_X_Forwarded_For=127.0.0.1:43732
Http_X_Forwarded_Host=127.0.0.1:8080
Http_Method=POST
Http_ContentLength=-1
Http_Content_Length=-1
Http_Query=workshop=1
Http_Path=/
Http_Host=env.openfaas-fn.svc.cluster.local:8080
root@ubuntu:~# 
```

> Trong Python có hàm `os.getenv("Http_Query")`

- Nối đường dẫn (path) đến function

Invoke function với

```
curl -X GET $OPENFAAS_URL/function/env/some/path -d ""
```

> Phải có $OPENFAAS_URL trước, lệnh `export OPENFAAS_URL="http://127.0.0.1:8080"`

```
root@ubuntu:~# curl -X GET $OPENFAAS_URL/function/env/some/path -d ""
Handling connection for 8080
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=env-5fb9768c77-cvqt6
fprocess=env
KUBERNETES_SERVICE_HOST=10.43.0.1
ASTRONAUT_FINDER_SERVICE_HOST=10.43.78.124
ASTRONAUT_FINDER_PORT_8080_TCP_PORT=8080
NODEINFO_SERVICE_PORT_HTTP=8080
BASE64_SERVICE_PORT=8080
MARKDOWN_PORT_8080_TCP_ADDR=10.43.217.129
ENV_SERVICE_PORT=8080
ENV_PORT=tcp://10.43.38.118:8080
HELLO_OPENFAAS_SERVICE_PORT_HTTP=8080
HUBSTATS_SERVICE_PORT_HTTP=8080
HUBSTATS_PORT_8080_TCP_PROTO=tcp
HUBSTATS_PORT_8080_TCP_ADDR=10.43.158.67
KUBERNETES_PORT_443_TCP=tcp://10.43.0.1:443
MARKDOWN_SERVICE_PORT_HTTP=8080
NODEINFO_SERVICE_PORT=8080
HELLO_OPENFAAS_PORT_8080_TCP_PROTO=tcp
ECHOIT_SERVICE_HOST=10.43.24.236
ECHOIT_PORT_8080_TCP_ADDR=10.43.24.236
WORDCOUNT_PORT_8080_TCP_ADDR=10.43.177.75
BASE64_SERVICE_HOST=10.43.159.2
MARKDOWN_SERVICE_PORT=8080
ASTRONAUT_FINDER_PORT_8080_TCP_PROTO=tcp
ENV_SERVICE_PORT_HTTP=8080
ENV_PORT_8080_TCP=tcp://10.43.38.118:8080
ENV_PORT_8080_TCP_ADDR=10.43.38.118
HUBSTATS_PORT_8080_TCP=tcp://10.43.158.67:8080
ECHOIT_PORT_8080_TCP_PROTO=tcp
BASE64_PORT=tcp://10.43.159.2:8080
KUBERNETES_PORT_443_TCP_ADDR=10.43.0.1
ASTRONAUT_FINDER_PORT_8080_TCP_ADDR=10.43.78.124
ENV_PORT_8080_TCP_PORT=8080
HELLO_OPENFAAS_PORT_8080_TCP=tcp://10.43.240.44:8080
ECHOIT_PORT_8080_TCP=tcp://10.43.24.236:8080
ECHOIT_PORT_8080_TCP_PORT=8080
KUBERNETES_PORT_443_TCP_PROTO=tcp
MARKDOWN_PORT=tcp://10.43.217.129:8080
MARKDOWN_PORT_8080_TCP=tcp://10.43.217.129:8080
NODEINFO_SERVICE_HOST=10.43.84.173
NODEINFO_PORT_8080_TCP_PORT=8080
WORDCOUNT_SERVICE_PORT_HTTP=8080
WORDCOUNT_PORT_8080_TCP_PORT=8080
KUBERNETES_SERVICE_PORT_HTTPS=443
HELLO_OPENFAAS_SERVICE_HOST=10.43.240.44
HELLO_OPENFAAS_PORT_8080_TCP_ADDR=10.43.240.44
KUBERNETES_SERVICE_PORT=443
ASTRONAUT_FINDER_SERVICE_PORT_HTTP=8080
HELLO_OPENFAAS_PORT=tcp://10.43.240.44:8080
HUBSTATS_SERVICE_PORT=8080
ECHOIT_PORT=tcp://10.43.24.236:8080
NODEINFO_PORT_8080_TCP_ADDR=10.43.84.173
ECHOIT_SERVICE_PORT=8080
WORDCOUNT_SERVICE_PORT=8080
BASE64_PORT_8080_TCP_PROTO=tcp
BASE64_PORT_8080_TCP_ADDR=10.43.159.2
MARKDOWN_PORT_8080_TCP_PROTO=tcp
ENV_SERVICE_HOST=10.43.38.118
ENV_PORT_8080_TCP_PROTO=tcp
WORDCOUNT_SERVICE_HOST=10.43.177.75
WORDCOUNT_PORT=tcp://10.43.177.75:8080
BASE64_SERVICE_PORT_HTTP=8080
KUBERNETES_PORT_443_TCP_PORT=443
ASTRONAUT_FINDER_SERVICE_PORT=8080
ASTRONAUT_FINDER_PORT=tcp://10.43.78.124:8080
BASE64_PORT_8080_TCP_PORT=8080
HELLO_OPENFAAS_SERVICE_PORT=8080
HUBSTATS_SERVICE_HOST=10.43.158.67
BASE64_PORT_8080_TCP=tcp://10.43.159.2:8080
MARKDOWN_PORT_8080_TCP_PORT=8080
HELLO_OPENFAAS_PORT_8080_TCP_PORT=8080
HUBSTATS_PORT=tcp://10.43.158.67:8080
HUBSTATS_PORT_8080_TCP_PORT=8080
WORDCOUNT_PORT_8080_TCP_PROTO=tcp
KUBERNETES_PORT=tcp://10.43.0.1:443
NODEINFO_PORT=tcp://10.43.84.173:8080
NODEINFO_PORT_8080_TCP_PROTO=tcp
MARKDOWN_SERVICE_HOST=10.43.217.129
ASTRONAUT_FINDER_PORT_8080_TCP=tcp://10.43.78.124:8080
NODEINFO_PORT_8080_TCP=tcp://10.43.84.173:8080
ECHOIT_SERVICE_PORT_HTTP=8080
WORDCOUNT_PORT_8080_TCP=tcp://10.43.177.75:8080
HOME=/home/app
Http_X_Forwarded_For=127.0.0.1:49562
Http_X_Forwarded_Host=127.0.0.1:8080
Http_Accept_Encoding=gzip
Http_User_Agent=curl/7.58.0
Http_Accept=*/*
Http_Content_Type=application/x-www-form-urlencoded
Http_Method=GET
Http_ContentLength=0
Http_Content_Length=0
Http_Path=/some/path
Http_Host=env.openfaas-fn.svc.cluster.local:8080
```

Bạn có thể thấy `Http_Path=/some/path`. Trong Python, dùng code `os.getenv("Http_Path")`

- Invoke function với 1 header

```
curl $OPENFAAS_URL/function/env --header "X-Output-Mode: json" -d ""
```

```
root@ubuntu:~# curl $OPENFAAS_URL/function/env --header "X-Output-Mode: json" -d ""
Handling connection for 8080
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=env-5fb9768c77-cvqt6
fprocess=env
KUBERNETES_SERVICE_HOST=10.43.0.1
ASTRONAUT_FINDER_SERVICE_HOST=10.43.78.124
ASTRONAUT_FINDER_PORT_8080_TCP_PORT=8080
NODEINFO_SERVICE_PORT_HTTP=8080
BASE64_SERVICE_PORT=8080
MARKDOWN_PORT_8080_TCP_ADDR=10.43.217.129
ENV_SERVICE_PORT=8080
ENV_PORT=tcp://10.43.38.118:8080
HELLO_OPENFAAS_SERVICE_PORT_HTTP=8080
HUBSTATS_SERVICE_PORT_HTTP=8080
HUBSTATS_PORT_8080_TCP_PROTO=tcp
HUBSTATS_PORT_8080_TCP_ADDR=10.43.158.67
KUBERNETES_PORT_443_TCP=tcp://10.43.0.1:443
MARKDOWN_SERVICE_PORT_HTTP=8080
NODEINFO_SERVICE_PORT=8080
HELLO_OPENFAAS_PORT_8080_TCP_PROTO=tcp
ECHOIT_SERVICE_HOST=10.43.24.236
ECHOIT_PORT_8080_TCP_ADDR=10.43.24.236
WORDCOUNT_PORT_8080_TCP_ADDR=10.43.177.75
BASE64_SERVICE_HOST=10.43.159.2
MARKDOWN_SERVICE_PORT=8080
ASTRONAUT_FINDER_PORT_8080_TCP_PROTO=tcp
ENV_SERVICE_PORT_HTTP=8080
ENV_PORT_8080_TCP=tcp://10.43.38.118:8080
ENV_PORT_8080_TCP_ADDR=10.43.38.118
HUBSTATS_PORT_8080_TCP=tcp://10.43.158.67:8080
ECHOIT_PORT_8080_TCP_PROTO=tcp
BASE64_PORT=tcp://10.43.159.2:8080
KUBERNETES_PORT_443_TCP_ADDR=10.43.0.1
ASTRONAUT_FINDER_PORT_8080_TCP_ADDR=10.43.78.124
ENV_PORT_8080_TCP_PORT=8080
HELLO_OPENFAAS_PORT_8080_TCP=tcp://10.43.240.44:8080
ECHOIT_PORT_8080_TCP=tcp://10.43.24.236:8080
ECHOIT_PORT_8080_TCP_PORT=8080
KUBERNETES_PORT_443_TCP_PROTO=tcp
MARKDOWN_PORT=tcp://10.43.217.129:8080
MARKDOWN_PORT_8080_TCP=tcp://10.43.217.129:8080
NODEINFO_SERVICE_HOST=10.43.84.173
NODEINFO_PORT_8080_TCP_PORT=8080
WORDCOUNT_SERVICE_PORT_HTTP=8080
WORDCOUNT_PORT_8080_TCP_PORT=8080
KUBERNETES_SERVICE_PORT_HTTPS=443
HELLO_OPENFAAS_SERVICE_HOST=10.43.240.44
HELLO_OPENFAAS_PORT_8080_TCP_ADDR=10.43.240.44
KUBERNETES_SERVICE_PORT=443
ASTRONAUT_FINDER_SERVICE_PORT_HTTP=8080
HELLO_OPENFAAS_PORT=tcp://10.43.240.44:8080
HUBSTATS_SERVICE_PORT=8080
ECHOIT_PORT=tcp://10.43.24.236:8080
NODEINFO_PORT_8080_TCP_ADDR=10.43.84.173
ECHOIT_SERVICE_PORT=8080
WORDCOUNT_SERVICE_PORT=8080
BASE64_PORT_8080_TCP_PROTO=tcp
BASE64_PORT_8080_TCP_ADDR=10.43.159.2
MARKDOWN_PORT_8080_TCP_PROTO=tcp
ENV_SERVICE_HOST=10.43.38.118
ENV_PORT_8080_TCP_PROTO=tcp
WORDCOUNT_SERVICE_HOST=10.43.177.75
WORDCOUNT_PORT=tcp://10.43.177.75:8080
BASE64_SERVICE_PORT_HTTP=8080
KUBERNETES_PORT_443_TCP_PORT=443
ASTRONAUT_FINDER_SERVICE_PORT=8080
ASTRONAUT_FINDER_PORT=tcp://10.43.78.124:8080
BASE64_PORT_8080_TCP_PORT=8080
HELLO_OPENFAAS_SERVICE_PORT=8080
HUBSTATS_SERVICE_HOST=10.43.158.67
BASE64_PORT_8080_TCP=tcp://10.43.159.2:8080
MARKDOWN_PORT_8080_TCP_PORT=8080
HELLO_OPENFAAS_PORT_8080_TCP_PORT=8080
HUBSTATS_PORT=tcp://10.43.158.67:8080
HUBSTATS_PORT_8080_TCP_PORT=8080
WORDCOUNT_PORT_8080_TCP_PROTO=tcp
KUBERNETES_PORT=tcp://10.43.0.1:443
NODEINFO_PORT=tcp://10.43.84.173:8080
NODEINFO_PORT_8080_TCP_PROTO=tcp
MARKDOWN_SERVICE_HOST=10.43.217.129
ASTRONAUT_FINDER_PORT_8080_TCP=tcp://10.43.78.124:8080
NODEINFO_PORT_8080_TCP=tcp://10.43.84.173:8080
ECHOIT_SERVICE_PORT_HTTP=8080
WORDCOUNT_PORT_8080_TCP=tcp://10.43.177.75:8080
HOME=/home/app
Http_User_Agent=curl/7.58.0
Http_X_Output_Mode=json
Http_Accept_Encoding=gzip
Http_Content_Length=0
Http_Accept=*/*
Http_Content_Type=application/x-www-form-urlencoded
Http_X_Forwarded_For=127.0.0.1:53030
Http_X_Forwarded_Host=127.0.0.1:8080
Http_Method=POST
Http_ContentLength=0
Http_Path=/
Http_Host=env.openfaas-fn.svc.cluster.local:8080
```

Bạn có thể thấy `Http_X_Output_Mode=json`. Trong Python, dùng `os.getenv("Http_X_Output_Mode")`

Và nhiều thứ hay ho khác như `Content-Length`, `Http_Method`, `User_Agent`

## Security: hệ thống tập tin read-only

Tạo một hàm lưu file vào filesystem

```
faas-cli new --lang python3 ingest-file --prefix="1amtr"
```

Update handler
```
import os
import time

def handle(req):
    # Read the path or a default from environment variable
    path = os.getenv("save_path", "/home/app/")

    # generate a name using the current timestamp
    t = time.time()
    file_name = path + str(t)

    # write a file
    with open(file_name, "w") as f:
        f.write(req)
        f.close()

    return file_name
```

Build
```
faas-cli up -f ingest-file.yml
```

Chạy thử
```
echo "Hello function" > message.txt

cat message.txt | faas-cli invoke -f ingest-file.yml ingest-file

root@ubuntu:~/lab4OF# cat message.txt | faas-cli invoke -f ingest-file.yml ingest-file
Handling connection for 8080
/home/app/1590519506.846731
root@ubuntu:~/lab4OF# 
```

Các file được lưu vào đường dẫn `/home/app`

Giờ ta chỉnh sửa cho ingest-file.yml thành read-only
```
...
version: 1.0
provider:
  name: openfaas
  gateway: http://127.0.0.1:8080
functions:
  ingest-file:
    lang: python3
    handler: ./ingest-file
    image: 1amtr/ingest-file:latest
    readonly_root_filesystem: true
```

Deploy lại
```
faas-cli up -f ingest-file.yml
```

Kiểm tra lại
```
echo "Hello function" > message.txt

cat message.txt | faas-cli invoke -f ingest-file.yml ingest-file
```

Bạn sẽ thấy lỗi ghi
![](/screenshot/ingest-file.png)

Để ghi vào vùng tạm thời (tmp), ta dùng `save_path`

```
functions:
  ingest-file:
    lang: python3
    handler: ./ingest-file
    image: 1amtr/ingest-file:latest
    readonly_root_filesystem: true
      environment:
        save_path: "/tmp/"
```

Build lại, ta sẽ thấy

```
faas-cli up -f ingest-file.yml
```

![](/screenshot/ingest-file-tmp.png)

## Sử dụng để đăng nhập

Tận dụng `hello-openfaas` ở lab3

Thay đổi code `handler.py`

```
import sys
import json

def handle(req):

    sys.stderr.write("This should be an error message.\n")
    return json.dumps({"Hello": "OpenFaaS"})
```

Build và deploy
```
faas-cli up -f hello-openfaas.yml
```

Chạy function
```
echo | faas-cli invoke hello-openfaas
```

Kết quả
```
root@ubuntu:~/lab4OF# echo | faas-cli invoke hello-openfaas
Handling connection for 8080
This should be an error message.
{"Hello": "OpenFaaS"}
root@ubuntu:~/lab4OF# 
```

> Check logs sẽ không thấy stderr `kubectl logs deployment/hello-openfaas -n openfaas-fn`

Giờ chỉnh sửa trong `hello-openfaas.yaml`. Thêm
```
environment:
    combine_output: false
```

Deploy và gọi function lại. Kết quả
```
root@ubuntu:~/lab4OF# echo | faas-cli invoke hello-openfaas
Handling connection for 8080
{"Hello": "OpenFaaS"}
root@ubuntu:~/lab4OF# 
```

Không còn báo lỗi. Check trong logs
```
root@ubuntu:~/lab4OF# kubectl logs deployment/hello-openfaas -n openfaas-fn
2020/05/26 19:17:38 Version: 0.18.1	SHA: b46be5a4d9d9d55da9c4b1e50d86346e0afccf2d
2020/05/26 19:17:38 Timeouts: read: 5s, write: 5s hard: 0s.
2020/05/26 19:17:38 Listening on port: 8080
2020/05/26 19:17:38 Writing lock-file to: /tmp/.lock
2020/05/26 19:17:38 Metrics listening on port: 8081
2020/05/26 19:17:45 Forking fprocess.
2020/05/26 19:17:45 stderr: This should be an error message.
2020/05/26 19:17:45 Wrote 22 Bytes - Duration: 0.139890 seconds
```

Đã có! `2020/05/26 19:17:45 stderr: This should be an error message.`

## Tạo Workflows

Đầu ra của function này sẽ là đầu vào của function khác.

- Chức năng xâu chuỗi ở client-side

Bạn có thể dùng `curl` và `faas-cli` để test

Ví dụ: deploy một function NodeInfo trong Store, sau đó chuyển đầu ra NodeInfo vào đầu vào Markdown

```
root@ubuntu:~/lab4OF# echo -n "" | faas-cli invoke nodeinfo | faas-cli invoke markdown
Handling connection for 8080
Handling connection for 8080
<p>Hostname: nodeinfo-c48b59ddc-q5wlv</p>

<p>Platform: linux
Arch: x64
CPU count: 1
Uptime: 51902</p>
```

- Gọi một function từ function khác

Ví dụ

```
faas-cli store deploy SentimentAnalysis
```

Qua [lab5](lab5.md)