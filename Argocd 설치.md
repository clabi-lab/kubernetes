# Argocd 설치
## 공식 홈페이지에서 제공하는 yaml로 설치
- kubectl은 이미 설치가 완료된 상태
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## 초기 비밀번호 확인
```
kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/0734353f-7d2d-4687-ae95-1bd2cb3e49b6)


## 초기 비밀번호 변경
```
kubectl exec -it -n argocd deployment/argocd-server -- /bin/bash #Argocd 파드 접속
#Argocd pod 내에서
argocd account update-password #argocd cli 사용 password 변경

```
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/2bfd68f6-e683-420e-b2b1-9e27c1a5a78e)

# 인증서 생성
## Url 발급 후 DNS 설정
- Url을 신규 발급 하거나 기존 url에 DNS 연동
- 사진은 가비아 DNS 설정 예제 a 레코드로 베스천이나 외부 접근이 가능한 서버 지정
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/c95c1048-4e91-474f-ac08-a388c1db6ac3)

## 인증서 생성
```
snap install --classic certbot
certbot --standalone -d [위에서 생성한 url] certonly
```
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/f2e82727-3727-4db0-a782-bd4a8edb8c6f)

## 인증서 Ncloud에 등록
- private.pem -> private Key
- cert.pem -> Public Key Certificate
- fullchain.pem -> Certification chain



# argocd 환경 설정
## argocd 파드를 노드포트로 변경
```
kubectl patch -n argocd svc argocd-server -p '{"spec": {"type": "NodePort"}}'
```

## argocd ALB ingress controller 설치
```
kubectl apply -f https://raw.githubusercontent.com/NaverCloudPlatform/nks-alb-ingress-controller/main/docs/install/pub/install.yaml
```

## argocd-ingress.yaml 생성 및 적용
```
echo "apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: argocd-alb-ingress
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80},{"HTTPS":443}]'
	alb.ingress.kubernetes.io/backend-protocol: HTTPS
    alb.ingress.kubernetes.io/ssl-certificate-no: "8374"
    alb.ingress.kubernetes.io/actions.ssl-redirect: |
      {"type":"redirection","redirection":{"port": "443","protocol":"HTTPS","statusCode":301}}
  labels:
    app: argocd-alb-ingress
  name: argocd-alb-ingress
spec:
  backend:
    serviceName: argocd-server
    servicePort: 80
  rules:
    - http:
        paths:
          - backend:
              serviceName: ssl-redirect
              servicePort: use-annotation
    - host: argocd.clabitest.store
      http:
        paths:
          - backend:
              serviceName: argocd-server
              servicePort: 443" | sudo tee ./argocd-ingress.yaml
```



