NKS용 서브넷 과 LB용 서브넷, NATGW용 서브넷을 사전 생성<br>
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/c6dfa2da-00d4-4954-9750-d1f58c3f29bb)<br>

NKS 클러스터 생성<br>
 - subnet 은 17~26 비트 이내로 구성되어야 하며, 172.17.0.0/16은 도커 내부 사용IP로 사용불가<br>
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/eddb11cc-d2c1-4629-a6d3-107460249780)<br>

NodePool 생성<br>
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/4399fb68-0937-4af2-8247-a6236528a2c6)<br>
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/0b55c8d3-d769-427f-9d31-16e030c6d8c4)

인증키는 추가로 생성하거나 기존의 인증키 선택

생성전 스펙 최종 확인<br>
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/78ec33e2-92b4-42d3-acd3-bbd15970fa6b)

NatGW 생성
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/14a342da-41ba-43a5-ba3f-5b56f494b8ad)

routetable 생성 기존라우트 테이블을 이용할 경우 생략
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/67a63954-6b9e-459b-b017-afaed426e834)

쿠버네티스 서브넷이 들어있는 라우트 테이블에서 natgw를 추가 
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/33662c97-5691-47e9-a9d0-08652acae5b0)

쿠버네티스 제어 (로컬 제어시 만 베스천에서 구성시 생략)
- 마이크로소프트 스토어에서 ubuntu LTS를 다운로드, 설치 및 열기<br>
- 재설치간 오류가 발생할시 cmd > wsl --unregister Ubuntu-20.04 와 같이 unregister 명령으로 WSL 설정 삭제 후 다시 설치 진

![image](https://github.com/clabi-lab/kubernetes/assets/142856874/6c6cd8ed-2fa6-4b06-93dd-93ec828b673b)
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/697097fc-f9b2-4183-a3e0-83ff2148beb5)
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/b0cb453c-e4ab-48cc-b7cd-dad3db692351)
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/38e11f06-0dea-4e2d-8480-e48b3cb33507)

설치 완료 후 계정 몇 비밀번호를 생성 후 저장
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/29810583-3b07-4d20-8e89-25741f4fd629)

ncp-iam-authenticator 설치<br>
curl -o ncp-iam-authenticator -L https://github.com/NaverCloudPlatform/ncp-iam-authenticator/releases/latest/download/ncp-iam-authenticator_linux_amd64<br>
chmod +x ./ncp-iam-authenticator<br>
mkdir -p $HOME/bin && cp ./ncp-iam-authenticator $HOME/bin/ncp-iam-authenticator && export PATH=$PATH:$HOME/bin<br>
echo 'export PATH=$PATH:$HOME/bin' >> ~/.profile

kubectl 클라이언트 설치<br>
sudo apt-get update<br>
# apt-transport-https may be a dummy package; if so, you can skip that package<br>
sudo apt-get install -y apt-transport-https ca-certificates curl<br>
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --yes --dearmor -o /usr/share/keyrings/kubernetes-archive-keyring.gpg<br>
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list > /dev/null
sudo apt-get update
sudo apt-get install -y kubectl

API 키 가져오기<br>
포털화면에서, 마이페이지, 계정관리, 인증키 관리 에서 API 키를 생성하거나 기존 키 활용
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/dd520c7d-8611-466d-88b4-e6e808e74937)












