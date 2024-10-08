---
layout: single
title:  "AWS VPC와 Public,Private Subnet 생성"
categories: [AWS]
tag: [AWS, EC2, VPC]
toc: true
toc_sticky: true
post-header: false

---

## 구축 순서

VPC 생성 → 서브넷 생성 → 인터넷 게이트웨이 생성 → 퍼블릭 라우팅 → 라우팅 테이블 생성 및 설정

![스크린샷 2024-08-23 오후 3.33.55.png](/assets/images/aws04/01.png)

## VPC (Virtual Private Cloud)

- AWS 클라우드의 프라이빗 가상 네트워크
- 보통 AWS 리전 및 가용 영역의 고가용성을 기반으로 구축한다.
- VPC는 한 리전 내에 상주하며, 여러 가용영역에 걸쳐 사용 가능하다.
- VPC는 네트워크 구성을 완벽하게 제어할 수 있도록 허용한다.
- VPC 내부의 리소스를 격리하고 노출할 수 있는 기능이 있다, (서브넷, 액세스 제어 목록 정의, 라우팅 규칙 등)
- 여러 계층의 보안 제어 기능을 제공한다.
- 특정 인터넷 및 내부 트래픽을 허용 및 거부할 수 있는 기능이 있다.
- 다른 AWS 서비스를 VPC에 배포할 수 있다.

## 1. VPC 생성

VPC 이름은 이름-리전-vpc로 작성하였다.

이후, IPv4 CIDR 블록에 생성할 VPC IP 대역대를 입력

![스크린샷 2024-08-22 오후 4.05.26.png](/assets/images/aws04/02.png)

## 2. Public subnet 생성

### Subnet이란?

VPC를 나누는 데 사용된다. VPC안에 나누어진 부분 네트워크라고 할 수 있다. 

보통 VPC를 IP로 구분해 나눈 후에 다중 AZ(가용영역)에 걸쳐 서브넷을 구성한다.

- 퍼블릭 서브넷
    - 인터넷에 직접 엑세스가 가능한 서브넷이다.
    - 인터넷 게이트웨이를 VPC에 연결하고 퍼블릭 서브넷의 라우팅 테이블을 업데이트하여 로컬이 아닌 트래픽을 인터넷 게이트웨이로 보내면 퍼블릭 서브넷처럼 사용할 수 있다.
- 프라이빗 서브넷
    - 인터넷에 직접 액세스가 불가능한 서브넷이다.
    - 보통 회사 내부 서버나 프로그램들은 프라이빗 서브넷에 위치시켜 인터넷을 통해 아무나 접근이 불가능하게 한다.

### Public Subnet을 각각 A,C 가용영역에 생성해보기

![스크린샷 2024-08-22 오후 4.01.51.png](/assets/images/aws04/03.png)

서브넷을 생성할 VPC를 목록에서 선택하고, 서브넷 이름은 이름-public-subnet-가용영역으로 생성하였다.

![스크린샷 2024-08-22 오후 4.09.50.png](/assets/images/aws04/04.png)

첫번째로, 가용영역 A에 서브넷을 생성해준다.

IPv4 CIDR 블록에는 선택한 VPC의 IP범위 내, 생성할 서브넷의 IP 대역대를 입력한다.

![스크린샷 2024-08-22 오후 4.12.16.png](/assets/images/aws04/05.png)

새 서브넷 추가를 클릭하여 가용영역 C에 서브넷을 하나 더 추가해준다.

![스크린샷 2024-08-22 오후 4.54.01.png](/assets/images/aws04/06.png)

## 3. Internet Gateway 생성

Internet Gateway란

- VPC에 부착되어, VPC와 인터넷 간에 통신할 수 있게 해주는 출입문 역할을 한다.
- 방금 만든 서브넷들이 퍼블릭 서브넷 역할을 하게끔 해주려면, 인터넷으로 접속할 수 있어야 하기 때문이다.

![스크린샷 2024-08-22 오후 4.17.29.png](/assets/images/aws04/07.png)

이제 방금 만든 인터넷 게이트웨이를 선택해서 VPC와 연결을 해준다.

![스크린샷 2024-08-22 오후 4.18.42.png](/assets/images/aws04/08.png)

![스크린샷 2024-08-22 오후 4.19.53.png](/assets/images/aws04/09.png)

![스크린샷 2024-08-22 오후 4.20.27.png](/assets/images/aws04/10.png)

## 4. Public 전용 라우팅 테이블 생성

Routing Table 이란

- IP 주소에 트래픽 라우팅 경로를 정의하여 Subnet 안팎으로 나가는 트래픽에 대한 라우팅 경로 설정 기능을 수행한다.
- 인터넷을 통해 요청이 들어왔을 때, 요청이 들어올 경로 안내를 하는 네비게이션 역할을 한다.

