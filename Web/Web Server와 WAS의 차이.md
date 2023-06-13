# Web Server와 WAS의 차이

`“상황에 따라 변하는 정보를 제공할 수 있는가?”`

## Web Server (웹 서버)

- 웹 브라우저(클라이언트)로부터 HTTP 요청을 받아 **정적 컨텐츠(html, image, css…)**를 제공하는 프로그램
- **동적 콘텐츠 요청** 시 제공을 위해 **WAS에 요청을 전달**하고, 그 결과를 클라이언트에 전달
- Apache, IIs, Nginx 등

<img src="https://github.com/kkzoone/4Group-Study/assets/65496092/831b165b-40ab-4dd9-b5f7-4e8ded620113" width="500" height="400"/>

## WAS (웹 어플리케이션 서버)

- 용어 정리
    - Web Application: 웹에서 실행되는 응용 프로그램
    - Web Application Server: 웹 애플리케이션과 서버 환경을 만들어 동작시키는 기능을 제공하는 소프트웨어 프레임워크
- DB 조회/트랜잭션 관리/비즈니스 로직 수행 등을 요구하는 **동적 컨텐츠**를 제공하기 위해 만들어진 애플리케이션 서버
- 자바 계열에선 `웹 애플리케이션 컨테이너`라고 부름
    - 웹 애플리케이션을 실행하기 위한 환경(공간)
- 대부분의 WAS는 Web Server를 내장하기 때문에 HTTP 요청을 받을 수 있으며, 정적 컨텐츠 제공도 가능
- Tomcat, JBoss, Jeus

<img src="https://github.com/kkzoone/4Group-Study/assets/65496092/529e0dd9-64b0-40f6-817b-391206be28d5" width="500" height="400"/>

# WAS가 있는데 굳이 Web Server가 필요한 이유?

## 1. 책임 분리를 통한 서버 부하 방지

동적 컨텐츠를 제공하려면 다양한 로직을 처리해야 하기 때문에, WAS가 정적 컨텐츠 요청까지 모두 처리하면 서버 부하가 커지고 동적 컨텐츠 처리가 지연된다 → 페이지 로딩 길어짐

<img src="https://github.com/kkzoone/4Group-Study/assets/65496092/264fef13-7281-48a8-b5c2-aa72bad37f5c" width="800" height="300"/>


정적 컨텐츠는 Web Server가 동적 컨텐츠는 WAS가 담당하도록 분리하여 부하를 줄이면 수행 속도가 빨라진다.

## 2. 여러 대의 WAS를 연결하여 로드밸런싱

- 로드밸런싱(부하분산): 서버가 처리해야 할 업무 혹은 요청(Load)을 여러 대의 서버로 나누어(Balancing) 처리하는 것을 의미

웹 서버를 앞에 두면, 뒤에 여러 대의 WAS를 연결하여 요청을 나누어서 처리하도록 설정할 수 있다.

<img src="https://github.com/kkzoone/4Group-Study/assets/65496092/db3e9dbd-a202-4222-933d-a1e56b5d4e59" width="400" height="300"/>


또한, 여러 대의 WAS를 사용할 때에는 **Health check를 통해** 하나의 WAS에 오류가 발생하더라도 다른 WAS를 통해 사용자가 정상적으로 이용하도록 제어할 수 있다

**→ 안정적인 서버**


# Reference
> [[10분 테코톡] 알리의 Web Server vs WAS](https://youtu.be/mcnJcjbfjrs)
