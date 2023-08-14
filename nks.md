### NKS 생성 (콘솔)
-> https://guide.ncloud-docs.com/docs/k8s-k8soverview

![image](https://github.com/clabi-lab/kubernetes/assets/138098979/115b88ca-6f2f-436f-a483-8b10db3bad4d)

#### 생성의 순서/ 절차
##### - 클러스터 설정
- 클러스터 네임
- VPC-Zone-Network type(private/public)-subnet-LB subnet 설정
- 최대 node 수, security 설정 등
##### - 노드 설정
- 노드 풀 네임
- 서버 이미지/ 서버 타입
- 노드 수 등 설정
##### - 로그인 키 설정
##### - Confirm
※ 이상의 절차에 따라 NKS는 간단히 생성된다

#### kubectl client 설치
##### - NKS에 접속/ 접근하기 위한 client의 kubectl를 local에 설치
- WSL2 에 kubectl 설치 (linux 버전의 설치 참조)
- update package
```
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl
```
- register key
```
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://dl.k8s.io/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
- install
```
sudo apt update
sudo apt install -y kubectl
```

#### NKS 클러스터 접속 및 관리
##### - NKS 접근 개념
- NCP의 API Gateway를 통한 API 접근으로 NKS 클러스터에 접근한다
- IAM 계정에 대한 access/secret key를 이용하여 인증을 진행한다
- ncp-iam-authenticator를 통해 kubeconfig를 생성/관리할 수 있다
##### - API key
- NCP 콘솔의 계정 관리에서 로그인 계정의 API 인증키를 확인
- ${HOME}/.ncloud/configure 파일에 인증 키 관리
```
[DEFAULT]
ncloud_access_key_id = 464239241EXXXXXXXXXXX
ncloud_secret_access_key = AD00B1FBAAC921619454XXXXXXXXXXXXXXXXXXX
ncloud_api_url = https://ncloud.apigw.ntruss.com
```
##### - ncp-iam-authenticator
- NKS는 ncp-iam-authenticator를 통해 IAM 인증을 제공하며, 이 인증을 통해 클러스터의 config 파일을 다운로드할 수 있다
- WSL2 에서 설치/ 진행 (리눅스 버전)
```
curl -o ncp-iam-authenticator -L https://github.com/NaverCloudPlatform/ncp-iam-authenticator/releases/latest/download/ncp-iam-authenticator_linux_amd64
chmod +x ./ncp-iam-authenticator
mkdir -p $HOME/bin 
cp ./ncp-iam-authenticator $HOME/bin/ncp-iam-authenticator 
export PATH=$PATH:$HOME/bin
echo 'export PATH=$PATH:$HOME/bin' >> ~/.bash_profile
ncp-iam-authenticator help
```
##### - kubeconfig 다운로드
- 다음의 명령으로 클러스터의 config 파일을 다운로드 받을 수 있다.
```
ncp-iam-authenticator create-kubeconfig --region <region-code> --clusterUuid <cluster-uuid> --output kuecondif.yaml
```
※ clusterUuid는 NKS 콘솔 화면에서 cluster name옆에 확인 가능하다
※ region은 KR-1, KR-2와 같이 '-1'이나 '-2'가 붙지 않는다
※ --debug 옵션을 주어 조금 더 추가적인 정보를 확인할 수 있다

![image](https://github.com/clabi-lab/kubernetes/assets/138098979/705172b7-447a-4b1b-9628-cd3fe3e6e42d)

##### - 클러스터 접근
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/22f5c055-8241-4186-8786-8d5c79af9dea)

![image](https://github.com/clabi-lab/kubernetes/assets/138098979/3b64bccb-ced9-41b4-b279-a4fca93f293e)

![image](https://github.com/clabi-lab/kubernetes/assets/138098979/bced3ba7-41f7-44a4-a88f-fbb5bbae8160)



