# All

sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
sudo docker run hello-world

sudo usermod -aG docker your-user

sudo apt-get purge docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker

curl -sLSf https://cli.openfaas.com | sudo sh

faas-cli help
faas-cli version

docker login

export OPENFAAS_PREFIX="" # Populate with your Docker Hub username

export VER=$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)
curl -LO https://storage.googleapis.com/kubernetes-release/release/$VER/bin/linux/amd64/kubectl
chmod +x kubectl
mv kubectl /usr/local/bin/

curl -s https://raw.githubusercontent.com/rancher/k3d/master/install.sh | bash

k3d create

export KUBECONFIG="$(k3d get-kubeconfig --name='k3s-default')"

curl -SLsf https://dl.get-arkade.dev/ | sudo sh

arkade install openfaas --load-balancer
arkade install openfaas

kubectl rollout status -n openfaas deploy/gateway
kubectl port-forward svc/gateway -n openfaas 8080:8080
kubectl get svc -o wide gateway-external -n openfaas

export OPENFAAS_URL="" # Populate as above
PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)
echo -n $PASSWORD | faas-cli login --username admin --password-stdin

faas-cli list

mkdir -p lab2OF && cd lab2OF

faas-cli list --verbose
faas-cli list -v

faas-cli invoke markdown

echo Hi | faas-cli invoke markdown

uname -a | faas-cli invoke markdown

kubectl -n openfaas run \
--image=stefanprodan/faas-grafana:4.6.3 \
--port=3000 \
grafana

kubectl -n openfaas create deploy --image=stefanprodan/faas-grafana:4.6.3 grafana

kubectl -n openfaas expose deployment grafana \
--type=NodePort \
--name=grafana

GRAFANA_PORT=$(kubectl -n openfaas get svc grafana -o jsonpath="{.spec.ports[0].nodePort}")

GRAFANA_URL=http://IP_ADDRESS:$GRAFANA_PORT/dashboard/db/openfaas

kubectl port-forward deployment/grafana 3000:3000 -n openfaas

kubectl -n openfaas get pods

kubectl -n openfaas get deploy

kubectl -n openfaas get svc

mkdir -p lab3OF && cd lab3OF

faas-cli template pull

faas-cli new --list

faas-cli new --lang python3 hello-openfaas --prefix=""

faas-cli up -f hello-openfaas.yml

kubectl -n openfaas-fn get pods

kubectl -n openfaas-fn get deploy

kubectl -n openfaas-fn get svc

faas-cli new --lang python3 astronaut-finder --prefix=""

faas-cli build -f ./astronaut-finder.yml

faas-cli push -f ./astronaut-finder.yml

faas-cli deploy -f ./astronaut-finder.yml

echo | faas-cli invoke astronaut-finder

kubectl logs deployment/astronaut-finder -n openfaas-fn

faas-cli new --lang python3 first

faas-cli new --lang python3 second --append=./first.yml

mv first.yml example.yml

faas-cli build -f ./example.yml --parallel=2

faas-cli build -f ./example.yml --filter=second

faas-cli template pull https://github.com/openfaas-incubator/python3-debian

faas-cli new --list | grep python

faas-cli template store list

faas-cli template store list -v

cp -r lab3OF lab4OF && cd lab4OFOF

faas-cli deploy --name env --fprocess="env" --image="functions/alpine:latest"

echo "" | faas-cli invoke env --query workshop=1

curl -X GET $OPENFAAS_URL/function/env/some/path -d ""

curl $OPENFAAS_URL/function/env --header "X-Output-Mode: json" -d ""

faas-cli new --lang python3 ingest-file --prefix=your-name

faas-cli up -f ingest-file.yml

echo "Hello function" > message.txt

mkdir -p lab5 \
   && cd lab5

mkdir -p lab5OF && cd lab5OF

kubectl -n openfaas run \
--image=alexellis2/ngrok-admin \
--port=4040 \
ngrok -- http gateway:8080

kubectl -n openfaas run --image=alexellis2/ngrok-admin --port=4040 ngrok -- http gateway:8080
kubectl -n openfaas run --image=alexellis2/ngrok-admin ngrok http gateway:8080
kubectl -n openfaas create deploy ngrok --image=alexellis2/ngrok-admin http gateway:8080
kubectl -n openfaas expose deployment ngrok --type=NodePort --port=4040 --name=ngrok
kubectl port-forward deployment/ngrok 4040:4040 -n openfaas &

