# Lab 1 - Prepare for OpenFaaS

- Cài đặt Docker
- Cài đặt Kubernetes

## Cài đặt Docker

Thực hiện từng lệnh phía dưới
```
apt-get update

apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

apt-get update

apt-get install docker-ce docker-ce-cli containerd.io
```

Chạy hello-world Docker
```
docker run hello-world
```

![alt text](/screenshot/install-docker.png)

## Cài OpenFaaS CLI

Dùng lệnh
```
curl -sLSf https://cli.openfaas.com | sudo sh
```

Kiểm tra
```
faas-cli version
faas-cli help
```

![alt text](/screenshot/openfaas-cli.png)

## Đăng nhập Docker Hub

Để mỗi lần up images thì sẽ tự động đưa lên Docker Hub

```
docker login
```

> Lưu prefix. Chỉnh sửa `~/.bashrc` hoặc `~/.bash_profile` - nếu chưa có thì tạo

```
export OPENFAAS_PREFIX="" # là tên tài khoản Docker hub
```

## Cài đặt Kubernetes

Tiến hành cài đặt
```
export VER=$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)

curl -LO https://storage.googleapis.com/kubernetes-release/release/$VER/bin/linux/amd64/kubectl

chmod +x kubectl

mv kubectl /usr/local/bin/
```            

Kiểm tra
```
kubectl version
```

## Tạo cluster trên local bằng k3d

- Cài đặt k3d (thuộc k3s)

Cài đặt k3d
```
curl -s https://raw.githubusercontent.com/rancher/k3d/master/install.sh | bash
```

Chạy cluster
```
k3d create
```

Kết quả
```
root@ubuntu:~# k3d create
INFO[0000] Created cluster network with ID b010b0317ff51b44b2ac8b865b163af872226614124effdeb3062e454ea46189 
INFO[0000] Created docker volume  k3d-k3s-default-images 
INFO[0000] Creating cluster [k3s-default]               
INFO[0000] Creating server using docker.io/rancher/k3s:v1.17.3-k3s1... 
INFO[0000] Pulling image docker.io/rancher/k3s:v1.17.3-k3s1... 
INFO[0021] SUCCESS: created cluster [k3s-default]       
INFO[0021] You can now use the cluster with:

export KUBECONFIG="$(k3d get-kubeconfig --name='k3s-default')"
kubectl cluster-info 
root@ubuntu:~# 
```

> Bước này bắt đầu để tạo và chạy OpenFaaS

Nếu muốn xóa k3d
```
k3d delete
```

## Triển khai OpenFaaS

Có nhiều cách để chạy ứng dụng OpenFaaS, lab này sử dụng `arkade`

- Cài đặt arkade

```
curl -SLsf https://dl.get-arkade.dev/ | sudo sh
```

- Chạy OpenFaaS

```
arkade install openfaas

# hoặc là

arkade install openfaas --load-balancer
```

Kết quả
```
root@ubuntu:~# arkade install openfaas
Using kubeconfig: /home/irt/.config/k3d/k3s-default/kubeconfig.yaml
Using helm3
Node architecture: "amd64"
Client: "x86_64", "Linux"
2020/05/26 12:25:16 User dir established as: /home/irt/.arkade/
https://get.helm.sh/helm-v3.1.2-linux-amd64.tar.gz
/home/irt/.arkade/bin/helm3/linux-amd64 linux-amd64/
/home/irt/.arkade/bin/helm3/helm linux-amd64/helm
/home/irt/.arkade/bin/helm3/README.md linux-amd64/README.md
/home/irt/.arkade/bin/helm3/LICENSE linux-amd64/LICENSE
2020/05/26 12:25:20 extracted tarball into /home/irt/.arkade/bin/helm3: 3 files, 0 dirs (2.665112089s)
"openfaas" has been added to your repositories
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "openfaas" chart repository
Update Complete. ⎈ Happy Helming!⎈ 
VALUES values.yaml
Command: /home/irt/.arkade/bin/helm3/helm [upgrade --install openfaas openfaas/openfaas --namespace openfaas --values /tmp/charts/openfaas/values.yaml --set gateway.replicas=1 --set ingressOperator.create=false --set queueWorker.maxInflight=1 --set basic_auth=true --set serviceType=NodePort --set clusterRole=false --set gateway.directFunctions=true --set openfaasImagePullPolicy=IfNotPresent --set queueWorker.replicas=1 --set operator.create=false --set faasnetes.imagePullPolicy=Always --set basicAuthPlugin.replicas=1]
Release "openfaas" does not exist. Installing it now.
NAME: openfaas
LAST DEPLOYED: Tue May 26 12:25:25 2020
NAMESPACE: openfaas
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
To verify that openfaas has started, run:

  kubectl -n openfaas get deployments -l "release=openfaas, app=openfaas"
=======================================================================
= OpenFaaS has been installed.                                        =
=======================================================================

# Get the faas-cli
curl -SLsf https://cli.openfaas.com | sudo sh

# Forward the gateway to your machine
kubectl rollout status -n openfaas deploy/gateway
kubectl port-forward -n openfaas svc/gateway 8080:8080 &

# If basic auth is enabled, you can now log into your gateway:
PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)
echo -n $PASSWORD | faas-cli login --username admin --password-stdin

faas-cli store deploy figlet
faas-cli list

# For Raspberry Pi
faas-cli store list \
 --platform armhf

faas-cli store deploy figlet \
 --platform armhf

# Find out more at:
# https://github.com/openfaas/faas

Thanks for using arkade!
root@ubuntu:~# 
```

## Đăng nhập vào OpenFaaS gateway 

Kiểm tra gateay. Báo thành công là được
```
kubectl rollout status -n openfaas deploy/gateway
```

Chạy gateway dưới nền qua cổng 8080
```
kubectl port-forward -n openfaas svc/gateway 8080:8080 &
```

Lệnh này dùng để lấy mật khẩu ra
```
PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)
```

Hiện mật khẩu lên màn hình để đăng nhập ở UI
```
echo $PASSWORD
```

Tiếp theo dùng lệnh này để chứng thực đăng nhập OpenFaaS UI
```
faas-cli login --username admin --password=$PASSWORD
```

![alt text](/screenshot/openfaas-portal.png)

## Lưu OpenFaaS URL

OpenFaaS URL mặc định là: http://127.0.0.1:8080

Chỉnh sửa `~/.bashrc` hoặc `~/.bash_profile` - nếu chưa có thì tạo
```        
export OPENFAAS_URL=""
```

Hết. Qua [lab2](lab2.md)