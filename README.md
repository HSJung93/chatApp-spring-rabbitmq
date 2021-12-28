## 스프링과 RabbitMQ를 사용한 실시간 채팅 어플리케이션
* RabbitMQ가 설치되어 있어야 한다.
* `rabbitmq-plugins enable rabbitmq_stomp`로 RabbitMQ 실행 후 어플리케이션 실행.
* 리퀘스트 웹소켓 채널과 리스폰스 웹소켓 채널이 어플리케이션과 클라이언트를 연결한다.

![아키텍쳐](./docs/spring-boot-websocket-chat-architecture.jpg)

### STOMP(Simple Text Oriented Messaging Protocol) 프로토콜
* UI 클라이언트(브라우저)에서 메세지 브로커와 연결할 때 사용된다.
* SEND, SUBSCRIBE 명령어를 보내어 destination 헤더에 메세지를 구독한다. 
* destination 헤더에는 메세지 설명과 수신 정보가 적혀있다.\

### Spring의 STOMP 지원
* 스프링의 웹소켓 앱은 클라이언트 입장에서 STOMP 브로커처럼 동작한다.
* 메세지는 `@Controller`의 메세지 핸들링 메소드로 라우팅되거나 유저들에게 메세지를 발송하고 기록하는 간단한 인메모리 브로커로 라우팅된다.
* 물론 특정한 STOMP 브로커(RabbitMQ, ActiveMQ)를 사용할 수 있으며, 이 어플리케이션에서는 RabbitMQ를 사용한다.

### 어플리케이션 설명
* `configureMessageBroker()` 메소드가 RabbitMQ 메세지 브로커가 메시지를 운반하여 `/topic`과 `/queue` 클라이언트로 전달되도록 한다. 
* `/app` prefix를 가진 메세지의 경우 `@MessageMapping` 어노테이션의 메소드로 라우팅된다. 
* 예를 들어 `/app/chat.sendMessage` 엔드포인트는 `WebSocketController.sendMessage()` 메소드에서 다루어지도록 매핑되어 있다.

![플로우차트](./docs/spring-boot-websocket-chat-flow.jpg)

* WebSocket Listener class에서 