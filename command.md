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

