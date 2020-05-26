# Lab 2 - Test things out

- Sử dụng được OpenFaaS UI và deploy function
- Theo dõi số liệu theo thời gian thực

Trước khi thực hiện, tạo một thư mục mới
```
mkdir -p lab2OF && cd lab2OF
```

## Sử dụng UI Portal

Hãy nhớ rằng, OpenFaaS URL mặc định là: http://127.0.0.1:8080

Giờ chúng ta có thể thử một số function viết sẵn và test chúng, tải ở đây:

```
faas-cli deploy -f https://raw.githubusercontent.com/openfaas/faas/master/stack.yml
```

Chúng ta sẽ test function `Markdown`

Nhập Input
```
## The **OpenFaaS** _workshop_
```

Kết quả
```
<h2>The <strong>OpenFaaS</strong> <em>workshop</em></h2>
```

![alt text](/screenshot/test-markdown.png)

Tìm hiểu một số thứ hiển thị trên UI

- Status: trạng thái của function, ban đầu là Not Ready - phải chờ cho đến khi Ready mới sử dụng được function
- Replicas: số lượng bản sao của function đó trong cluster
- Image: là image của function sau khi build, nó được up sẵn trên Docker Hub
- Invocation count: số lượt gọi function, cứ mỗi 5s sẽ reset một lần
- Invoke: khi click nút này sẽ tăng `Invocation count` và function được gọi

## Triển khai function trên Store

Bạn cũng có thể triển khai các function có sẵn trên Store

![alt text](/screenshot/deploy-function-Store.png)

- Click Deploy New Function
- Click From Store
- Click Figlet hoặc nhập figlet thanh tìm kiếm và sau đó click Deploy

## Liệt kê các function

Dùng lệnh
```
faas-cli list
```

Giờ thử lệnh
```
faas-cli list --verbose

# hoặc là

faas-cli list -v
```

> Sẽ thấy tên các Docker images và chi tiết hơn

## Gọi function bằng CLI

Dùng lệnh gọi hàm `markdown`
```
faas-cli invoke markdown
```

Sau đó, bạn chỉ cần nhập text và dùng `Ctrl + D` khi nhập xong

Bạn cũng có thể dùng lệnh đường ống (pipeline)

```
echo Hi | faas-cli invoke markdown

# hoặc 

uname -a | faas-cli invoke markdown
```

# Bảng theo dõi (Monitoring dashboard)

OpenFaaS theo dõi các thông số, số liệu bằng Prometheus. Để theo dõi dễ dàng thì bạn cần cài đặt thêm một bảng theo dõi sử dụng Grafana

- Cài đặt OpenFaaS Grafana trên namespace

Tạo container Grafana
```
kubectl -n openfaas run --image=stefanprodan/faas-grafana:4.6.3 grafana
```

- Deploy container đó mới chạy được
```
kubectl -n openfaas create deploy --image=stefanprodan/faas-grafana:4.6.3 grafana
```

Expose ra cổng 3000 cho Grafana
```
kubectl -n openfaas expose deployment grafana --type=NodePort --port=3000 --name=grafana
```

Cho Grafana chạy nền trên cổng 3000
```
kubectl port-forward deployment/grafana 3000:3000 -n openfaas &
```

URL mặc định sẽ là: http://127.0.0.1:3000

![alt text](/screenshot/grafana-login.png)

Đăng nhập Grafana bằng tài khoản mặc định: `admin/admin` 

![alt text](/screenshot/grafana-dashboard.png)

Hết. Qua [lab3](lab3.md)