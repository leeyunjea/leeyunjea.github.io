---
layout: post
title: "Websocket"
date: 2019-04-30
tags: [socket]
categories:
  - Websocket
description:
image: /image/websocket/socketio.jpg
image-sm: /image/websocket/socketio.jpg

---
<!-- {:.post-img.small}
![producer](/image/rabbitmq/producer.png) -->

<h2>서론</h2><br>
<p>Web Browser에서 Request를 보내면 Server는 Response를 준다. HTTP 통신의 기본적인 동작 방식이다. 하지만 Server에서 Client로 특정 동작을 알려야 하는 상황도 있다. 예를 들어 Browser로 Facebook에 접속해 있다가 누군가 친구가 글을 등록하는 경우, 혹은 Web Browser로 메신저를 구현하는 경우다. WebSocket이 있기 전에는 이를 Polling이나 Long polling 등의 방식으로 해결했었다. 하지만 WebSocket의 등장으로 Server-Client 간의 실시간 통신이 가능하게 되면서, 앞으로 Long polling은 역사의 뒤안길로 사라질 것 같다.<br><br>
WebSocket이란 HTTP 환경에서 전이중 통신(full duplex, 2-way communication)을 지원하기 위한 프로토콜로, RFC 6455에 정의되어 있다. HTTP 프로토콜에서 Handshaking을 완료한 후, HTTP로 동작을 하지만, HTTP와는 다른 방식으로 통신을 한다.</p><br>

<h3>Web Socket이란?</h3><br>
  <img src="/image/websocket/what_websocket.png" class="post-img-grande"/>
<br>

<h3>Handshake</h3><br>
<p>WebSocket은 HTTP 기반으로 Handshaking을 한다.</p>
<br>

<h3>Handshake 요청</h3>
<img src="/image/websocket/handshakerequest.png" class="post-img-grande"/>
<ul>
  <li>Connection: Upgrade : HTTP 사용 방식을 변경하자.</li>
  <li>Upgrade : websocket : WebSocket을 사용하자.</li>
  <li>Sec-WebSocket-Protocol: xxx, yyy, zzz : WebSocket을 쓰면서 이 중에서 protocol을 골라서 쓰자.</li>
  <li>Sec-WebSocket-Key : 보안을 위한 요청 키.</li>
</ul>
<br>

<h3>Handshake 응답</h3>
<img src="/image/websocket/handshakeresponse.png" class="post-img-grande"/>
<ul>
  <li>101 Switching Protocols : Handshake 요청 내용을 기반으로 다음부터 WebSocket으로 통신할 수 있다.</li>
  <li>Sec-WebSocket-Accept : 보안을 위한 응답 키 - base64.encode(Sec-WebSocket-Key.concat(GUID))</li>
</ul>
<br>

<h3>WebSocket Server를 운용할 때의 유의사항</h3>
<img src="/image/websocket/cautionhand.png" class="post-img-grande"/>
<p>Spring의 지원을 받아 Web Application Server에서 HTTP를 지원하면서 WebSocket도 지원할 수 있다. 하지만 HTTP와 WebSocket의 개념이 많이 상이하다 보니, WebSocket을 사용해야 한다면 전용 Server를 구축하는 편이 운영하기 쉬울 것으로 판단된다.</p>
<br>

<h3>언제 쓰면 좋을까?</h3><br>
<p>Spring Reference을 참조하면, 자주 + 많은 양의 + 지연이 짧아야 하는 통신을 할 수록 WebSocket이 적합하다고 설명하고 있다. 주로 채팅이나 게임이 이러한 요구 사항을 가질 것이다. 단순한 알림 성격의 뉴스 피드 같은 정보에는 polling이나 streaming 방식이 더욱 단순하고 효율적인 솔루션이 될 수 있다.</p>
<br>

<h3>지원하는 Browser</h3>
<img src="/image/websocket/browser.png" class="post-img-grande"/>
<br>

<h3>SockJS</h3><br>
<p>IE 8, 9는 여전히 많은 인터넷 사용자가 사용하고 있는 브라우저이나, 해당 버전에서는 WebSocket을 지원하지 않는다. Spring에서는 sockjs-client와 합을 맞추어서, 기존 설정에 큰 변경 없이, 마치 WebSocket Polyfill을 사용하는 것과 같은 효과를 낸다.</p>
<br>

<h3>Spring WebSocket 설정</h3><br>
<h5>WebSocket 기본 설정</h5>
<img src="/image/websocket/defaultsocket.png" class="post-img-grande"/>
<br>

<h5>WebSocket SockJS 설정</h5>
<img src="/image/websocket/sockjs.png" class="post-img-grande"/>
<p>.withSockJS()만 추가되었다. Client에서는 GET /myHandler/info를 호출해서 Server의 정보를 취득하며, Client의 지원 여부에 따라 Long polling이나 Polling으로 통신한다. Server 측에서는 위 설정 외에는 소스 코드의 변함없이 운영할 수 있다.<br><br>

