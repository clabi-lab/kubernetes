
## Minikube 
### Provisioning
- 설치 최소 사양 : cpu 2, memory 2GB, disk 20GB

```
C:\>multipass launch -n minikube -c 2 -m 2G -d 20G
Launched: minikube
Skipping mount due to disabled mounts feature

C:\> multipass shell minikube
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/a6fed10a-57ee-41bc-b5e2-518f8396bf92)


### Install
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
sudo apt install -y docker-ce docker-ce-cli containerd.io

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


#### Minikube
##### - Download
```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube
```

##### - install minikube
```
sudo mkdir -p /usr/local/bin/
sudo install minikube /usr/local/bin/
```

##### - Configuration
```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sudo sysctl --system
```

##### - update package
```
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl
```

##### - register key
```
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://dl.k8s.io/apt/doc/apt-key.gpg

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
※ 기존의 k8s 설치에 사용한 url (https://packages.cloud.google.com/apt/doc/apt-key.gpg)이 유효하지 않다

##### - update package
```
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

##### - start
```
minikube start --network-plugin=cni --cni=bridge --container-runtime=containerd  --bootstrapper=kubeadm
```

##### - 완료
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/e3d28486-4c32-41ac-bd4a-d002972e894f)


##### -dashboard
```
multipass exec minikube -- /bin/bash -c "minikube dashboard"
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/6fb39b24-fdab-4418-a28a-5572f37ecaa2)

※ 외부(local host) 에서 minikube의 dashboard 로 접근하기 위해서는 proxy 설정이 필요
```
multipass exec minikube -- /bin/bash -c "kubectl proxy --address='0.0.0.0' --disable-filter=true
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/f31764d9-532d-4fea-ab09-8832cd2f1faf)

- 브라우저 접속

![image](https://github.com/clabi-lab/kubernetes/assets/138098979/6ec7bdf5-9769-49e4-9311-9d50ddce34b3)





