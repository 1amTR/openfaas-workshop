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