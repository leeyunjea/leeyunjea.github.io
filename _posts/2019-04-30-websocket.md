---
layout: post
title: "Websocket"
date: 2019-04-30
categories:
  - Websocket
description:
image: /image/websocket/websocket3.png
image-sm: /image/websocket/websocket3.png

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



<!-- <h3>기본 흐름</h3><br>
<p>Exchange를 별도로 명시하지 않은 요청의 기본 흐름</p>
<br>
  <img src="/image/rabbitmq/default_flow.png" class="post-img-large"/>

<ol>
  <li>Producer가 메시지를 생성하여 전송</li>
  <li>Queue가 이 메시지를 순차적으로 쌓음</li>
  <li>Consumer가 Queue에 대한 Binding을 가지고 있다가 메시지를 Queue에서 수신</li>
</ol>
<br>

<h3>상세 흐름</h3><br>

<ol>
  <li>Producer가 메시지를 생성하여 전송</li>
  <li>Exchange가 어떤 Queue에 전달할지를 Routing</li>
  <li>Queue가 이 메시지를 순차적으로 쌓음</li>
  <li>Consumer가 Queue에 대한 Binding을 가지고 있다가 메시지를 Queue에서 수신</li>
</ol>
<br>

<h3>메시지 분배 (Round-robin dispatching)</h3><br>
<p>RabbitMQ는 Consumer가 병렬처리를 쉽게 할 수 있도록 같은 Queue를 바라보고 있는 Consumer에게 메시지를 균등 분배한다.
즉 첫번째 메시지는 Consumer1에게 두번째 메시지는 Consumer2에게 분배(중복 처리하지 않도록 1명에게만 줌)
MQ의 존재 이유를 보여주는 매우 중요한 개념이다. 이로 인해 메시지를 받아 처리하는 프로그램들은 수평 확장이 가능하다.물론 Producer도 수평 확장이 가능하다. (같은 Queue에 메시지를 던져주기만 하면 됨)
</p>
<br>

<h3>Fair dispatch(공평한 분배)</h3><br>
<p>여러 consumer에게 round robin할 때 번갈아가면서 메시지를 전달하지만 완전히 공평하진 않음(매홀수는 데이터크기가 크고, 매짝수는 데이터크기가 작은 등)
때문에 busy한 서버에게 메시지를 계속 전달하지 않도록 prefetchCount라는 개념사용.prefetchCount가 1일때는 아직 ack를 받지 못한 메시지가 1개라도 있으면 다시 그 consumer에게 메시지 할당하지 않음.즉 prefetchCount는 동시에 보내는 메시지 양임
</p><br>
  <img src="/image/rabbitmq/fair_dispatch.png" class="post-img-large"/>
<br>

<h3>메시지 수신 통보 (Acknowledgment)</h3><br>
<p>많은 프로토콜들이 메시지 전달 보장을 위해 Acknowlegment(ACK)라는 개념을 사용하여 메시지에 대한 응답을 보내주도록 되어있다.MQTT의 경우는 QoS(Quality of Service) level이라고해서 0=안보냄, 1=전달완료확인, 2=최종목적지까지 처리완료 확인의 개념이 있는데RabbitMQ의 경우는 Acknowledgment(Consumer전달확인)와 Confirm(Publish전달확인)을 이용한 level 1만 지원하는 듯 하다.(추정)
ACK가 중요한 이유는 Queue는 Consumer에게 데이터를 전달하고나면 Queue에서 메시지를 삭제하므로, Consumer가 전달받았지만 처리도중 오류가 발생하여 재처리해야하는 경우를 위해 보관 유예기간을 두는 용도로 이용된다. 즉 ACK가 온 메시지만 삭제처리하도록 하는 것이다.<br>
※ 주의 basicQos라는 설정은 prefetchCount의 숫자 설정임(!! QoS level 0,1,2의 개념 아님!!)
</p><br>
  <img src="/image/rabbitmq/acknowledge.png" class="post-img-large"/>
<br>

<h3>여러 Client(Consumer) 중 일부가 죽었을시 대응방법</h3><br>
<p>앞서 설명했듯이 Consumer가 ACK를 보내지 않으면 Queue에 쌓인 메시지는 지워지지 않고 남아있다.메시지를 받은 Consumer가 느려서 아직 Processing 중일 수 있어서 다른 Consumer에게 Round Robin하여 재전송 하자니 중복처리 우려가 있고,그대로 Unacknowledged 상태로 마냥 처리 안된채로 남겨 둘 수도 없고 곤란하다.
이 경우에 어떻게 동작하는지 확인해볼 필요가 있다.<br><br>

