# Network & DNS Wheel Seminar Assignment 2
2024 겨울 SPARCS Wheel Network & DNS 세미나의 2번째 과제입니다. 여러분들은 **C언어와 리눅스 API만을 사용해** 간단한 **HTTP 서버**를 만드셔야 합니다.

`main.c` 파일의 `DoSomething` 함수(47번 줄)를 완성시켜, 아래의 **구현 사항**을 모두 구현하는 것이 목표입니다. 필요한 경우 추가적인 함수를 정의하거나, 이미 작성된 코드를 수정하셔도 됩니다.

## 컴파일 및 실행 방법
- 다음 명령어를 순서대로 셸에 입력하세요. 포트를 입력하지 않으면 기본 포트(`main.c`의 `PORT` 값)에서 서버가 열립니다.
```
> make
> ./server.out [포트]
```
- `make run` 명령어를 입력하면 서버를 실행합니다. 변경 사항이 있을 경우 컴파일 후 실행합니다. 기본 포트에서 서버가 열립니다.
- `make clean` 명령어를 입력하면 컴파일 결과물을 모두 삭제합니다.

아무 것도 하지 않은 상태로 서버를 구동하여 웹 브라우저로 접속했을 때 `Hello, world!`가 표시되어야 정상입니다.

## 구현 사항
- HTTP/1.1 외의 HTTP 요청은 적절한 응답 코드를 보내 거절합니다.
- `Connection` 헤더의 값에 따라, HTTP 응답을 보낸 후 TCP 연결을 끊거나 유지해야 합니다. 간단함을 위해, `keep-alive`인 경우(대소문자 구분 X) 연결을 유지하고, 그 외의 경우 연결을 즉시 끊는 것으로 합니다.
- 다음은 리소스 경로 & 메서드에 따른 서버의 동작 요구 사항입니다.
	- `GET /`: 로그인된 경우, `public/index.html` 파일을 전송합니다. 로그인되지 않은 경우, `/login`으로 리다이렉트합니다.
	- `GET /login`: 로그인된 경우, `/`로 리다이렉트합니다. 로그인되지 않은 경우, `public/login.html` 파일을 전송합니다.
	- `POST /login`: 로그인된 경우, 적절한 응답 코드를 보내 잘못된 접근임을 알립니다. 로그인되지 않은 경우, 아이디와 비밀번호를 검증해 로그인합니다. 쿠키를 이용해 세션 정보를 저장해야 합니다. 성공한 경우, `/`로 리다이렉트합니다.
	- `GET /register`: 로그인된 경우, `/`로 리다이렉트합니다. 로그인되지 않은 경우, `public/register.html` 파일을 전송합니다.
	- `POST /register`: 로그인된 경우, 적절한 응답 코드를 보내 잘못된 접근임을 알립니다. 로그인되지 않은 경우, 아이디와 비밀번호를 검증해 회원가입합니다. 성공한 경우, `/login`으로 리다이렉트합니다.
	- `GET /text`: 로그인된 경우, 데이터베이스의 `text` 카테고리를 조회해 저장된 문자열을 반환합니다. 저장된 문자열이 없는 경우, 빈 값을 반환합니다. 로그인되지 않은 경우, 적절한 응답 코드를 보내 잘못된 접근임을 알립니다.
	- `POST /text`: 로그인된 경우, 데이터베이스의 `text` 카테고리에 HTTP 요청의 Body를 저장합니다. 로그인되지 않은 경우, 적절한 응답 코드를 보내 잘못된 접근임을 알립니다.
- 세션의 유효 시간은 1분입니다. 즉, 로그인한 후 1분이 지나면 다시 로그인해야 합니다.
- 위에 명시되지 않은 리소스에 접근하는 경우 404 Not Found와 함께 `public/404.html` 파일을 전송합니다.
- 메모리 할당 해제는 신경쓰지 않으셔도 됩니다.

## 팁
- 이 과제의 본질은 HTTP 프로토콜의 이해이지 C언어 코딩이 아니라고 생각했습니다. 그래서, `util.h` 파일에 주요 비지니스 로직을 미리 구현해 두었습니다. 해당 파일에 정의된 함수를 적절히 사용하시면 금방 과제를 완료하실 수 있을거예요.
- Node.js와 달리, 소스 코드를 수정하더라도 변경 사항이 서버에 바로 반영되지 않습니다. 서버를 종료한 뒤(Ctrl+C) 다시 컴파일 해주셔야 합니다!
- 정말 어떻게 할지 모르시겠다면 `main.example.c` 파일을 참고해 보세요!

### TMI
- 가끔 서버를 비정상적으로 종료한 경우, 같은 포트에서 서버가 열리지 않는 경우가 있습니다. 시간이 지나면 해당 포트를 다시 사용할 수 있지만, 이런 경우에는 다른 포트에 여시는 것이 속 편합니다.
- telnet을 이용해 테스트해 보고 싶으시다면, telnet의 표준 입력을 Redirection하거나 Here Document(`<<`)를 사용하셔야 합니다. telnet은 줄 단위로 서버에 데이터를 전송하는데, 현재 구현체에서는 줄 단위로 따로 전송된 HTTP 요청을 합쳐서 처리하는 기능이 구현되어 있지 않기 때문입니다.

## 참고한 곳
- https://github.com/GrasshopperBears/sparcs-simple-http-server