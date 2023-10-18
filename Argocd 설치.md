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

# argocd 환경 설정
## argocd 파드를 노드포트로 변경
```
kubectl patch -n argocd svc argocd-server -p '{"spec": {"type": "NodePort"}}'
```

## argocd ALB ingress controller 설치
```
kubectl apply -f https://raw.githubusercontent.com/NaverCloudPlatform/nks-alb-ingress-controller/main/docs/install/pub/install.yaml
```


