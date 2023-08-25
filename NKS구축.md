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