채팅 예제는 url -> <a href="https://supawer0728.github.io/2018/03/30/spring-websocket/#%EC%B1%84%ED%8C%85%EB%B0%A9-%EC%9E%85%EC%9E%A5-%EA%B5%AC%ED%98%84">채팅 예제</a></p>
<br>

<h3>현재 채팅 서버의 단점</h3><br>
<p>ChatRoom이 하는 일이 저수준이다. 마치 socket을 직접 처리하는 것 같다. socket을 보유하고 있고, 특정 이벤트가 발생하면 socket에 메시지를 보내고, session이 종료되면 socket을 삭제한다. 지금까지의 소스에서 WebSocket의 기본적인 동작에 대해서 배웠다고 생각하자. Spring에서 지원해주는 STOMP를 사용하게 되면, 지금까지와는 전혀 다른 모습으로 개발을 하게 될 것이다.</p>
<br>

<h2 >STOMP</h2><br>
<p>여태까지의 WebSocket Application은 잊자. STOMP를 사용하게 되면, 사실상 Application에서 직접 session을 처리하는 것이 아니라, 오히려 proxy에 가까운 역할을 하게 된다.</p>
<img src="/image/websocket/proxy.png" class="post-img-grande"/>
<p>앞서 WebSocketHandler에 대해서 설명하기를 message 타입이 binary 혹은 text로 나뉜다고 했었다. message가 binary거나 text이기만 하면 실제 내용물이 무엇이든지 통신이 이루어지는 것이다. 이를 sub-protocol을 사용해서 제어할 수 있다. handshake 과정에서 특정 sub-protocol을 사용하기로 합의할 수 있다. sub-protocol의 하나인 STOMP를 사용하게 되면 단순한 binary, text가 아닌 규격을 갖춘(format) message를 보낼 수 있다.<br><br>

STOMP의 형식은 마치 HTTP와 닮아 있다.
<img src="/image/websocket/likehttp.png" class="post-img-mid"/>
</p>
<ul>
  <li>COMMAND : SEND, SUBSCRIBE를 지시할 수 있다.</li>
  <li>header : 기존의 WebSocket으로는 표현이 불가능한 header를 작성할 수 있다.</li>
  <ul>
  <li>destination : 이 헤더로 메시지를 보내거나(SEND), 구독(SUBSCRIBE)할 수 있다. 이를 통해 간단하게 PUB-SUB를 구현할 수 있다.</li>
  </ul>
</ul>
<br>
<p>예를 들어 ClientA는 아래와 같이 5번 채팅방에 대해 구독을 걸어둘 수 있다.</p>
<img src="/image/websocket/sub.png" class="post-img-large"/>
<p>후에 Client에서 아래와 같이 채팅 메시지를 보낼 수 있다.</p>
<img src="/image/websocket/send.png" class="post-img-large"/>
<p>이때 Server에서는 내용을 기반(chatRoomId)으로 메시지를 전송할 broker에 전달한다.</p>
<img src="/image/websocket/message.png" class="post-img-large"/>
<p>이렇게 /subscribe/chat/room/5를 구독하는 Client에게 메시지가 송신된다.</p>
<img src="/image/websocket/architecture.png" class="post-img-grande"/>
<br>

<h2>STOMP 기반 채팅서버 예제</h2><br>
<p>Spring Reference에서는 동작 방식을 먼저 상세히 설명한 후에 이런저런 소스를 설명하는데, 오히려 소스를 한 번 보고 동작 방식을 설명하는 것이 이해하기 쉬울 것 같아서 예제를 먼저 소개하려고 한다.</p>

<h3>Spring Application 설정</h3><br>
<p>StompWebSocketConfig</p>
<img src="/image/websocket/stompwebsocketconfig.png" class="post-img-grande"/>
<p>구현할 interface의 대상이 WebSocketMessageBrokerConfigurer로 바뀌었다. registerStompEndpoints에서 기존의 WebSocket 설정과 마찬가지로 handshake와 통신을 담당할 endpoint를 지정한다. configureMessageBroker에서 Application 내부에서 사용할 path를 지정할 수 있다.</p>
<ul>
  <li>setApplicationDestinationPrefixes : client에서 SEND 요청을 처리한다.</li>
  <ul>
  <li>Spring Reference에서는 /topic, /queue가 주로 등장하는데 여기서는 이해를 돕기 위해 /publish로 지정하였다.</li>
  <ul>
  <li>/topic : 암시적으로 1:N 전파를 의미한다.</li>
  <li>/queue : 암시적으로 1:1 전파를 의미한다.</li>
  </ul>
  </ul>
  <li>enableSimpleBroker : 해당 경로로 SimpleBroker를 등록한다. SimpleBroker는 해당하는 경로를 SUBSCRIBE하는 client에게 메시지를 전달하는 간단한 작업을 수행한다.</li>
  <li>enableStompBrokerRelay : SimpleBroker의 기능과 외부 message broker(RabbitMQ, ActiveMQ 등)에 메시지를 전달하는 기능을 가지고 있다.</li>
  
