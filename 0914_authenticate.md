# 2022.09.14 TIL

## Knowledges | 인증 & 인가

## 

ID, PW를 통한 로그인방식 (Basic 인증 방식)

1. Client가 Server에게 GET /home API를 요청
2. Server는 GET /home API가 회원만 요청할 수 있는 API임을 인지함. → Server는 현재 신원을 알 수 없는 Client에게 권한 없음 Response를 보낸다.
    1. HTTP Status Code는 401 (권한없음)코드를 반환한다.
    2. Response의 Headerdp WWW-Authenticate: Basic이라는 값을 함께 반환한다.
        - WWW-Authenticate : Basic 은 Server가 Client에게 ID, PW를 통한 인증 방식으로 인증할 수 있음을 알리는 메시지이다.
        - WWW-Authenticate: Bearer는 OAuth2.0 프로토콜을 따르는 인증방식(간편로그인)을 의미한다. 이처럼 basic 말고 다른 방식도 있다.
3. Client는 Server로부터 위와 같은 요청 거절 Response를 전달받았다. 비록 Client는 요청이 거절됐지만 ID, PW를 통한 로그인을 해야함을 인지
4. Client는 다시 Server에게 GET /home API를 요청한다. 단, 이번에는 Header에 Authorization: Basic YWxpY2U6cGFzc3dvcmQ를 담아서 함께 전송한다.
    1. 이것은 서버가 보낸 Header WWW-Authenticate: Basic에 대응하는 Client의 올바른 인증 시도이다. 
    2. 이때 Basic 뒤에 붙은 YWxpY2U6cGFzc3dvcmQ 라는 값이 Base64로 인코딩된 ID:PW 이다
    3. Base64 인코딩은 암호화가 아닌 그저 문자열의 패키징이다.
    4. 이처럼 계정 정보의 보안이 굉장히 취약함을 알 수 있다.
5. Server는 Client로 부터 전송받은 Header값으로부터 Base64 디코딩하여 ID와 PW를 얻는다.
    1. 그리고 그 값으로 유저의 신원을 인증(Authentication)을 한다. ( =로그인)
    2. 그리고 해당 신원의 유저가 해당 API (GET /home)을 요청할 권한이 있는지 확인하여 인가(Authorization)한다.
    - 예) 일반 회원이 VIP회원만 할 수 있는 일을 요청하면 여전히 권한이 없어야 한다.
6. Server는 정상적으로 인가될 경우 Client에게 Http Status Code 200 을 보낸다.
    1. 정상 인가되지 않을 경우 401 (Unauthorized), 403 (Forbidden)을 보낼수도 있다.
