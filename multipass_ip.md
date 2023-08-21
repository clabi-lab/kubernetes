### Multipass IP 고정
※ Multipass는 hyper-V의 동작으로 시작되며 새로 부팅될 때마다 IP가 변경된다

※ 이 때문에 VM을 재시작하는 경우 IP가 변경되어 테스트의 지속성이 깨진다

※ 이러한 문제를 해결하기 위해 Multipass의 IP를 고정시키는 방법을 진행한다


#### VM 기본 상태 확인
- Multipass의 VM이 생성되었다면 다음의 명령어로 현재의 네트워크 정보를 확인한다
```
$ ip a
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/b822ef65-d456-43a5-a258-0a3eefbf768b)

- Hyper-V 관리자를 통해 현재 VM의 네트워 어댑터를 확인한다
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/9e34c8de-b2a0-4d96-be47-e842cfc8d976)

- 이상과 같이 default switch에 연결되어 있으며 그 결과 eth0에 대한 네트워크 인터페이스를 확인할 수 있다

#### 새로운 네트워크 스위치 생성
- 이제 새로운 네트워크 스위치를 생성하고 VM에 그 스위치를 연결할 것이다
- VM은 새로운 스위치에서 허용하는 범위 내의 IP를 할당할 것이고 스위치는 VM에 고정된 IP를 허용할 것이다
- 이때 스위치는 NAT 기능을 포함하는 것으로 생성하여 외부로의 인터넷 접근이 가능하도록 구성한다

※ PowerShell 
```
new-vmswitch -switchname "lns" -switchtype internal
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/7fbf6682-060f-4846-a27e-1982954b0411)

- Hyper-V GUI 에서도 다음과 같이 생성하고 확인할 수 있다
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/e18b5cdc-1b12-47b7-a8f7-ae05d93afc76)

- 새로운 스위치에 NAT 게이트웨이 설정 추가
```
New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 92
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/d2b5166e-49b3-4b5a-b456-c2e0ebb05ec7)

※ 이때 추가한 switch의 index 값은 다음과 같이 확인할 수 있다
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/bfe3e0aa-c38c-45ad-a363-f53be6c02513)

- NAT 네트워크 설정
```
New-NetNat -Name lns-NAT -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/925309ad-e5e5-464d-9b49-160c6c2fd23e)

※ NAT는 시스템에 1개만 허용되므로 만일 에러메시지가 나타난다면 기존의 NAT를 삭제한다
-> remove-nat -name XXX


#### VM 에 연결
```
multipass launch -n test1 -c 1 -m 1G
```
- Hyper-v GUI에서 새로운 VM에 lns 스위치를 추가해준다
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/1aae49e9-8bc2-44ce-8174-a2f79b1cd0d1)

- 가상스위치의 combo-box에서 새로 생성한 스위치 'lns'를 선택한다
- multipass의 VM에서 "ip -a" 명령어 확인
```
multipass exec test1 -- /bin/bash -c "ip a"
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/41350087-efc0-40af-8882-eb9ee37d5033)
※ 이제 새로 추가한 네트워크 eth1이 확인된다

#### VM에 고정 IP 설정
- eth1에 고정 IP를 설정한다
```
multipass exec test1 -- /bin/bash -c "sudo vi /etc/netplan/50-cloud-init.yaml"
```
- 기본 구성되어 있는 현재의 설정이 아래와 같이 나타난다
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/42c79a79-9412-4b97-b58f-40d515f22a55)

- eth1에 대한 추가를 다음과 같이 하고 저장한다
```
        eth1:
            addresses: [192.168.0.2/24]
            gateway4: 192.168.0.1
            dhcp4: no
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/f1adc8a2-db03-490b-9c31-cfd93c1bbdc3)

#### VM 재시작/ 확인
```
multipass restart test1
multipass exec test1 -- /bin/bash -c "ip a"
```
![image](https://github.com/clabi-lab/kubernetes/assets/138098979/2b516cfb-8272-479e-a216-5fe9771e6f7e)