라우팅 테이블이 사용할 VPC를 목록에서 선택 후 생성해준다.

![스크린샷 2024-08-22 오후 4.24.05.png](/assets/images/aws04/11.png)

그다음, 라우팅 테이블 목록에서 라우팅 테이블을 체크 후 ‘서브넷 연결 편집’ 메뉴를 통해 서브넷과 연결해준다.

![스크린샷 2024-08-22 오후 4.26.06.png](/assets/images/aws04/12.png)

‘서브넷 연결 편집’은 해당 라우팅 테이블이 어떤 서브넷에 적용이 되는지 설정해주는 곳이다.

퍼블릭 서브넷 역할을 하게 할 라우팅 테이블이므로, 퍼블릭 역할을 하게 될 서브넷들만 선택한다.

![스크린샷 2024-08-22 오후 4.27.49.png](/assets/images/aws04/13.png)

이제 어떤 라우팅을 해줄 것인지 설정해주면 된다.

방금 등록한 라우팅 테이블을 선택 후 ‘라우팅 편집 버튼’을 클릭하여 설정해준다.

![스크린샷 2024-08-22 오후 4.28.47.png](/assets/images/aws04/14.png)

local : 현재 기본 설정되어 있는 라우팅, VPC 대역대에 있는 IP들 (즉, VPC 내부의 Private IP)들이 서로 알고있게끔 하는 기본 설정이다.

라우팅을 추가하여 대상에는 0.0.0.0/0을 입력, 만든 Internet Gateway로 설정한다.

즉, 위치 무관 아무나 들어올 수 있게 추가한다는 의미

![스크린샷 2024-08-22 오후 4.32.17.png](/assets/images/aws04/15.png)

이제 VPC에 priavte subnet을 구축해보자

프라이빗 서브넷 생성 → 프라이빗 라우팅 테이블 생성 및 설정 순으로 진행

## 5. Private Subnet 생성

이전과 비슷한 방식으로 가용영역 A,C에 생성해준다.

이름-private-subnet-가용영역 으로 생성하고, 서브넷의 IP대역대를 설정해준다.

![스크린샷 2024-08-22 오후 4.44.27.png](/assets/images/aws04/16.png)

![스크린샷 2024-08-22 오후 4.47.24.png](/assets/images/aws04/17.png)

## 6. Private 라우팅 테이블 생성

라우팅 테이블 이름과 사용될 VPC를 선택하고 생성해준다.

![스크린샷 2024-08-22 오후 4.48.23.png](/assets/images/aws04/18.png)

‘서브넷 연결 편집’ 메뉴에서 방금 만든 private subnet들을 선택하여 등록해준다.

![스크린샷 2024-08-22 오후 4.50.01.png](/assets/images/aws04/19.png)

private subnet에 위치하게 될 리소스들은 private IP만 가지기 때문에 내부가 아닌 외부의 리소스들과 통신을 할 수 없게 된다.

### NAT 인스턴스 생성

private IP를 public IP로 변환해 private IP는 감추고, 통신을 할 수 있도록 하게 해준다.

NAT Gateway는 비용이 비싸므로 NAT역할을 할 수 있는 NAT Instance를 생성하여 EC2의 t2.micro 비용으로 NAT 역할을 하는 서버를 만들것이다.

프라이빗 라우팅 테이블에 이 NAT Instance를 추가하면 private subnet안의 리소드들은 이를 통해 외부 리소스들과 통신할 수 있게 된다.

## 7. EC2(NAT Instance) 생성

- private subnet 내의 웹 서버들이 외부로 통신을 하기 위해 사용하는 서버
- 보통 NAT서버를 public subnet안에 구축하여 NAT를 통해 외부로 통신함

AMI 선택

AMI > ‘nat’로 검색 > 아무버전이나 선택 > AMI로 인스턴스 시작

![스크린샷 2024-08-22 오후 5.46.13.png](/assets/images/aws04/20.png)

인스턴스 유형 : t2.micro 선택

VPC : 내가 만든 vpc 선택

서브넷 : public subnet - a 선택

스토리지 : default

![스크린샷 2024-08-22 오후 5.51.53.png](/assets/images/aws04/21.png)

새로운 keypair 생성

보안 그룹 추가 : 내 PC에서만 SSH 접속이 가능하도록 설정

마지막으로, NAT는 소스/대상 확인을 하면 안됩니다

그렇기 때문에 방금 생성한 nat instance 서버를 체크하고 작업-네트워킹-소스/대상 확인 변경 클릭

소스/대상 확인의 ‘중지’를 체크하고 저장하기

![스크린샷 2024-08-22 오후 5.58.21.png](/assets/images/aws04/22.png)

## 8. 탄력적 IP 주소 할당

