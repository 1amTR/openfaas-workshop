# Lab 11 - Advanced feature - Trust with HMAC

Chuẩn bị môi trường. Tạo thư mục
```
mkdir -p lab11OF && cd lab11OF
```

Để đảm bảo thì phải từ version 0.7.4 trở lên. Kiểm tra version
```
faas-cli version
```
## HMAC là gì

...

## Sử dụng HMAC

Trong đó phải có 2 flag: --sign và --key

Tạo hàm
```
faas-cli deploy --name env --fprocess="env" --image="functions/alpine:latest"
```

Gọi function mà không cần --sign flag
```
echo "The message" | faas-cli invoke env

oot@ubuntu:~# echo "The message" | faas-cli invoke env
Handling connection for 8080
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=env-5fb9768c77-sh5tt
fprocess=env
GO_ECHO_SERVICE_PORT=8080
GO_ECHO_PORT=tcp://10.43.179.136:8080
NODEINFO_PORT_8080_TCP_PROTO=tcp
ENV_SERVICE_HOST=10.43.118.231
FIGLET_PORT_8080_TCP_ADDR=10.43.14.37
KUBERNETES_SERVICE_PORT_HTTPS=443
SLEEP_FOR_SERVICE_HOST=10.43.165.205
NODEINFO_PORT_8080_TCP_PORT=8080
FIGLET_PORT_8080_TCP_PROTO=tcp
ENV_PORT=tcp://10.43.118.231:8080
ENV_PORT_8080_TCP_PROTO=tcp
ENV_PORT_8080_TCP_ADDR=10.43.118.231
KUBERNETES_PORT_443_TCP_ADDR=10.43.0.1
SLEEP_FOR_SERVICE_PORT_HTTP=8080
SLEEP_FOR_PORT_8080_TCP_PORT=8080
FIGLET_SERVICE_PORT_HTTP=8080
ENV_SERVICE_PORT=8080
GO_ECHO_PORT_8080_TCP_PROTO=tcp
FIGLET_PORT_8080_TCP_PORT=8080
KUBERNETES_PORT=tcp://10.43.0.1:443
GO_ECHO_SERVICE_PORT_HTTP=8080
GO_ECHO_PORT_8080_TCP=tcp://10.43.179.136:8080
NODEINFO_PORT_8080_TCP_ADDR=10.43.165.248
FIGLET_PORT=tcp://10.43.14.37:8080
NODEINFO_PORT=tcp://10.43.165.248:8080
KUBERNETES_SERVICE_PORT=443
KUBERNETES_PORT_443_TCP_PROTO=tcp
GO_ECHO_SERVICE_HOST=10.43.179.136
NODEINFO_SERVICE_HOST=10.43.165.248
NODEINFO_SERVICE_PORT=8080
FIGLET_SERVICE_HOST=10.43.14.37
ENV_SERVICE_PORT_HTTP=8080
ENV_PORT_8080_TCP=tcp://10.43.118.231:8080
KUBERNETES_PORT_443_TCP=tcp://10.43.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
SLEEP_FOR_PORT=tcp://10.43.165.205:8080
NODEINFO_SERVICE_PORT_HTTP=8080
NODEINFO_PORT_8080_TCP=tcp://10.43.165.248:8080
SLEEP_FOR_PORT_8080_TCP=tcp://10.43.165.205:8080
KUBERNETES_SERVICE_HOST=10.43.0.1
SLEEP_FOR_PORT_8080_TCP_ADDR=10.43.165.205
FIGLET_SERVICE_PORT=8080
FIGLET_PORT_8080_TCP=tcp://10.43.14.37:8080
SLEEP_FOR_PORT_8080_TCP_PROTO=tcp
GO_ECHO_PORT_8080_TCP_PORT=8080
GO_ECHO_PORT_8080_TCP_ADDR=10.43.179.136
ENV_PORT_8080_TCP_PORT=8080
SLEEP_FOR_SERVICE_PORT=8080
HOME=/home/app
Http_Content_Type=text/plain
Http_X_Forwarded_For=127.0.0.1:50948
Http_X_Forwarded_Host=127.0.0.1:8080
Http_User_Agent=Go-http-client/1.1
Http_Accept_Encoding=gzip
Http_Method=POST
Http_ContentLength=-1
Http_Content_Length=-1
Http_Path=/
Http_Host=env.openfaas-fn.svc.cluster.local:8080
```