같은 메시지에 대하여 바인딩되어있는 Consumer가 2개가 있다. 하나는 ACK를 재대로 보내는 Consumer고 하나는 오류 상황이 발생하여 ACK를 보내지 못하는 상황이다. prefetchCount는 1로 하나라도 Unacknowledge 상태라면 재전송을 하지 않도록 설정한다.<br>
  <img src="/image/rabbitmq/many_client_die.png" class="post-img-large"/>


<br><br>
위에서 보는 것 처럼 Acknowledge 메시지가 돌아오지 않으면 Queue는 메시지를 삭제하지 않고 보관하고 있다가, Consumer가 이를 처리하지 못한다고 판단했을 때 (Disconnected 되는 등의 상황) 메시지를 다음 순번의 Consumer(Worker)에게 라운드 로빈한다.
</p>
<br>

<h4>RabbitMQ가 재기동 됐을때 대응책</h4><br>
<p>메시지를 Queue에 넣은 뒤 Consumer에게 전달하기 전에 RabittMQ 서버가 죽는다면기본적으로 해당 메시지는 날라가버리게 된다.이런 상황을 방지 하기 위해 durable이라는 개념을 가지고 있다.
</p>
<br>

<h4>Message durability</h4><br>
<p>메시지는 Queue에 보관할 때 file에도 같이 쓰도록 만드는 방법이다.
아래와 같은 방법으로 설정해야 동작한다.
queue생성시 durable속성을 true로 주고 만든다.
message publish할때 MessageProperties.PERSISTENT_TEXT_PLAIN을 설정함
1,2번 모두 만족해야 메시지가 Queue에 남아있을 때 restart해도 날라가지 않는다.<br><br>
  <img src="/image/rabbitmq/message_duarability.png" class="post-img-large"/>

※ 메시지의 persistent는 완변히 보장되진 않음. 메번 메시지마다 fsync 로 동기화히지 않기 때문에 짧은시간이나마 아직 Disk에 쓰여지지 않았을 경우가 있다. 좀더 강력한 방법을 보장하기 위해서는 publisher confirms를 사용

</p>
<br>

<h3>Publish/Subscribe</h3><br>
<p>한 메시지를 여러 Consumer가 동시에 받아 사용해야 하는 경우가 있다. 이러한 Publish/Subscribe 구조를 RabbitMQ로 구성하여 사용 할 수 있다.
</p><br>

<h4>기본 exchanges</h4>
<p>: RabbitMQ설치시 기본적으로 세팅된 exchanges
</p>
<ul>
  <li>amq.direct direct</li>
  <li>amq.fanout fanout</li>
  <li>amq.headers headers</li>
  <li>amq.match headers</li>
  <li>amq.rabbitmq.log topic</li>
  <li>amq.rabbitmq.trace topic</li>
  <li>amq.topic topic</li>
</ul><br>

<h4>이름없는 exchanges</h4>
<p>Producer가 메시지를 보낼 때 exchange를 지정해서 보낼 수 있는데, 이름이없다면 routingKey라는 특정 key값으로 Queue에 라우팅 된다.즉 exchange명이 있거나 routingKey명이 있어야 한다.<br>
channel.basicPublish("", "hello", null, message.getBytes());
<br><br>
channel.basicPublish( "logs", "", null, message.getBytes());

</p>
<br>

<h4>임시 Queue</h4><br>
<p>이전에 사용한 Queue를 기억하고 계속적으로 사용하기 위해서는 같은 이름의 Queue를 Worker가 바라보도록 하는 것이 중요하다.그러나 fanout을 받아 처리하는 Queue인 경우 Consumer가 임시 이름이라도 지정해줘야하는데 다음 2가지가 고려 되어야 한다.<br>
 
RabbitMQ 연결시마다 서버에서 발급한 random Queue 이름의 사용<br>
 
연결이 종료 되었을 때 Queue의 자동삭제<br>
  <img src="/image/rabbitmq/temp_queue.png" class="post-img-large"/>

String queueName = channel.queueDeclare().getQueue();
이런식의 amq.gen-JzTY20BRgKO-HjmUJj0wLg Random Queue가 사용된다.
</p>
<br>

