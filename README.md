# http-basic

## IP

- ip(인터넷 프롵토콜)의 한계
  - 패킷을 받을 대상이 없거나 서비스 불능 상태여도 일단 패킷 전송
  - 중간에 패킷 유실 가능
  - 패킷 전달 순서 바뀔 수도 있음 (두 패킷이 다른 노드를 탈 수도 있음)
  - 같은 ip를 사용하는 서버에서 통신하는 애플리케이션이 둘 이상일 경우

<br>

## TCP

### 인터넷 프로토콜 스택의 4계층
- 애플리케이션 계층 - http, ftp
- 전송 계층 - tcp, udp
- 인터넷 계층 - ip
- 네트워크 인터페이스 계층

### TCP/IP 패킷 정보
- ip 패킷: 출발지 ip, 도착지 ip, 기타 정보..
- tcp 세그먼트: 출발지 port, 목적지 port, 전송 제어, 순서, 검증 정보...

### tcp 특정
- tcp 3 way handshake
  - syn: 클 -> 서
  - syn + ack: 서 -> 클
  - ack: 클 -> 서
  - 데이터 전송
- 데이터 전달 보증
- 순서 보장

<br>

## UDP

- 하얀 도화지 같은 프로토콜
- ip 프로토콜에 port 추가, 체크섬 추가
- tcp는 과정이 복잡해서 시간이 오래 걸림
- 애플리케이션에서 추가 작업 필요
- http3에서는 성능 최적화를 위해 (3way handshaking 최적화) udp 프로토콜 사용

<br>

## PORT
- 같은 ip 내에서 프로세스 구분
- 0~65535 할당 가능
- 0~1023은 잘 알려진 포트, 사용하지 않는 게 좋음
  - ftp: 20, 21
  - telnet: 23
  - http: 80
  - https: 443

<br>

## DNS
- 문제: ip는 변경될 수 있음
- 도메인명에 ip를 연결
- ip가 바뀌면 dns 서버에도 ip 수정
- 클라이언트는 도메인명으로 요청 -> dns 서버에서 ip 내려줌 -> 받은 ip로 요청

<br>

## URI

- Uniform Resource Identifier
- URI, URL, URN
- URL(Locator), URN(Name)은 URI(Identifier)에 포함된다.
- scheme://[userinfo@]host[:port][/path][?query][#fragment]
  - scheme - protocol
  - userinfo - 사용자 정보 포함해서 인증 (거의 사용하지 않음)
  - host - 도메인명, ip
  - port - 생략 가능
  - path - 리소스 경로, 계층적 구조
  - query - key:value의 형태, ?로 시작, &로 추가 가능, '쿼리 파라미터' 혹은 '쿼리 스트링'으로 불림
  - fragment - html 내부 북마크

<br>

## 웹 브라우저 요청 흐름
- _1 url 입력 시 dns 서버에 ip, port 조회
- _2 Http 요청 메시지 생성
- _3 패킷 생성
- _4 요청
- _5 응답 메시지 및 패킷 생성
- _6 html 랜더링

<br>

## HTTP
- HyperText Transfer Protocol
- http/1.1, http/2는 tcp 기반
- http/3은 udp 기반
- 