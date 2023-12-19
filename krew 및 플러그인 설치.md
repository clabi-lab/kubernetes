# 쿠버네티스 플러그인 매니져 KREW 설치
## 리눅스에서 아래 명령 실행
```
(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)
```
## 환경변수 등록
```
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"
```

## 기타 플러그인 설치
```
kubectl krew install tree rolesum sort-manifest open-svc view-serviceaccount-kubececonfig
```

## 플러그인 종류
- tree 리소스 계층관계 표시
- neat  kubectl get -o yaml 등에서 불필요한 필드 삭제
- sick-pods Ready되지 않은 파드의 목록과 이유를 표시
- podevents 파드의 이벤트 정보를 출력
- resource-capacity 클러스터의 리소스 사용률을 표시한다.
- get-all 모든 리소스를 가져옴
- sort-manifest 입력한 매니페스트를 의존 관계를 기준으로 정렬한다.
- ctx kubectx 플러그인
- ns kubens 플러그인
- images 사용하고 있는 컨테이너 이미지를 출력한다.
- outdated 사용하고 있는 컨테이너 이미지에 새로운 버젼이 있는지를 표시한다.
- open-svc ClusterIP 서비스에 대해 부하분산이 가능한 상태의 접속을 허용한다.
- iexec kubectl exe를 실행하는 파드를 대화형으로 선택한다.
- tmux-exec tmux를 사용하여 여러 컨테이너에서 명령어를 실행한다.
- cssh tmux를 사용하여 여러 쿠버네티스 노드에 ssh
- node-shell 쿠버네티스 노드에 셀을 기동한다. ( nsenter 이용 )
- node-restart 쿠버네티스 노드를 재기동한다.
- view-secret Base64 디코드 된 상태에서 시크릿 표시
- modify-secret Base64 디코드 된 상태에서 kubectl edit 한다.
- konfig kubeconfig를 병합한다.
- view-serviceaccount-kubeconfig 특정 serviceaccount의 kubeconfig를 생성한다.
- rolesum Serviceaccount나 Group에 할당된 RBAC설정값을 행렬로 시각화 한다.
- who-can 지정한 권한을 가진 serviceaccount나 사용자를 표시한다.
