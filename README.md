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
  - 컨트롤 uri를 사용할 수밖에 없음
  - /new, /edit, /delete
  - 실무에서 컨트롤 uri를 생각보다 많이 쓴다.
- uri 설계 개념
  - 문서: 단일 파일, 객체 인스턴스
    - ex) /member/100, /files/star.jpg
  - 커렉션: 서버가 관리하는 리소스 디렉토리
    - ex) /members
  - 스토어: 클라이언트가 관리하는 자원 저장소
    - ex) /files
  - 컨트롤러, 컨트롤 uri: 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행
    - 동사를 직접 사용
    - ex) /member/{id}/delete

### Http 상태코드
- 1xx: 요청이 수신되어 처리중. 거의 사용하지 않음.
- 2xx: 요청 정상 처리
  - 200 Ok
  - 201 Created - 새로운 리소스가 생성됨
  - 202 Accepted - 요청이 접수되었으나 처리가 되지 않았음.
    - ex) 요청 접수 후 1시간 뒤 배치 프로세스가 요청 처리할 경우
  - 204 No Content - 요청은 성공적이지만, 응답 본문에 보낼 데이터가 없음
    - ex) 웹 문서 편집기에서 save 버튼
- 3xx: 요청 완료하려면 추가 행동 필요
  - 3xx 결과에 Location 헤더가 있으면, Location 위치로 자동 이동
  - 영구 리다이렉션 - /members -> /users
    - uri가 영구적으로 이동
    - 301 Move Permanently: 리다이렉트 요청 메서드가 get으로 변하고, 본문이 제거될 수 있음 - 보통은 이렇게 처리함
    - 308 Permanent Redirect: 301과 기능은 같지만 요청 메서드와 본문 유지
  - 일시 리다이렉션 - PRG: Post/Redirect/Get
    - 302 Found: 리다이렉트 요청 메서드가 get으로 변하고, 본문이 제거될 수 있음
    - 307 Temporary Redirect: 리다이렉트 요청 시 메서드와 본문 유지해야 함
    - 303 See Other: 리다이렉트 시 요청 메서드가 get으로 변경
    - PRG
      - Post로 주문 후에 웹 브라우저를 새로고침을 하면? -> 서버에 중복 주문이 들어갈 수 있음
      - Post 주문 요청 -> 서버에서 주문 처리 -> 302 Found 응답을 Location과 함께 내려줌
      - -> 웹에서는 get 요청으로 바꾸고 조회
      - 결과 화면에서 새로고침을 해도 get 요청만 보냄 -> 중복 요청 방지
  - 특수 리다이렉션 - 결과 대신 캐시를 사용하라는 리다이렉션
    - 304 Not Modified
    - 캐시를 목적으로, 클라이언트에게 리소스가 수정되지 않았음을 알려줌.
- 4xx: 클라이언트 오류
  - 여러 번 요청해도 실패
  - 400 Bad Request
  - 401 Unauthorized: 인증되지 않음
    - 인증(Authentication): 본인이 누구인지 확인 (로그인)
    - 인가(Authorization): 권한 부여
  - 403 Forbidden: 서버가 요청을 이해했지만, 승인 거부
  - 404 Not Found: 리소스가 없음
- 5xx: 서버 오류
  - 여러 번 요청해도, 서버가 복구되면 성공
  - 500 Internal Server Error
  - 503 Service Unavailable: 서비스 이용 불가.
  - 왠만하면 500대 에러를 만들면 안 됨. 

## Http 헤더
- 메시지 바디 내용, 메시지 바디 크기, 압축, 인증, 요청 클라이언트, 서버 정보, 캐시 등
- 표준 헤더가 너무 많음
- 필요시 임의 헤더 추가 가능
- 표현 헤더 + 표현 데이터 (메시지 바디)

- 표현 헤더
  - Content-Type
    - 표현 데이터의 형식
    - ex) text/html; charset=utf-8, application/json
  - Content-Encoding
    - 표현 데이터의 압축 방식
    - 전달하는 쪽에서 압축 후 인코딩 헤더 추가
    - 읽는 쪽에서 인코딩 헤더 정보로 압축 해제
    - ex) gzip, deflate, identity
  - Content-Language: 표현 데이터의 자연 언어
    - ex) ko, en, en-US
  - Content-Length: 표현 데이터의 길이
    - 바이트 단위

- 협상 헤더(콘텐트 네고시에이션) - 요청 시에만 사용
  - Accept: 클라이언트가 선호하는 미디어 타입 전달
  - Accept-Charset: 클라가 선호하는 문자 인코딩
  - Accept-Encoding: 클라가 선호하는 압축 인코딩
  - Accept-Language: 클라가 선호하는 자연 언어
  - 우선순위
    - 위해 q(Quality Values)를 사용함
      - 1이 가장 높음, 0이 가장 낮음, 생략하면 1
    - 헤더가 구체적일수록 우선순위가 높음

### 전송 방식
- 단순 전송: 한 번에 요청하고 한 번에 받는 전송
- 압축 전송: response header에 Content-Encoding 값을 넣어줘야 함
- 분할 전송: response header에 Transfer-Encoding 값을 넣어줘야 함, 큰 용량의 데이터를 분할해서 전송, Content-Length를 주면 안 됨
- 범위 전송: 요청을 중간에 받다가 중지되었을 경우, 처음부터 받지 않고 범위를 지정해서 요청하는 방식


### 일반 정보
- Form: 유저 에이전트의 이메일 정보, 일반적으로 잘 사용하지는 않음
- Referer: 현재 요청된 페이지의 이전 웹 페이지 주소. 유입 경로 분석 시 사용됨
- User-Agent: 클라이언트 어플리케이션 정보. 어떤 종류의 브라우저에서 장애가 발생하는지 파악 가능
- Server: Origin 서버의 정보
- Date: 메시지가 발생한 날짜, 시간

### 특별 정보
- Host: 요청한 호스트 정보(도메인)
  - 요청에서 사용하는 필수값
  - 하나의 서버가 여러 도메인을 처리해야할 때, 호스트를 특정하기 위해 사용
- Location: 페이지 리다이렉션
  - 201 (Created)에서의 Location: 요청에 의해 생성된 리소스 uri
  - 3xx (Redirection)에서의 Location: 요청을 자동으로 리다이렉션하기 위한 대상 리소스를 가리킴
- Allow: 허용 가능한 http 메서드
  - 405 응답 반환 시 허용 가능한 메서드를 포함해서 리턴해야 함
- Retry-After
  - 503 응답 반환 시 서비스가 언제까지 불능인지 알려주는 헤더


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
