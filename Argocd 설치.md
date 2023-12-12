# Argocd 설치
## 공식 홈페이지에서 제공하는 yaml로 설치
- kubectl은 이미 설치가 완료된 상태
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## curl로 다운로드해서 수정하여 적용하는것도 가능
```
curl https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml -o argo-cd.yaml
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
argocd login argocd-server
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
sudo apt-get update
sudo apt-get install certbot
sudo certbot certonly -d "*.sample.com" --manual --preferred-challenge dns
```
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/9980e9f9-4fb5-4a5c-a479-bb8ad70652d3)
- 위 certbot에서 생성된 acme challenge를 DNS 서버에 TXT로 입력
- https://toolbox.googleapps.com/apps/dig/#TXT/_acme-challenge.sample.com. 에서 TXT로 위 값이 조회 될 때까지 대기 (약5분 이내)
![image](https://github.com/clabi-lab/kubernetes/assets/142856874/ee761a0f-ff6f-48b0-8d27-303709799f00)


## 인증서 Ncloud에 등록
- private.pem -> private Key
- cert.pem -> Public Key Certificate
- fullchain.pem -> Certification chain



# argocd 환경 설정
## TLS 해제
```
kubectl edit cm argocd-cmd-params-cm -n argocd
```
## 서비스 NodePort 로 변경
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
```

- VI 에디터에서 data: server.insecure: "true" 항목 추가
```
apiVersion: v1
data:
  server.insecure: "true"
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ConfigMap","metadata":{"annotations":{},"labels":{"app.kubernetes.io/name":"argocd-cmd-params-cm","app.kubernetes.io/part-of":"argocd"},"name":"argocd-cmd-params-cm","namespace":"argocd"}}
  creationTimestamp: "2023-09-15T06:36:22Z"
  labels:
    app.kubernetes.io/name: argocd-cmd-params-cm
    app.kubernetes.io/part-of: argocd
  name: argocd-cmd-params-cm
  namespace: argocd
  resourceVersion: "<리소스번호>"
  uid: <uid 번호>
```

## ingress 생성
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80},{"HTTPS":443}]'
    alb.ingress.kubernetes.io/ssl-certificate-no: "인증서번호"
    alb.ingress.kubernetes.io/actions.ssl-redirect: |
      {"type":"redirection","redirection":{"port": "443","protocol":"HTTPS","statusCode":301}}
  labels:
    app: argocd-alb-ingress
  name: argocd-alb-ingress
  namespace: argocd
spec:
  ingressClassName: alb
  defaultBackend:
    service:
      name: argocd-server
      port:
        number: 80
  rules:
  - http:
      paths:
      - path: /*
        pathType: Prefix
        backend:
          service:
            name: ssl-redirect
            port:
              name: use-annotation
      - path: /*
        pathType: Prefix
        backend:
          service:
            name: argocd-server
            port:
              number: 80
```

# DNS 등록
- LB 주소로 CNAME 등록