<h4>바인딩 (Bindings)</h4><br>
<p>생성한 exchange를 queue에 매핑을 해줘야 exchange가 메시지를 받으면 Queue에 메시지를 보낼 수 있는데이를 Bindings 이라고 함.<br>
channel.queueBind(queueName, "logs", "");
</p>
<br>

<h4>Routing</h4><br>
<p>모든 메시지를 받는 것이 아니라 선별정으로 특정 종류의 메시지를 받도록 subset을 설정하여 멀티케스트 함
</p>
<br>

<h4>Direct exchange</h4><br>
<p>exchange type 중 direct을 이용해서 exchange를 만들면 부분적으로 메시지를 라우팅 하도록 처리 할 수 있다.
<br>
// 특정 EXCHANGE_NAME 으로 "direct' exchange를 생성<br>
channel.exchangeDeclare(EXCHANGE_NAME, "direct");
</p>
<br>

<h4>Producer</h4><br>
<p>메시지를 publish할 때 routingKey값을 주어 선별적인 Queue에만 메시지를 전달 한다.
<br>
// 특정 EXCHANGE_NAME에 특정 "routingKey"로 binding된 queue에게만 메시지 전송
channel.basicPublish(EXCHANGE_NAME, routingKey, null, message.getBytes());

</p>
<br>

<h4>Consumer</h4><br>
<p>메시지를 받는 Consumer, Worker에서 queue bind시 routingKey값을 주어 선별적으로 수신 한다.
<br>
// 특정 EXCHANGE_NAME의 특정 routingKey만 전달 받도록 binding 함<br>
// 여러 routingKey와 bind 될 수 있다.<br>
channel.queueBind(queueName, EXCHANGE_NAME, routingKey1);<br>
channel.queueBind(queueName, EXCHANGE_NAME, routingKey2);<br>
</p>
<br>

<h4>Multiple binding</h4><br>
<p>Exchange와 Queue간에 n:m binding이 가능하다.<br>
1개의 Queue는 여러 routingKey와 binging 할 수 있다.여러개의 Queue는 같은 routingKey와 binding 할 수 있다.
</p>
<br>

<h3>패턴매칭 라우팅(Topics)</h3><br>
<p>RabbitMQ는 Topic이라는 개념을 통해 패턴 매칭식 멀티케스트 라우팅을 지원한다.
topic exchange를 생성하여 특정 문자열 패턴에 일치하는 Queue에만 데이터를 보내주도록 구성할 수 있다.
</p>
<br>

<h4>Topic exchange</h4><br>
<p>exchange type 중 topic을 이용해서 exchange를 만들면 특정 패턴에 일치하는 routingKey로 메시지가 전송된 경우 메시지를 수신 할 수 있다.
해당 패턴은 다음의 특수 문자를 사용하여 구성한다.<br>
channel.exchangeDeclare(EXCHANGE_NAME, "topic");
</p>
<br>

<h4>Topic Pattern</h4><br>
<p>*<br>
여러 Word를 나타냄 (1 Word 는 .로 구분됨 )<br>
#<br>
여러 Word를 나타냄 (1 Word 는 .로 구분됨 )<br>
※ Topic이 # 으로만 구성되면 fanout과 동일하다.※ Topic에 *나 # 문자가 하나도 없다면 direct와 동일하다.
</p>
<br>

<h4>예제</h4><br>
<p>quick으로 시작되는 .으로 구분되는 4개의 Word로 구성된 문자열<br>
quick.*.*.*<br>
quick.test.good.job => 매칭<br>
front.quick.test.good.job => 비매칭<br>
#.end<br>
front.mid.end => 매칭<br>
front.end => 매칭<br>
front.mid.end.last => 비매칭

</p>
<br>

<h4>Producer</h4><br>
<p>메시지를 publish할 때 routingKey값을 주어 선별적인 Queue에만 메시지를 전달 한다.<br>

// 특정 EXCHANGE_NAME에 특정 "routingKey"로 binding된 queue에게만 메시지 전송 (메시지 전송시는 direct와 별반 다를바 없음)
channel.basicPublish(EXCHANGE_NAME, routingKey, null, message.getBytes());
</p>
<br>

<h4>Consumer</h4><br>
<p>메시지를 받는 Consumer, Worker에서 queue bind시 routingKey의 패턴 값을 주어 선별적으로 수신 한다.<br><br>

// 특정 EXCHANGE_NAME의 특정 패턴을 가진 문자열로 routingKey만 전달 받도록 binding 함<br>
// 여러 routingKey와 bind 될 수 있다.<br><br>

