### kubernetes
#### Provisioning

- master node 1대와 worker node 3대로 구성
- 각 node의 spec은 2 cpu, 2G memory, 10G disk storage로 구성한다
```
multipass launch -n k8s-master -c 2 -m 2G -d 10G
multipass launch -n k8s-node-1 -c 2 -m 2G -d 10G
multipass launch -n k8s-node-2 -c 2 -m 2G -d 10G
multipass launch -n k8s-node-3 -c 2 -m 2G -d 10G
```

```
C:\> multipass ls | findstr k8s
```



#### Install Container Runtime
#### Docker
##### - Package
```
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release
```

##### - repo 추가
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

##### - Docker/ Containderd
```
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

sudo systemctl enable docker
sudo systemctl enable containerd

sudo systemctl start docker
sudo systemctl start containerd
```

##### - docker group 설정
```
sudo groupadd docker
sudo usermod -aG docker ${USER}
sudo systemctl restart docker
```
※ 현재 session 종료(로그아웃) 이후 재로그인
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/3359cb80-b5ce-4c79-a5cb-16cf817650ac)

##### - cri-dockerd
※ kubernetes 엔진으로 docker를 사용하기 위해서는 cri-dockerd 어댑터를 반드시 설치해야 한다
※ cri-dockerd 는 go-lang으로 build해야 하므로 go-lang이 설치되지 않은 경우 설치를 진행한다
```
sudo apt install -y golang-go
```

- build cri-dockerd
```
git clone https://github.com/Mirantis/cri-dockerd.git
cd cri-dockerd/
mkdir bin
go build -o bin/cri-dockerd
```
- build가 성공하고 나면 bin/ 디렉토리에 생성된 binary file을 /usr/local/bin으로 설치한다
```
sudo install -o root -g root -m 0755 bin/cri-dockerd /usr/local/bin/cri-dockerd
```
- binray 설치 후에는 service를 등록한다
```
sudo cp packaging/systemd/* /etc/systemd/system
sudo sed -i -e 's,/usr/bin/cri-dockerd,/usr/local/bin/cri-dockerd,' /etc/systemd/system/cri-docker.service
sudo systemctl daemon-reload
sudo systemctl enable cri-docker.service
sudo systemctl enable --now cri-docker.socket
sudo service cri-docker status
```

##### - kubeadm 설치
```
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://dl.k8s.io/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
※ kubelet은 swap on인 경우 정상 동작되지 않는다. 다음과 같은 설정으로 swap를 off 시킨다
```
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```


##### - kubeadm init (only for master node)
```
sudo kubeadm init --cri-socket unix:///var/run/cri-dockerd.sock --pod-network-cidr=10.244.0.0/16
```

![image](https://github.com/clabi-lab/kubernetes/assets/138098979/d400112b-e6bc-4bff-a9ac-ad187a8bde87)
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/6fac086a-59ac-41bf-8e80-7c2f2bce45c8)

```
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join <master node ip>:6443 --token 450a08.elw3p2dt52xgdqjm \
        --discovery-token-ca-cert-hash sha256:92e6fc712ac083694994c3f78ed63fc3bbd08265d6591fdbb4ff230669bc03e7
```

##### - kubeadm join (for worker nodes)
```
sudo kubeadm join <master node ip>:6443  --cri-socket unix:///var/run/cri-dockerd.sock --token xxx --discovery-token-ca-cert-hash sha256:xxxxxxxxxxxxxxxxxx
```

※ --cri-socket 옵션은 2개 이상의 container 설정이 있는 경우 사전 정의할 것을 요구하는 경고가 있기 때문에 사용한다
※ master node의 설치는 kubeadm init 까지를 진행하고 worker node는 위의 모든 과정과 kubeadm init을 제외하고 join을 포함하는 과정이다


