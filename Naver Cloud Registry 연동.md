# 클라우드 레지스트리
## 클라우드 레지스트리를 생성하기 위한 버킷 생성
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/3f381fa0-946e-4790-8014-13bf3fcf3b73)
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/cd9ab614-26bf-49a2-82ad-957668ba9bca)
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/1ba88d5c-69ab-4168-954e-092f29104f37)
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/fee6f5d2-824e-4901-a71c-d11a5530c487)
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/3f29cc6e-b27e-4bf1-b355-ee39df3b9d5b)


## 클라우드 레지스트리 생성
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/180854ab-5bc6-4b25-bc9f-6022f63c571a)
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/1b1819ce-3a10-4312-a7b2-42d0579adf7f)


## 도커 설치
### APT레포지터리를 이용한 설치<br>
- 도커 엔진을 최초 설치하기 위해선 레포지터리에 추가 필요 이후 레포티토리를 이용해 설치 가능

#### 레포지터리 설정
 - APT에서 HTTPS를 이용할 수 있도록 APT 패키지 인덱스 업데이트 및 패키지 설치
```
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
```

#### 도커 공식 GPG key 추가:
```
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

#### 다음명령을 통해 레포지터리 추가:
```
 echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/b8782e48-01da-438a-b575-e926fe6bba60)


#### 패키지 인덱스 업데이트<br>
```
sudo apt-get update
```
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/c73bc17b-7e28-49f0-ab97-0bd6e45307d8)

#### Docker Engine, containerd, Docker Compose 설치<br>

##### 최신 버전 설치 시
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable docker
sudo systemctl enable containerd
sudo systemctl start docker
sudo systemctl start containerd
```
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/b677de03-1bc4-4e25-b522-70509759c690)
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/d194e09f-1f4e-4c08-a0bd-a9500d453e4d)

#### 도커 그룹 설정
```
sudo groupadd docker
sudo usermod -aG docker ${USER}
sudo systemctl restart docker
```


#### 도커 실행 및 동작 확인
```
sudo docker run hello-world
```
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/a917d86f-2eaf-49d5-8850-cbe5166b9ef6)


#### 도커 로그인 
```
docker login -u <access-key-id> <registry-name>.<region-code>.ncr.ntruss.com
Password: <secret-key>
```
Login Succeeded
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/2ff62835-590a-4aab-bb74-f228db237e84)


#### KUBE Secret Repository 연결
```
kubectl --kubeconfig $KUBE_CONFIG create secret docker-registry regcred --docker-server=<registry-end-point> --docker-username=<access-key-id> --docker-password=<secret-key> --docker-email=<your-email>
```
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/35561678-c2af-4ea6-a11c-f6ca42bcc350)


##동작 테스트
```
git clone https://github.com/clabi-lab/hello-clabi.git
cd hello-clabi
docker build -t <registry-end-point>/hello-clabi:1.0 .
docker push <registry-end-point>/hello-clabi:1.0
docker pull <registry-end-point>/hello-clabi:1.0
```

![image](https://github.com/clabi-lab/kubernetes/assets/142856874/5dc49e99-4458-4a61-8133-e47ee29a9454)












