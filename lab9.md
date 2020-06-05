# Lab 9 - Advanced feature - Auto-scaling

```
mkdir -p lab9OF && cd lab9OF
```
## Auto-scaling

*Tham khảo: https://docs.openfaas.com/architecture/autoscaling/*

### Yêu cầu

- Phải hoàn thành [Lab1](lab1.md)
- Sử dụng `curl` cùng với Git Bash

### Background on auto-scaling

Bạn có thể kiếm soát số lượng bản sao scale function thông qua `com.openfaas.scale.min`, giá trị mặc định sẽ là 1. Giá trị lớn nhất `com.openfaas.scale.max` là 20

Nếu `com.openfaas.scale.min` và `com.openfaas.scale.max` cùng một giá trị thì sẽ tắt tính năng auto-scaling

### Check out Prometheus

Để chạy Prometheus ta dùng.

```
kubectl port-forward deployment/prometheus 9090:9090 -n openfaas &
```

Địa chỉ Prometheus mặc định là `http://127.0.0.1:9090`

Bây giờ, thêm graph với lệnh `rate( gateway_function_invocation_total{code="200"} [20s])` như chuỗi truy vấn.

![](/screenshot/prometheus-graph.png)

Bạn có thể xem cảnh báo mức sử dụng các function qua `http://127.0.0.1:9090/alerts`

![](/screenshot/prometheus-alerts.png)

### Trigger scaling bằng Go function

Tạo một function mới `echo-fn` từ anh Alex Ellis

```
git clone https://github.com/alexellis/echo-fn \
 && cd echo-fn \
 && faas-cli template store pull golang-http \
 && faas-cli deploy \
  --label com.openfaas.scale.max=10 \
  --label com.openfaas.scale.min=1
```

Kiểm tra xem `echo-fn` có `Ready` hay chưa
```
faas-cli describe go-echo
```

Bạn có thể theo dõi giá trị `gateway_service_count` bằng Prometheus hoặc qua Gateway API với function được chọn

```
for i in {0..10000};
do
   echo -n "Post $i" | faas-cli invoke go-echo && echo;
done;
```

### Monitor bằng alerts

Kiểm tra alerts bằng http://127.0.0.1:9090/alerts. Bạn sẽ thấy đổi màu dùng để cảnh báo khi vượt ngưỡng

![](/screenshot/prometheus-monitor.png)

### Troubleshooting

Nếu không hoạt động, bạn có thể check
- Alerts trong Prometheus bằng màu sắc
- Check logs trong gateway Prometheus / AlertManager

### Load-testing (optional)

Để kiểm tra hiệu suất, đọc https://docs.openfaas.com/architecture/performance/

Sử dụng `hey` để theo dõi quá trình rõ hơn. Cài đặt

```
sudo apt-get install hey
```

Đo hiệu suất

```
hey -z=30s -q 5 -c 2 -m POST -d=Test http://127.0.0.1:8080/function/go-echo
```

Kết quả
```
root@ubuntu:~/lab9OF# hey -z=30s -q 5 -c 2 -m POST -d=Test http://127.0.0.1:8080/function/go-echo
Handling connection for 8080
Handling connection for 8080


Summary:
  Total:	30.0247 secs
  Slowest:	0.4194 secs
  Fastest:	0.0045 secs
  Average:	0.0213 secs
  Requests/sec:	9.9252
  
  Total data:	1192 bytes
  Size/request:	4 bytes

Response time histogram:
  0.004 [1]	|
  0.046 [272]	|■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■
  0.087 [19]	|■■■
  0.129 [4]	|■
  0.170 [0]	|
  0.212 [0]	|
  0.253 [0]	|
  0.295 [0]	|
  0.336 [0]	|
  0.378 [0]	|
  0.419 [2]	|


Latency distribution:
  10% in 0.0067 secs
  25% in 0.0089 secs
  50% in 0.0132 secs
  75% in 0.0214 secs
  90% in 0.0412 secs
  95% in 0.0548 secs
  99% in 0.4186 secs

Details (average, fastest, slowest):
  DNS+dialup:	0.0000 secs, 0.0045 secs, 0.4194 secs
  DNS-lookup:	0.0000 secs, 0.0000 secs, 0.0000 secs
  req write:	0.0001 secs, 0.0000 secs, 0.0029 secs
  resp wait:	0.0210 secs, 0.0044 secs, 0.4190 secs
  resp read:	0.0001 secs, 0.0000 secs, 0.0016 secs

Status code distribution:
  [200]	298 responses



root@ubuntu:~/lab9OF# 
```

> Trong mô phỏng có 2 người dùng hoạt động với -c, 5 reuqest mỗi giây với -q và thời gian là 30 giây

### Thử scale từ zero

Bạn thử cho function với bản sao bằng 0

```
kubectl scale deployment --replicas=0 nodeinfo -n openfaas-fn

root@ubuntu:~/lab9OF# kubectl scale deployment --replicas=0 nodeinfo -n openfaas-fn
deployment.apps/nodeinfo scaled
```

Kiểm tra
```
kubectl get deployment nodeinfo -n openfaas-fn
```

Sau đó, tăng lên từ 0 lên 1.

```
kubectl scale deployment --replicas=1 nodeinfo -n openfaas-fn

kubectl get deployment nodeinfo -n openfaas-fn
```

Hết. Qua [lab10](lab10.md)