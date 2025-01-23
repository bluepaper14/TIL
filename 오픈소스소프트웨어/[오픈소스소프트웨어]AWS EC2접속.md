# [오픈소스소프트웨어]AWS EC2접속
Q.클라우딩 컴퓨팅을 하는 이유

- 사용한 만큼 비용 납부
- 어디든지 서비스를 구축가능
- 비즈니스에 따라 확장가능
- 신뢰성과 가용성이 좋음

이제 클라우드 컴퓨팅의 아마존 웹 서비스를 이용해보자.
![](https://velog.velcdn.com/images/bluepaper14/post/6408e6f7-87b9-4502-8692-9a0e20d286b9/image.png)회원가입하고 콘솔홈으로 이동한다.
(대학생일 경우 보통 IAM사용자가 아닌 루트 사용자를 이용함-> 학교 이메일)

오른쪽 상단의 지역을 확인할 수 있는데,
> 아시아 태평양(서울) ap-northeast-2

서울로 선택해주면된다. AWS가 지역마다 가용영역이 있는데, 현재 서울은 4개의 가용 영역이 있다.
> ap-northeast-2a
ap-northeast-2b
ap-northeast-2c
ap-northeast-2d

예를 들어 4개의 서버가 있을때, 하나의 가용영역에 전부 설치할 수도 있고 각각 분산시켜서 설치할 수 있다. 각각의 장단점이 있는데, 분산시켜 서비스를 운영하면 안전의 **가용성**은 있지만 네트워크 **비용의 복잡성**이 단점이 되기도 한다.


---
## 1-1 Cost Management 설정

AWS는 유지비용에 대한 지출이 있기때문에 한도를 설정할 수 있다. 
콘솔홈 위젯의 [비용 및 사용량] -> [과금 정보 및 비용 관리 홈] -> [예산] 으로 들어간다.
![](https://velog.velcdn.com/images/bluepaper14/post/582631fd-814a-4fb6-b688-fd1f0deaa579/image.png) 이후 수신 받을 이메일과 템플릿을 설정하게 되면
![](https://velog.velcdn.com/images/bluepaper14/post/99220dae-bc48-40e2-b14f-e1661f2c6c6b/image.png) 이렇게 예산에 대한 한도 설정이 되었다. 

---
## 1-2 Free Tier 설정
제품을 사용할때 여러가지 상품이 있다.
아래는 대표적인 12개월 무료 제품 5개다.
- 컴퓨팅: Amazon EC2 / 리눅스 윈도우 가상 서버 사용
- 스토리지 : Amazon Elastic Block Storage / 저장소
- 데이터베이스 : Amazon RDS / 관계형 데이터베이스
- 스토리지 : Amazon S3 / 사진이나 동영상 저장 관련 스토리지(Simple Stroage Service)
- 컴퓨팅 : Elastic Load Blancing / 여러 서비스 서버들의 트래픽 부하를 막음

---
## 1-3 Key Pair 생성
Ec2를 기준으로 가상 서버를 만들 것이다.
Ec2에서는 공개키 암호가 사용된다.
- public key -> Amazon에서 가지고 있음
- private key -> 내가 가지고 있음

우리는 ssh client를 이용하여 Ec2를 접ㅂ속할 것이다.

원리를 먼저 설명하겠다.
![](https://velog.velcdn.com/images/bluepaper14/post/7d8cb1b3-9e88-414b-82bb-0ecbb395e7f8/image.gif)
AWS EC2에서 사용자가 SSH를 통해 인증하는 과정이다. 요약해서 비대칭 암호화를 사용하여 접근을 안전하게 하는 방식인데,
1. 사용자(Alice)가 EC2에 연결을 시도한다. I'm Alice라고 선언한다.
2. EC2는 무작위 랜덤값 R을 생성하여, Alice의 공개키를 가지고 암호화한 후 Alice에게 전송한다.
3. Alice는 자신의 개인 키를 사용하여 암호화된 R을 복호화한다.
4. 복호화된 R값을 다시 EC2에 보내면 올바를 값인지 확인하고 해당 신원이 맞는지 확인한다.

이것이 Key Pair의 원리이다. 이제 생성해보자
![](https://velog.velcdn.com/images/bluepaper14/post/d27a4a2d-8878-465d-8b6d-c3e1e8dd6175/image.png)
검색에 EC2라고 검색하고 대시보드에 키페어라고 있다.![](https://velog.velcdn.com/images/bluepaper14/post/5af03898-71d5-4d99-9e34-77b4f6d6a737/image.png) 해당란에 맞게 작성해주면된다.
RSA는 공개키를 의미한다. 또한 .pem(개인키)를 이용항여 만들 것이다.![](https://velog.velcdn.com/images/bluepaper14/post/709a7576-6b9f-4562-8ce5-37c520733dbf/image.png)
이렇게 개인키가 생성되고 로그인할때 사용된다. 언제든지 생성하고 삭제가 가능하다.

## 1-4 EC2 생성
![](https://velog.velcdn.com/images/bluepaper14/post/180ef882-0c06-421b-ac2f-f2aad777e31a/image.png)
위 사진을 요약하면 AWS 센터에 있는 물리적인 서버가 존재한다. AWS는 이 물리 서버들을 관리하고, 그 위 호스트 Os 와 하이퍼바이저를 통해 가상 머신을 생성한다. 각 가상 머신은 운영체제를 실행시킨다.

또한 각 호스트 머신의 하드웨어 리소스를 가상 머신들이 공유한다. 여러 운영 체제의 머신을 하나의 물리 서버 상에서 실행 가능하다.

이제 직접 콘솔홈에서 EC2를 생성해보자.

1.검색에 EC2 라고 검색하여 [대시보드] 들어가기
2.이후 이름을 설정하고 Amazon Linux 설정
![](https://velog.velcdn.com/images/bluepaper14/post/11faf727-aa8f-410c-9f2d-3b9735c38cc1/image.png)


3. 그리고 아까 설정한 Key Pair의 이름을 입력한다.
4.이후 네트 워크를 설정할 것이다. 하나씩 알아보자.
![](https://velog.velcdn.com/images/bluepaper14/post/1f6a16f6-da2e-4442-8e24-09ef6dd3e479/image.png)
### 네트워크
EC2 인스턴스가 연결될 네트워크를 선택하는 옵션이다. VPC~~ 로 시작하는데 VPC는 '가상 사설 클라우드' 네트워크를 의미한다. 이 네트워크는 사용자가 만든 (서브넷 + 라우팅 테이블)로 구성되어 있다. 
### 서브넷
현재는 기본 설정이 없는데, VPC 내의 서브넷을 선택할 수 있다. 서브넷은 일반적으로 트래픽을 세분화하거나 리소스를 더 효율적으로 관리한다. 보통은 기본 서브넷으로 자동 선택된다.
+ 실습에서는 서브넷 -2a를 선택함

### 퍼블릭 IP 자동 할당
공용 IP 주소를 자동으로 할당할지 여부를 물어본다. 공용 IP가 할당된 인스턴스는 인터넷 접근이 가능하다.
### 방화벽
현재 방화벽에서 '다음에서 SSH 트래픽 허용'이라고 되어 있다. 또한 위치무관은 어디서든지 SSH 트래픽을 허용한다는 의미이기에 보안상으로 권장하지 않으며 특정 IP 주소만 허용한다.
SSH만 허용하였기 때문에 port 22번만 접속 가능하도록 했다.
![](https://velog.velcdn.com/images/bluepaper14/post/155aef81-85ae-4cb9-acc9-62cf13fb56b9/image.png)
최종적으로 EC2 인스턴스가 생성됨을 볼 수 있다. 이제 SSH로 Key Pair을 가지고 서버를 접속해보자.

위 화면에서 **[인스턴스 ID] -> [연결] -> [SSH 클라이언트] ** 들어가면 SSH의 EC2를 접속하는 퍼블릭 주소가 존재한다.
![](https://velog.velcdn.com/images/bluepaper14/post/4180b470-e3e3-4f7a-a3e3-1671559060e6/image.png)
이후 cmd창에 그래도 복사한 퍼블릭 주소를 입력한다.
여기서 중요한 것은 현재 osskey.pem의 디렉토리가 어디에 위치해 있는지 알아야한다.
일반적으로 osskey.pem 위치를 AWS에서 알지 못하기 때문에 
>```
ssh -i "C:/Users/.../.../osskey.pem" + 명령 복사한 주소
```

이렇게 디렉토리의 위치를 추가해서 cmd창에  붙어야한다. 결과적으로 이렇게 서버가 접속된다.
![](https://velog.velcdn.com/images/bluepaper14/post/9485c8d5-e44b-4251-b270-a530f0238c0d/image.png)
```
whoami 	/사용자 이름 출력
pwd 	/디렉토리 경로 출력
ls -al  /디렉터리 내의 파일 및 디렉터리 목록을 자세하게 출력
```

또한 마지막으로 SSH를 이용하지 않더라도 서버에 접속이 가능하다. ![](https://velog.velcdn.com/images/bluepaper14/post/4a83cab3-bd43-4b33-9871-a09fce017390/image.png)
바로 연결하면 웹에서 cmd창을 볼수 있다.
