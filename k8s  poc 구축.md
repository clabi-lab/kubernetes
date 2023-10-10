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

