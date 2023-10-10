# 도커 구축
- runtime 설치
```
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release
```
- repo 추가
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- docker 설치
```
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

sudo systemctl enable docker
sudo systemctl enable containerd

sudo systemctl start docker
sudo systemctl start containerd
```
- 현재 계정에 docker 그룹 권한 부여 ( 세션 재접속 필요 )
```
sudo groupadd docker
sudo usermod -aG docker ${USER}
sudo systemctl restart docker
```
- Go 설치
```
wget https://go.dev/dl/go1.21.2.linux-amd64.tar.gz
sudo tar -C /usr/local -xvf go1.21.2.linux-amd64.tar.gz
mkdir ~/go
echo "export GOROOT=/usr/local/go" >> ~/.profile
echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.profile
echo "export GOPATH=$HOME/go" >> ~/.profile
source ~/.profile
echo $GOPATH
```
- cri-dockerd 빌드, 설치
```
go build -o bin/cri-dockerd
sudo install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
```
- cri-dockerd 서비스등록
```
sudo cp packaging/systemd/* /etc/systemd/system
sudo sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
sudo systemctl daemon-reload
sudo systemctl enable cri-docker.service
sudo systemctl enable --now cri-docker.socket
sudo service cri-docker status
```
- kubeadm 설치
```
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://dl.k8s.io/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
- swap off
```
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```
- kubeinit ( master 노드 )
```
sudo kubeadm init --cri-socket unix:///var/run/cri-dockerd.sock --pod-network-cidr=10.244.0.0/16
```
