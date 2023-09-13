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
- client - server 구조

### 무상태 프로토콜
- 서버가 클라이언트의 상태를 보존하지 않음
- 보통은 무상태. 사용자 세션 같은 경우에는 stateful하게.
- 단점: 요청 시 데이터를 너무 많이 보냄

### 비연결성
- 기본이 연결을 유지하지 않음
- 수천명이 사용해도 실제 서버에서 처리되는 요청은 수십개 내외
- 매 요청 시 3way handshake 추가
- 현재는 http 지속 연결로 해소됨
  - 연결 > 요청-html 응답 > 요청-js 응답 > 요청-css 응답 > 종료
- 같은 시간에 딱 발생하는 대용량 트래픽
  - ex) 선착순 이벤트, 명절 기차표 끊기 -> 수만명 동시 요청
  - 보통 정적 페이지를 먼저 하나 띄워서 사용자들이 시간을 보내게 함으로써 요청을 약간은 분산시킨다.

### Http 메시지
- http 헤더에서 key값은 대소문자를 구분하지 않음 (value는 구분)

<br>

## Http API

### uri 설계 (bad)
- 회원 목록 조회: read-member-list
- 회원 조회: read-member-by-id
- 회원 등록: create-member
- 회원 수정: update-member
- 회원 삭제: delete-member

### uri 설계 (good)
- 리소스와 행위를 분리
- 회원 목록 조회: GET  /members
- 회원 조회: GET  /member/{id}
- 회원 등록: POST  /member/{id}
- 회원 수정: PUT  /member/{id}
- 회원 삭제: DELETE  /member/{id}

### Http 메서드
- GET: 조회
- POST: 데이터 처리, 등록
  - 새 리소스 생성 및 등록
  - 요청 데이터 처리 (중요)
    - 상품 주 후 배달 시작 버튼 누를 때 POST 요청
    - 새로운 리소스가 생성되지 않을 수도 있음
    - POST  /orders/{orderId}/start-delivery
    - 어쩔 수 없이 리소스만으로 설계가 안 될 때가 있음 -> 동사를 사용한 uri (컨트롤 uri)
  - 다른 메서드로 처리하기 애매한 경우
    - json으로 조회(get) 데이터 넘길 때 -> post 요청
    - get 성격의 요청이지만, 메시지 body를 허용하지 않을 때 -> 조회이지만 post를 사용해야 
  - POST는 모든 것을 할 수 있음
  - 하지만 조회는 get이 유리 -> 캐싱을 하기 때문
- PUT: 대체
  - 완전히 대체
  - 있으면 대체, 없으면 새로 생성
  - put은 리소스 위치를 알고 uri 지정 (post와 차이)
  - 사실 put은 리소스를 수정하는 게 아니라 갈아치우는 것
- PATCH: 수정
  - 리소스 부분 변경
  - 요청 시 보내는 필드만 수정
  - http에서 patch를 받아들이지 못하는 경우 -> post 사용
- DELETE: 삭제
- HEAD: GET과 동일하지만 메시지 제외하고 상태, 헤더만 리
- OPTION: 통신 가능 여부 (CORS)
- CONNECT: 서버에 대한 터널 설정 (?)
- TRACE: 메시지 루프백 테스트 수행 (?)

### Http 메서드 속성
- GET
  - 요청에 body 넣을 수 있지만 사용하지 않는 것이 좋다. 안 되는 브라우저가 있기 때문
- 안전
  - 호출해도 리소스를 변경시키지 않음
  - GET: 안전
  - POST, PUT, DELETE: 안전하지 않음
- 멱등
  - 몇 번을 호출하든, 결과는 똑같음
  - 멱등 메서드
    - (o) GET: 몇 번을 조회해도 같은 결과.
    - (o) PUT: 결과를 대체. 여러 번 요청해도 최종 결과는 같음.
    - (o) DELETE: 결과를 삭제. 여러 번 삭제해도 삭제된 결과는 같음.
    - (x) POST: 멱등하지 않음. 두 번 호출 시 결제가 중복 발생.
  - 멱등은 외부 요인으로 인해 리소스가 변경되는 것까지 고려하지 않음. 동일한 사용자의 요청에 대해서만 판단함.