kubectl -n openfaas run --image=alexellis2/ngrok-admin --port=4040 ngrok -- http 8080

kubectl -n openfaas create deploy --image=alexellis2/ngrok-admin --port=4040 ngrok -- http 8080

kubectl -n openfaas delete pods,deploy ngrok
kubectl -n openfaas delete deploy ngrok

kubectl -n openfaas get 
kubectl -n openfaas delete pods,deploy ngrok

kubectl delete pod

iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT

iptables -F INPUT
iptables -F OUTPUT
iptables -F FORWARD
or
iptables -F


kubectl -n openfaas expose deployment ngrok \
--type=NodePort \
--name=ngrok

kubectl port-forward deployment/ngrok 4040:4040 -n openfaas

faas-cli list --gateway https://fuh83fhfj.ngrok.io/

faas-cli new --lang python3 issue-bot --prefix=""

faas-cli up -f ./issue-bot.yml

http://fuh83fhfj.ngrok.io/function/issue-bot

mkdir -p lab6OF && cd lab6OF

faas-cli new --lang python3 show-html --prefix="1amtr"

```
def handle(req):
    """handle a request to the function
    Args:
        req (str): request body
    """

    html = '<html><h2>Hi, from your function!</h2></html>'

    return html
```

mkdir -p lab7OF && cd lab7OF

faas-cli new --lang dockerfile long-task --prefix="1amtr"