String patternedRoutingKey1 = "kern.*";<br>
String patternedRoutingKey1 = "*.critical";<br><br>

channel.queueBind(queueName, EXCHANGE_NAME, patternedRoutingKey1);<br>
channel.queueBind(queueName, EXCHANGE_NAME, patternedRoutingKey2);<br>
</p>
<br>

<h4>원격 프로시져 호출 (RPC)</h4><br>
<p>RabbitMQ는 Request-Response로 Client와 Server를 이어주기 위해 RPC라는 개념으로 기능을 제공한다.
RPC라는 거창한 이름을 사용하였지만 실제로는 Client의 request를 Server에 전달하고, Server가 처리한 결과를 알맞은 Client 요청에 대한 응답으로 전달 할 수 있는 방법을 말한다.
</p>
<br>

<h5>Message Properties 설명</h5><br>
<p>DeliveryMode : persistent인지 transient인지 표시 (휘발성인지 비휘발성인지 구분자)<br>
ContentType : 내용물의 mime-type<br>
ReplyTo : Callback Queue의 이름<br>
CorrelationID : 요청을 구분할 수 있는 유일값<br>

</p>
<br>

<h5>처리 흐름</h5><br>
<ol>
  <li>Client가 CorrelationID, ReplyTo 주어서 RabbitMQ의 특정 Request보관용 Queue에 데이터를 Push한다.</li>
  <li>Request용 Queue에 데이터를 Server에서 Consume하여 요청을 처리한다.</li>
  <li>요청처리 후 Request에서 받은 CorrelationID 와 ReplyTo를 추출하여, 요청ID를 속성으로 갖는 Response를 ReplyTo Queue에 Push한다.</li>
  <li>Client는 ReplyTo Queue를 subscribe하고 있다가 Response가 오면 CorrelationID를 보고 어떤 요청에 대한 응답인지를 구분하여 처리한다.</li>
</ol>
<br>

<h5>RPC의 이점</h5><br>
<ul>
  <li>서버처리 이점 : 서버 처리속도가 느려서 성능을 증가 시키려고 할 때, RPC 서버를 하나 더 두고 같은 Request Queue를 바라보게 하면 됨 ( Round Robin 하므로 )</li>
  <li>Client 이점 : 하나의 메시지를 개별 Round Trip으로 처리를 위해 queueDeclare같은 동기처리 요청이 필요없다. <br>
  -> (임시 Queue를 생성하여 Client마다 다른 Queue를 사용하므로)</li>
  <li>RPC 구성시 고려할 점<br>
    돌아가는 서버가 없을 때 Client 처리<br>
    요청 Timeout시 Client 처리<br>
    서버 Exception이나 오동작시 Client에게 이를 어떻게 전달할지<br>
    Invalid한 데이터가 서버로 전달 되었을 때의 처리
  </li>
</ul>
<br>

<h3>RabbitMQ Queue 와 Topic 의 차이점</h3><br>
<h5>개념도</h5>
  <img src="/image/rabbitmq/default.png" class="post-img-large"/>

<ul>
  <li>큐를 사용한 방법<br>
    -큐를 미리 만들어두고 클라이언트가 각각 하나씩 빼가도록 만들수 있습니다.<br>
    -두개의 클라이언트는 각각 한번씩 큐를 꺼내가는것이 가능합니다.<br>
    -> A클라이언트가 한개의 요청을 가져가면 B는 이벤트가 발생하지 않습니다.
  </li><br>
  <li>Topic를 사용한 방법<br>
    -해당 토픽으로 온 요청을 /topic/noti 로 두개의 큐에 각각 쌓게 됩니다.<br>
    -sub 포인트를 지정해주면 자동으로 각각의 큐가 만들어지며 구독이 가능합니다.
</li><br>
  <li>RPC 구성시 고려할 점<br>
    -돌아가는 서버가 없을 때 Client 처리<br>
    -요청 Timeout시 Client 처리<br>
    -서버 Exception이나 오동작시 Client에게 이를 어떻게 전달할지<br>
    -Invalid한 데이터가 서버로 전달 되었을 때의 처리
  </li>
</ul>
<br>

<br><br><br><br>
<p>참고 블로그</p>
설치방법 : <a href="http://blog.saltfactory.net/install-rabbitmq/">http://blog.saltfactory.net/install-rabbitmq/<br>

기본기동 : <a href="http://bcho.tistory.com/834?category=492244">http://bcho.tistory.com/834?category=492244<br> -->