- 캐시 가능
  - 응답 결과 리소스를 캐시해서 사용해도 되는가?
  - GET, HEAD 정도만 캐시해서 사용
  - GET은 url만 key로 잡고 캐싱
  - 하지만 PUT, PATCH는 본문 내용까지 캐싱해야하기 때문에 구현이 쉽지 않음

### Http 활용

- 클 > 서버 데이터 전송
  - 쿼리 파라미터 사용: GET
    - 정렬 필터를 사용한 조회
  - 메시지 바디: POST, PUT, PATCH
    - 회원가입, 상품 주문, 리소스 등록, 리소스 변경
  - 1_정적 데이터 조회
    - 이미지, 정적 텍스트, 문서
    - GET 메서드 사용
    - 일반적으로 쿼리 파라미터 없이 리소스 경로로 조회
  - 2_동적 데이터 조회
    - 쿼리 파라미터 사용
    - 검색, 게시판 목록 필터링
    - 조회 조건을 줄여주는 필터, 정렬 조건
    - GET 메서드 사용
  - 3_html form을 통한 데이터 전송
    - post 요청
      - form 태그에 action은 url, method는 메서드 입력
      - form 태그 안의 input 태그의 name에 전송 데이터 이름 입력
      - button 태그의 type에 submit 입력
      - http 메시지에는 content-type: application/x-www-form-urlencoded 헤더로 생성됨
        - 전송 데이터를 url encoding 처리해서 보냄
      - 회원가입, 상품 주문, 데이터 변경 시 사용
    - get 요청
      - 전송 데이터를 메시지 바디가 아니라 쿼리 파라미터로 넘겨버림
    - enctype="multipart/form-data"
      - 파일 업로드 같은 바이너리 데이터 전송 시 form 태그에 사용
      - 다른 종류의 여러 파일과 데이터를 함께 보낼 수 있음 -> multipart
    - get, post만 지원
  - 4_html api를 통한 데이터 전송
- http api
  - content-type: application/json
  - 서버 to 서버 통신
  - 앱 클라이언트에서 사용
  - 웹 클라이언트 (리액트, 뷰 등)
  - json을 통한 데이터 전송 (json이 표준)

### Http api 설계
- http api (컬렉션)
  - uri는 리소스를 식별해야 한다.
    - 회원 목록 조회: GET  /members
    - 회원 조회: GET  /member/{id}
    - 회원 등록: POST  /member/{id}
    - 회원 수정: PUT  /member/{id}
      - 보통은 patch를 사용한다.
    - 회원 삭제: DELETE  /member/{id}
  - 클라이언트는 등록될 리소스의 uri를 모름
  - 서버가 새로 등록해줌
  - 컬렉션
    - 서버가 관리하는 리소스 디렉토리
    - 서버가 리소스의 uri를 생성하고 관리
    - 여기서 컬렉션은 /members
- http api (스토어)
  - 파일 목록: /files -> GET
  - 파일 조회: /files/{filename} -> GET  
  - 파일 등록: /files/{filename} -> PUT
  - 파일 삭제: /files/{filename} -> DELETE 
  - 파일 대량 등록: /files -> POST
  - PUT을 사용했다는 것에 주목
    - 클라이언트가 리소스 URI를 알고 있다.
    - POST 요청시와 다른 점이 이 부분.
    - POST는 클라이언트가 URI를 알지 못함.
    - 대부분은 POST 기반의 URI를 사용.
- html form (순수 html form) 
  - GET, POST만 지원함
  - 회원 목록 조회: GET  /members
  - 회원 등록 폼: GET  /members/new
  - 회원 등록: POST  /members/new, /members
  - 회원 조회: GET  /members/{id}
  - 회원 수정 폼: GET  /members/{id}/edit
  - 회원 수정: POST  /members/{id}/edit, /members/{id}
  - 회원 삭제: POST  /members/{id}/delete