- 탄력적 IP 주소는 인터넷에서 연결 가능한 퍼블릭 IPv4 주소 이다.
- AWS 계정에 할당되며 릴리스할 때까지 할당된 상태로 유지된다.
- 탄력적 IP주소를 생성해 방금 만든 인스턴스에 연결해주면 해당 인스턴스 IP는 고정 IP주소가 된다.

![스크린샷 2024-08-22 오후 6.01.05.png](/assets/images/aws04/23.png)

생성할 때, 맨 아래에 Name 태그를 하나 추가해줘서 식별해주기

![스크린샷 2024-08-22 오후 6.02.07.png](/assets/images/aws04/24.png)

### 탄력적 IP주소 연결

EIP를 생성했으면 해당 EIP를 사용할 인스턴스를 연결해야 한다.

해당 EIP를 체크해주고, 작업 매뉴에서 탄력적 IP 주소 연결 실행

![스크린샷 2024-08-22 오후 6.02.57.png](/assets/images/aws04/25.png)

연결할 인스턴스를 선택하고 연결 버튼 클릭.

주의) EIP는 AWS에게 IP주소를 빌려오는 개념이기 때문에 EIP에 인스턴스를 연결해주지 않으면 오히려 요금이 부과되는 형태이다. 만약 EC2 인스턴스를 릴리즈(종료)할려면, 꼭 EIP도 같이 릴리즈하기

![스크린샷 2024-08-22 오후 6.04.44.png](/assets/images/aws04/26.png)

이제 NAT Instance 생성 및 설정이 완료되었으니, 0.0.0.0/0 트래픽을 NAT를 통해 들어올 수 있도록 바꾸면 된다.

라우틴 테이블 편집하여 대상을 0.0.0.0/0, 인스턴스 선택 → nat instance를 선택한다.

![스크린샷 2024-08-22 오후 6.07.37.png](/assets/images/aws04/27.png)

이제 private subnet a에 EC2 인스턴스 생성하여 웹서버를 구축해준다.

기존에 가지고 있던 간단한 AMI로 생성해준다.

keypair는 nat instance의 keypair로 사용.

private subnet으로 네트워크 설정을 했으므로 아래와 같이 퍼블릭 IP주소는 할당이 안되게 잘 생성해주자.

![스크린샷 2024-08-22 오후 6.52.48.png](/assets/images/aws04/28.png)

## 9. ALB & Target Group 생성

### ALB(Application Load Balancer)

- 둘 이상의 가용 영역에서 EC2 인스턴스, 컨테이너, IP주소 등 여러 대상에 걸쳐 수신되는 트래픽을 자동으로 분산하는 서비스
- LB는 등록된 대상의 상태를 모니터링하며, 상태가 양호한 대상으로만 트래픽을 라우팅 해준다.
- LB에는 여러 종류가 있음(Application LB, Network LB, Gateway LB, Classic LB)
- 특히, ALB는 HTTP의 URL, FTP의 파일명, 쿠키 정보 등을 분석해 더 정교한 로드 밸런싱이 가능한 서비스이다.

EC2 - 로드 밸런싱 - 로드밸런서 클릭

![스크린샷 2024-08-22 오후 7.00.33.png](/assets/images/aws04/29.png)

[Application Load Balancer 선택]

이름 : tester-alb

체계 : 인터넷 경계

VPC : tester-seoul-vpc

가용영역 : a,c체크 및 public-subnet-a,c 선택

보안그룹 : 인바인드 규칙 0.0.0.0/0 80port

![스크린샷 2024-08-22 오후 7.02.06.png](/assets/images/aws04/30.png)

![스크린샷 2024-08-22 오후 7.03.10.png](/assets/images/aws04/31.png)

리스너 및 라우팅

간단하게 http 통신을 해볼것이기 때문에 80포트 사용(spring, node.js 등의 웹서버를 올리려면 해당 웹서버에서 사용하는 포트로 지정)

![스크린샷 2024-08-22 오후 7.11.11.png](/assets/images/aws04/32.png)

대상 그룹 생성 클릭하여 대상 그룹 지정해주기

ALB 대상은 인스턴스이므로 인스턴스 선택

프로토콜 : 포트 지정

![스크린샷 2024-08-22 오후 7.13.19.png](/assets/images/aws04/33.png)

![스크린샷 2024-08-22 오후 7.13.42.png](/assets/images/aws04/34.png)

ALB가 해당 옵션으로 타겟의 healthy/unhealty 상태를 검사함.

![스크린샷 2024-08-22 오후 7.14.00.png](/assets/images/aws04/35.png)

생성을 클릭하면 아래와 같은 화면에서 등록할 대상 즉, 인스턴스를 선택할 수 있다.

![스크린샷 2024-08-22 오후 7.16.26.png](/assets/images/aws04/36.png)