# 2022.09.27 TIL

## session 인증방식

어떤 유저가 로그인에 성공했을 경우 세션(Session)정보를 이용해서 해당 유저의 인증 효력을 유지한다.

server는 로그인에 성공한 유저에게 session이라고 부르는 입장 티켓을 발급하고, 이후로 client가 이 입장 티켓을 제시할 경우, server는 티켓만을 검사하며 ID, PW를 통한 로그인을 다시 요청하지 않는다.

→ session 인증방식은 로그인 상태 유지의 개념을 의미한다.

만약 session이 없다면 유저는 인증이 필요한 API를 호출할 때마다 수동으로 일일이 ID, PW를 타이핑 하여 로그인해야하는 과정을 반복해야한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/48f83238-ca14-4e03-889b-8b4730fae8df/Untitled.png)

### 1. 로그인 시도

id, pw를 입력하여 로그인 인증을 시도한다.

### 2. 세션 정보 저장

server는 client의 신원을 확인하는 데서 그치는 것이 아니라 session이라는 것을 생성해서 신원 확인된 계정과 연결한다

- session의 실체는 server만 알 수 있는 방식으로 암호화된 일종의 키값이다.
- id가 1234인 유저에게 session키 값 session_id를 할당한다.
- session_id란 : 세션ID를 의미하는 임의의 암호화 값이라고 가정
- 1234 ↔ session_id 연결 관계 정보가 DB에 저장됨
- 이 때 session의 발급날짜, 유통기한을 함께 저장한다.

### 3. Cookie반환 session_id

server는 최종적으로 로그인 요청에 대한 성공 response를 반환하면서 발급한 session에 대한 정보를 cookie로 함께 넘겨준다.

- session에 담긴 정보는 단순한 암호화 키값이라고 했지만 어떻게 구현하느냐에 따라 다른 여러 정보가 함께 담길 수 있다.
- client의 브라우저(chrome, edge)는 server로부터 전달 받은 cookie를 읽어서 별도 공간에 임시 저장 해둔다. (캐싱)

### 4. API요청

client는 server에게 API를 요청할 때, server로부터 받았던 cookie값 (session_id)를 함께 보낸다.

### 5. 세션 정보 조회 & 정상 Response

server는 Client가 보낸 API요청과, 함께 담긴 세션 cookie값 (session_id)가 DB에 존재하는지 확인한다.

DB에 session_id가 있다면 session_id와 매칭되어 있는 계정 ID가 무엇인지 확인한다.

- session_id 유효성 검증

PW암호값으로 로그인 하는 과정을 거치지 않고도 Client가 ID가 해당 유저임을 인증함

- API GET /my/private/info는 특정 유저의 개인정보를 요청하는 API라고 가정하면
- API는 위 세션을 통한 로그인 과정을 거쳐서 해당 계정의 개인정보를 요청하는 API로 동작한다.
- API처리 결과를 정상적으로 Response한다.

### 6. API요청

### 7. 세션 정보 조회 & 비정상 response

위 섹션 5처럼 server가 client로부터 전달받은 cookie값 (session_id)를 검증한다.

- session_id 유효성 검증
    - 검증 중 문제 발견 : ex) 발급일로부터 유통기한이 지난 만료된 세션 ID
- session_id에 매칭된 ID를 찾기도 전에 세션 자체에 대한 문제가 있음을 발견함 따라서 server는 client에게 비정상 response를 전송 → 다시 로그인 부탁
