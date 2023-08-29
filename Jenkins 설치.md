# 젠킨스 설치

## 1. 루트 인증서 추가
- 젠킨스 키 오류 방지 라는데 이유 추가
```
 sudo apt-get -y install ca-certificates
```

## 2. 저장소 키 다운로드 및 저장소 추가
- 2023년 3월 키 변경됨
```
 curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
 echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

## 3. OpenJDK 및 fontConfig 설치
```
 sudo apt-get update
 sudo apt-get -y install fontconfig openjdk-11-jre
```

## 4. Jenkins 설치
```
 sudo apt-get -y install jenkins
 sudo systemctl start jenkins
 sudo systemctl enable jenkins
 sudo systemctl status jenkins
```

## 5. ACG 설정 및 jenkins 초기 화
- ACG로 8080 open
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/02ea5522-931c-407b-a50c-3390f3e7976e)
- 아래 명령어로 초기 패스워드 확인 및 붙여넣기
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

## 6. 마무리
- 이후 젠킨스 url로 접속하여 패키지 설치 및 설정 마무리
  
  