Gọi lần nữa nhưng sử dụng --sign flag
```
echo -n "The message" | faas-cli invoke env --sign=HMAC --key=cookie

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=env-5fb9768c77-sh5tt
fprocess=env
GO_ECHO_SERVICE_PORT=8080
GO_ECHO_PORT=tcp://10.43.179.136:8080
NODEINFO_PORT_8080_TCP_PROTO=tcp
ENV_SERVICE_HOST=10.43.118.231
FIGLET_PORT_8080_TCP_ADDR=10.43.14.37
KUBERNETES_SERVICE_PORT_HTTPS=443
SLEEP_FOR_SERVICE_HOST=10.43.165.205
NODEINFO_PORT_8080_TCP_PORT=8080
FIGLET_PORT_8080_TCP_PROTO=tcp
ENV_PORT=tcp://10.43.118.231:8080
ENV_PORT_8080_TCP_PROTO=tcp
ENV_PORT_8080_TCP_ADDR=10.43.118.231
KUBERNETES_PORT_443_TCP_ADDR=10.43.0.1
SLEEP_FOR_SERVICE_PORT_HTTP=8080
SLEEP_FOR_PORT_8080_TCP_PORT=8080
FIGLET_SERVICE_PORT_HTTP=8080
ENV_SERVICE_PORT=8080
GO_ECHO_PORT_8080_TCP_PROTO=tcp
FIGLET_PORT_8080_TCP_PORT=8080
KUBERNETES_PORT=tcp://10.43.0.1:443
GO_ECHO_SERVICE_PORT_HTTP=8080
GO_ECHO_PORT_8080_TCP=tcp://10.43.179.136:8080
NODEINFO_PORT_8080_TCP_ADDR=10.43.165.248
FIGLET_PORT=tcp://10.43.14.37:8080
NODEINFO_PORT=tcp://10.43.165.248:8080
KUBERNETES_SERVICE_PORT=443
KUBERNETES_PORT_443_TCP_PROTO=tcp
GO_ECHO_SERVICE_HOST=10.43.179.136
NODEINFO_SERVICE_HOST=10.43.165.248
NODEINFO_SERVICE_PORT=8080
FIGLET_SERVICE_HOST=10.43.14.37
ENV_SERVICE_PORT_HTTP=8080
ENV_PORT_8080_TCP=tcp://10.43.118.231:8080
KUBERNETES_PORT_443_TCP=tcp://10.43.0.1:443
KUBERNETES_PORT_443_TCP_PORT=443
SLEEP_FOR_PORT=tcp://10.43.165.205:8080
NODEINFO_SERVICE_PORT_HTTP=8080
NODEINFO_PORT_8080_TCP=tcp://10.43.165.248:8080
SLEEP_FOR_PORT_8080_TCP=tcp://10.43.165.205:8080
KUBERNETES_SERVICE_HOST=10.43.0.1
SLEEP_FOR_PORT_8080_TCP_ADDR=10.43.165.205
FIGLET_SERVICE_PORT=8080
FIGLET_PORT_8080_TCP=tcp://10.43.14.37:8080
SLEEP_FOR_PORT_8080_TCP_PROTO=tcp
GO_ECHO_PORT_8080_TCP_PORT=8080
GO_ECHO_PORT_8080_TCP_ADDR=10.43.179.136
ENV_PORT_8080_TCP_PORT=8080
SLEEP_FOR_SERVICE_PORT=8080
HOME=/home/app
Http_Accept_Encoding=gzip
Http_Content_Type=text/plain
Http_Hmac=sha1=9239edfe20185eafd7a5513c303b03d207d22f64
Http_X_Forwarded_For=127.0.0.1:52710
Http_X_Forwarded_Host=127.0.0.1:8080
Http_User_Agent=Go-http-client/1.1
Http_Method=POST
Http_ContentLength=-1
Http_Content_Length=-1
Http_Path=/
Http_Host=env.openfaas-fn.svc.cluster.local:8080
```

## HMAC in action

Tạo function mới tên hmac-protected
```
faas-cli new --lang python3 hmac-protected --prefix=
```

Thêm `payload-secret` vào là chìa khóa quan trọng. Tạo `payload-secret`

```
echo -n "1 am TR" | faas-cli secret create payload-secret
```

Giờ sửa `hmac-protected.yml`
```
provider:
  name: openfaas
  gateway: http://127.0.0.1:8080

functions:
  hmac-protected:
    lang: python3
    handler: ./hmac-protected
    image: 1amtr/hmac-protected:latest
    secrets:
      - payload-secret
```

Thay đổi code trong `handler.py`
```
import os, hmac, hashlib

def validateHMAC(message, secret, hash):

    # GitHub and the sign flag prefix the hash with "sha1="
    receivedHash = getHash(hash)

    # Hash message with secret
    expectedMAC = hmac.new(secret.encode(), message.encode(), hashlib.sha1)
    createdHash = expectedMAC.hexdigest()

    return receivedHash == createdHash

def getHash(hash):
    if "sha1=" in hash:
        hash=hash[5:]
    return hash

def handle(req):
    # We receive the hashed message in form of a header
    messageMAC = os.getenv("Http_Hmac")

    # Read secret from inside the container
    with open("/var/openfaas/secrets/payload-secret","r") as secretContent:
        payloadSecret = secretContent.read()

    # Function to validate the HMAC
    if validateHMAC(req, payloadSecret, messageMAC):
        return "Successfully validated: " + req
    return "HMAC validation failed."
```

Up function
```
faas-cli up -f hmac-protected.yml
```

## Invoke function

Hai cách để gọi
- Gọi bình thường
- Gọi mà thêm flag `--key`

Gọi với `--key`
```
echo -n "This is a message" | faas-cli invoke hmac-protected --sign hmac --key=<your-secret>
```

Kết quả
```
Successfully validated: This is a message
```

Thử nhập sai `--key`
```
echo -n "This is a message" | faas-cli invoke hmac-protected --sign hmac --key=wrongkey
HMAC validation failed.
```

Đã hoàn thành Labs. Giờ quay lại [trang chính](README.md)