```
root@ubuntu:~/lab7OF# kubectl logs deployment/queue-worker -n openfaas

Loading basic authentication credentials
Connect: nats://nats.openfaas.svc.cluster.local:4222
Subscribing to: faas-request at nats://nats.openfaas.svc.cluster.local:4222
Wait for  1m0s
Listening on [faas-request], clientID=[faas-worker-queue-worker-54cfc7d5bc-nr6l4], qgroup=[faas] maxInFlight=[1]
[#1] Invoking: long-task with 0 bytes, via: http://gateway.openfaas.svc.cluster.local:8080/function/long-task/
[#1] Received on [faas-request]: 'sequence:1 subject:"faas-request" data:"{\"Header\":{\"Accept-Encoding\":[\"gzip\"],\"Content-Length\":[\"0\"],\"Content-Type\":[\"text/plain\"],\"User-Agent\":[\"Go-http-client/1.1\"],\"X-Call-Id\":[\"34559711-34c6-4ee4-aa4c-ff64780bce28\"],\"X-Start-Time\":[\"1590694479445876794\"]},\"Host\":\"127.0.0.1:8080\",\"Body\":\"\",\"Method\":\"POST\",\"Path\":\"\",\"QueryString\":\"\",\"Function\":\"long-task\",\"QueueName\":\"\",\"CallbackUrl\":null}" timestamp:1590694479472471790 '
[#1] long-task returned 0 bytes
[#1] Invoked: long-task [200] in 0.023882s
[#2] Invoking: long-task with 0 bytes, via: http://gateway.openfaas.svc.cluster.local:8080/function/long-task/
[#2] Received on [faas-request]: 'sequence:2 subject:"faas-request" data:"{\"Header\":{\"Accept-Encoding\":[\"gzip\"],\"Content-Length\":[\"0\"],\"Content-Type\":[\"text/plain\"],\"User-Agent\":[\"Go-http-client/1.1\"],\"X-Call-Id\":[\"0cdf3973-0bc6-469b-9519-08b5fb877122\"],\"X-Start-Time\":[\"1590694483706492236\"]},\"Host\":\"127.0.0.1:8080\",\"Body\":\"\",\"Method\":\"POST\",\"Path\":\"\",\"QueryString\":\"\",\"Function\":\"long-task\",\"QueueName\":\"\",\"CallbackUrl\":null}" timestamp:1590694483707151982 '
[#2] long-task returned 0 bytes
[#2] Invoked: long-task [200] in 0.013472s
[#3] Received on [faas-request]: 'sequence:3 subject:"faas-request" data:"{\"Header\":{\"Accept-Encoding\":[\"gzip\"],\"Content-Length\":[\"0\"],\"Content-Type\":[\"text/plain\"],\"User-Agent\":[\"Go-http-client/1.1\"],\"X-Call-Id\":[\"9f94364e-fc88-49e2-864c-ec249ff1e949\"],\"X-Start-Time\":[\"1590694485017363666\"]},\"Host\":\"127.0.0.1:8080\",\"Body\":\"\",\"Method\":\"POST\",\"Path\":\"\",\"QueryString\":\"\",\"Function\":\"long-task\",\"QueueName\":\"\",\"CallbackUrl\":null}" timestamp:1590694485022728858 '
[#3] Invoking: long-task with 0 bytes, via: http://gateway.openfaas.svc.cluster.local:8080/function/long-task/
[#3] Invoked: long-task [200] in 0.016299s
[#3] long-task returned 0 bytes
[#4] Received on [faas-request]: 'sequence:4 subject:"faas-request" data:"{\"Header\":{\"Accept-Encoding\":[\"gzip\"],\"Content-Length\":[\"0\"],\"Content-Type\":[\"text/plain\"],\"User-Agent\":[\"Go-http-client/1.1\"],\"X-Call-Id\":[\"455cdceb-2bb5-4482-8529-4dbdc94a88dd\"],\"X-Start-Time\":[\"1590694486036839694\"]},\"Host\":\"127.0.0.1:8080\",\"Body\":\"\",\"Method\":\"POST\",\"Path\":\"\",\"QueryString\":\"\",\"Function\":\"long-task\",\"QueueName\":\"\",\"CallbackUrl\":null}" timestamp:1590694486037268701 '
[#4] Invoking: long-task with 0 bytes, via: http://gateway.openfaas.svc.cluster.local:8080/function/long-task/
[#4] Invoked: long-task [200] in 0.007655s
[#4] long-task returned 0 bytes
[#5] Invoking: long-task with 0 bytes, via: http://gateway.openfaas.svc.cluster.local:8080/function/long-task/
[#5] Received on [faas-request]: 'sequence:5 subject:"faas-request" data:"{\"Header\":{\"Accept-Encoding\":[\"gzip\"],\"Content-Length\":[\"0\"],\"Content-Type\":[\"text/plain\"],\"User-Agent\":[\"Go-http-client/1.1\"],\"X-Call-Id\":[\"487e0639-2421-4006-9e45-7e0c0633ac4f\"],\"X-Start-Time\":[\"1590694486509516818\"]},\"Host\":\"127.0.0.1:8080\",\"Body\":\"\",\"Method\":\"POST\",\"Path\":\"\",\"QueryString\":\"\",\"Function\":\"long-task\",\"QueueName\":\"\",\"CallbackUrl\":null}" timestamp:1590694486510282347 '
[#5] Invoked: long-task [200] in 0.011569s
[#5] long-task returned 0 bytes
[#6] Received on [faas-request]: 'sequence:6 subject:"faas-request" data:"{\"Header\":{\"Accept-Encoding\":[\"gzip\"],\"Content-Length\":[\"0\"],\"Content-Type\":[\"text/plain\"],\"User-Agent\":[\"Go-http-client/1.1\"],\"X-Call-Id\":[\"6463531a-a6fa-4a3c-bc64-8d04874df2bc\"],\"X-Start-Time\":[\"1590694486978346461\"]},\"Host\":\"127.0.0.1:8080\",\"Body\":\"\",\"Method\":\"POST\",\"Path\":\"\",\"QueryString\":\"\",\"Function\":\"long-task\",\"QueueName\":\"\",\"CallbackUrl\":null}" timestamp:1590694486978933181 '
[#6] Invoking: long-task with 0 bytes, via: http://gateway.openfaas.svc.cluster.local:8080/function/long-task/
[#6] long-task returned 0 bytes
[#6] Invoked: long-task [200] in 0.008363s
[#7] Invoking: long-task with 0 bytes, via: http://gateway.openfaas.svc.cluster.local:8080/function/long-task/
[#7] Received on [faas-request]: 'sequence:7 subject:"faas-request" data:"{\"Header\":{\"Accept-Encoding\":[\"gzip\"],\"Content-Length\":[\"0\"],\"Content-Type\":[\"text/plain\"],\"User-Agent\":[\"Go-http-client/1.1\"],\"X-Call-Id\":[\"0ff057fb-cbe4-44d8-a92d-c3bc87d447a4\"],\"X-Start-Time\":[\"1590694487445717774\"]},\"Host\":\"127.0.0.1:8080\",\"Body\":\"\",\"Method\":\"POST\",\"Path\":\"\",\"QueryString\":\"\",\"Function\":\"long-task\",\"QueueName\":\"\",\"CallbackUrl\":null}" timestamp:1590694487446319381 '
[#7] Invoked: long-task [200] in 0.009036s
[#7] long-task returned 0 bytes
[#8] Received on [faas-request]: 'sequence:8 subject:"faas-request" data:"{\"Header\":{\"Accept-Encoding\":[\"gzip\"],\"Content-Length\":[\"0\"],\"Content-Type\":[\"text/plain\"],\"User-Agent\":[\"Go-http-client/1.1\"],\"X-Call-Id\":[\"faec1497-bec8-4765-bee9-61e1f60f3a3d\"],\"X-Start-Time\":[\"1590694487877182423\"]},\"Host\":\"127.0.0.1:8080\",\"Body\":\"\",\"Method\":\"POST\",\"Path\":\"\",\"QueryString\":\"\",\"Function\":\"long-task\",\"QueueName\":\"\",\"CallbackUrl\":null}" timestamp:1590694487878019286 '
[#8] Invoking: long-task with 0 bytes, via: http://gateway.openfaas.svc.cluster.local:8080/function/long-task/
[#8] Invoked: long-task [200] in 0.008546s
[#8] long-task returned 0 bytes
[#9] Received on [faas-request]: 'sequence:9 subject:"faas-request" data:"{\"Header\":{\"Accept-Encoding\":[\"gzip\"],\"Content-Length\":[\"0\"],\"Content-Type\":[\"text/plain\"],\"User-Agent\":[\"Go-http-client/1.1\"],\"X-Call-Id\":[\"d930c714-bd62-45e4-8da1-374d43889ea1\"],\"X-Start-Time\":[\"1590694489249926043\"]},\"Host\":\"127.0.0.1:8080\",\"Body\":\"\",\"Method\":\"POST\",\"Path\":\"\",\"QueryString\":\"\",\"Function\":\"long-task\",\"QueueName\":\"\",\"CallbackUrl\":null}" timestamp:1590694489250436762 '
[#9] Invoking: long-task with 0 bytes, via: http://gateway.openfaas.svc.cluster.local:8080/function/long-task/
[#9] Invoked: long-task [200] in 0.010250s
[#9] long-task returned 0 bytes
[#10] Received on [faas-request]: 'sequence:10 subject:"faas-request" data:"{\"Header\":{\"Accept-Encoding\":[\"gzip\"],\"Content-Length\":[\"0\"],\"Content-Type\":[\"text/plain\"],\"User-Agent\":[\"Go-http-client/1.1\"],\"X-Call-Id\":[\"23056c96-ac69-4746-8e18-7f06b2f94e2c\"],\"X-Start-Time\":[\"1590694489647825967\"]},\"Host\":\"127.0.0.1:8080\",\"Body\":\"\",\"Method\":\"POST\",\"Path\":\"\",\"QueryString\":\"\",\"Function\":\"long-task\",\"QueueName\":\"\",\"CallbackUrl\":null}" timestamp:1590694489648373775 '
[#10] Invoking: long-task with 0 bytes, via: http://gateway.openfaas.svc.cluster.local:8080/function/long-task/
[#10] Invoked: long-task [200] in 0.013108s
[#10] long-task returned 0 bytes
[#11] Received on [faas-request]: 'sequence:11 subject:"faas-request" data:"{\"Header\":{\"Accept-Encoding\":[\"gzip\"],\"Content-Length\":[\"0\"],\"Content-Type\":[\"text/plain\"],\"User-Agent\":[\"Go-http-client/1.1\"],\"X-Call-Id\":[\"7f9e9fa3-6b9d-4b4e-abd8-1d8c2d90819a\"],\"X-Start-Time\":[\"1590694536234747417\"]},\"Host\":\"127.0.0.1:8080\",\"Body\":\"\",\"Method\":\"POST\",\"Path\":\"\",\"QueryString\":\"\",\"Function\":\"long-task\",\"QueueName\":\"\",\"CallbackUrl\":null}" timestamp:1590694536239319131 '
[#11] Invoking: long-task with 0 bytes, via: http://gateway.openfaas.svc.cluster.local:8080/function/long-task/
[#11] long-task returned 0 bytes
[#11] Invoked: long-task [200] in 0.023874s
```

kubectl describe pod -n openfaas ngrok

mkdir -p lab8OF && cd lab8OF

echo | faas-cli invoke sleep-for