</ul>
<br>

<h3>Controller</h3>
<img src="/image/websocket/controller.png" class="post-img-grande"/>
<ul>
  <li>SimpleMessagingTemplate : @EnableWebSocketMessageBroker를 통해서 등록되는 bean이다. 특정 Broker로 메시지를 전달한다.</li>
  <li>@MessageMapping : Client가 SEND를 할 수 있는 경로다. StompWebSocketConfig에서 등록한 applicationDestinationPrfixes와 @MessageMapping의 경로가 합쳐진다.(/publish/chat/join)</li>
</ul><br>
<h4>설명</h4><br>
<p>소스는 위에서 설명한 것이 전부다. session도 Spring이 알아서 관리한다. 개발자가 할 일은 권한과 서비스 로직에 맞게 메시지를 처리하거나, broker로 메시지를 라우팅하는 것이다.</p>
<br>

<h3>메시지 흐름</h3><br>
<p>아래 그림은 Spring Reference에서 가져왔다. 이해하기 어렵다면 /app을 /publish로, /topic을 /subscribe로 치환해서 보자.</p>
<img src="/image/websocket/messageflow.png" class="post-img-grande"/>
<ul>
  <li>SimpAnnotationMethod : @MessageMapping 등 client의 SEND를 받아서 처리한다.</li>
  <li>eSimpleBroker : client의 정보를 메모리 상에 들고 있으며, client로 메시지를 내보낸다.</li>
  <li>channel : 3종류의 channel이 등장한다
  <ul>
  <li>SclientInboundChannel : WebSocket Client로부터 들어오는 요청을 전달하며, WebSocketMessageBrokerConfigurer를 통해 interceptor, taskExecutor를 설정할 수 있다.</li>
  <li>clientOutboundChannel : WebSocket Client로 Server의 메시지를 내보내며, WebSocketMessageBrokerConfigurer를 통해 interceptor, taskExecutor를 설정할 수 있다.</li>
  <li>brokerChannel : Server 내부에서 사용하는 channel이며, 이를 통해 SimpAnnotationMetho는 SimpleBroker의 존재를 직접 알지 못해도 메시지를 전달할 수 있다(결합도를 낮춤)</li>
  </ul>
  </li>
</ul>
<br>

<h3>2대 이상의 서버를 사용한다면?</h3><br>
<p>WebSocket을 사용해서 채팅 서버를 구현하고, 여러 서버를 사용한다면 당연히 아래와 같은 구조가 나올 것이다.</p>
<img src="/image/websocket/multiserver.png" class="post-img-grande"/>

<br>

<h3>마무리</h3><br>
<p>WebSocket에 대해서 알아보고, Spring에서 WebSocket을 어떻게 지원하는지 알아보았다. WebSocket 기술은 Web에서 Socket 통신을 하는 것처럼 동작하며, Spring에서는 이를 STOMP를 사용하여 고수준의 프로그래밍을 가능하게 했다. 간단한 소개가 목적이었기 때문에 Security, Load Balancing, Firewall 등의 주제는 다루지 않았다. 이러한 기술이 있음을 기억하고, 필요할 때 더 깊이 있게 파고들면 좋을 것 같다.<br><br>
WebSocket을 사용하고자 하는 서비스가 Web과 iOS, Android 환경을 모두 지원해야 하는 경우가 있을 수 있다. 이럴 때에는 Web 환경에서는 SockJS를 사용하여 /ws-sockjs로 endpoint를 열어두고, mobile 환경에는 /ws로 endpoint를 열어서 endpoint를 분리해서 운용할 수 있을 것이다. Android나 iOS 둘 다 STOMP를 지원하는 Library가 있는 것은 확인했다. 다만 필자가 해당 분야의 개발자가 아니기에 얼마나 사용성이 있는지는 확인하지 않았다. 가능하다면 STOMP로 동일한 sub-protocol을 운용할 수 있다면 운영하는 데에 큰 도움이 될 것 같다.<br><br>
WebSocket의 사용 여부를 결정하는 것은 사실상 하나로 귀결되는 것 같다. Connection을 유지할 필요가 있는지를 따져보는 것이다.
<ul>
  <li>장점 : Handshake(TCP 상의 통신 준비)를 할 필요가 없으며, 덕분에 지연이 낮다.</li>
  <ul>
  <li>적은 지연, 높은 빈도, 대량의 정보 통신에 유리</li>
  </ul>
  <li>단점 : Connection이 유지되는 동안 항상 통신을 하는 것은 아니다(Connection 낭비)</li>
</ul>
</p>
<br>
