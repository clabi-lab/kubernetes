## K3s
### Provisioning
- master node 1대와 worker node 3대로 구성
- 각 node의 spec은 1 cpu, 1G memory, 5G disk storage로 구성한다
```
multipass launch -n k3s-master -c 1 -m 1G
multipass launch -n k3s-node-1 -c 1 -m 1G
multipass launch -n k3s-node-2 -c 1 -m 1G
multipass launch -n k3s-node-3 -c 1 -m 1G
```

```
C:\> multipass ls|findstr k3s
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/71e987c5-cf2e-4fac-8338-36c32c10f822)

- Update package to all
```
multipass exec k3s-master -- /bin/bash -c "sudo apt update"
multipass exec k3s-node-1 -- /bin/bash -c "sudo apt update"
multipass exec k3s-node-2 -- /bin/bash -c "sudo apt update"
multipass exec k3s-node-3 -- /bin/bash -c "sudo apt update"
```

### Install
#### Master node
##### - install k3s
```
multipass exec k3s-master -- /bin/bash -c "curl -sfL https://get.k3s.io | sh -"
multipass exec k3s-master -- /bin/bash -c "service k3s status"
multipass exec k3s-master -- /bin/bash -c "sudo chmod 644 /etc/rancher/k3s/k3s.yaml"
multipass exec k3s-master -- /bin/bash -c "sudo ls -l /etc/rancher/k3s/k3s.yaml"
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/3a8d3083-a456-46ee-b0e2-9ce2879efe00)


##### - master node 설정
- master node의 IP 설정을 k3s service에 등록시켜두지 않은 경우 worker node의 join시 문제가 발생한다
```
multipass exec k3s-master -- /bin/bash -c "sudo sed -i 's/server/server --node-external-ip 172.21.169.208/g' /etc/systemd/system/k3s.service"
multipass exec k3s-master -- /bin/bash -c "sudo cat /etc/systemd/system/k3s.service"
multipass exec k3s-master -- /bin/bash -c "sudo systemctl daemon-reload"
multipass exec k3s-master -- /bin/bash -c "sudo systemctl restart k3s"
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/2d75e69b-d9ee-468f-8e19-874e424ea817)


#### Worker nodes
##### - 사전 확인 사항
- 3개의 worker node에 대해 설치를 진행하고 master node에 join 시킨다
- master node의 IP address와 server token 값을 사전에 확인한다
```
k3s-master              Running           172.21.169.208   Ubuntu 22.04 LTS
```

```
multipass exec k3s-master -- /bin/bash -c "sudo cat /var/lib/rancher/k3s/server/token"
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/8aabc8c7-3198-442f-8c5c-9b2de5c8d93d)


##### - cluster node 확인
```
multipass exec k3s-master -- /bin/bash -c "sudo kubectl get nodes"
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/ecbcd5d0-ba46-4bd3-bdb9-38f25b4f4b9c)


##### - join k3s-node-1 to master node
```
multipass exec k3s-node-1 -- /bin/bash -c "curl -sfL https://get.k3s.io | K3S_URL=https://172.21.169.208:6443 K3S_TOKEN=K10d2da3d1858d0a222d2ebd8068fde4416fcfb1847e9b6f88506fb005639bfd611::server:f7ae2fa2eaaf709a5c66eefcc82db2e1 sh -"
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/0cbb317b-e1e3-4c55-9cd8-9dd0c72be0c9)


#####  - join k3s-node-2 to master node
```
multipass exec k3s-node-2 -- /bin/bash -c "curl -sfL https://get.k3s.io | K3S_URL=https://172.21.169.208:6443 K3S_TOKEN=K10d2da3d1858d0a222d2ebd8068fde4416fcfb1847e9b6f88506fb005639bfd611::server:f7ae2fa2eaaf709a5c66eefcc82db2e1 sh -"
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/10aaf119-d474-45fd-bd98-9d901e1bad90)


#####  - join k3s-node-3 to master node
```
multipass exec k3s-node-3 -- /bin/bash -c "curl -sfL https://get.k3s.io | K3S_URL=https://172.21.169.208:6443 K3S_TOKEN=K10d2da3d1858d0a222d2ebd8068fde4416fcfb1847e9b6f88506fb005639bfd611::server:f7ae2fa2eaaf709a5c66eefcc82db2e1 sh -"
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/479d65b7-2482-4ac6-84f7-f7231c7fc931)



