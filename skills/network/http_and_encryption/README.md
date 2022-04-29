> 이슈 #4 : Network/HTTP

## HTTP 각 version 별 설명 및 차이는?
> 우선 HTTP 는 application layer의 protocol 이며, TCP/IP 에 의존한다. 
> 1.0 은 HTTP header가 도입되었고, status code 가 적용되어 간편하게 결과를 확인하고 오류를 유추할 수 있었다.
> 또한 GET 외에도 POST 와 HEAD 메서드가 생겨났다. (기존에는 GET 만 존재)
> 그러나 문제점은 매 요청마다 connection 이 유지되지 않았다는 점이다. 그래서 매 요청마다 3-way-handshake 를 통한 connection을 생성해주어야했다.
> 이런 문제점을 해결하고자 1.1 version이 1년 만에 등장하였다. 
> 1.1 은 keep-alive 속성을 도입하여 connection 이 일정 시간동안 유지되게 해주어, 네트워크 비용을 줄였다.
> 또한 PUT, PATCH, DELETE 등 method 들이 새롭게 도입되었다.
> 2.0 은 처음부터 프로토콜의 성능 향상에 포커싱을 하여 생겨났다.
> 비동기적인 데이터 요청을 통해 HTTP 1.1 에서 동기적인 데이터 요청에의해 발생하는 HoL 의 문제를 해결하고자했으며, 그래도 근본적인 TCP HoL 문제는 해결하지 못한다.
> 또한 자동 압축을 제공하고, 일괄요청에 대해 우선순위를 부여하여 CSS 를 JS 보다 우선 받아 페이지를 구성하는 등 성능 향상에 집중되었다.
> 또한 기존 아스키 기반의 데이터에서 binary 기반의 프로토콜로 변화하였다.


### HoL Blocking 이란?
> Head of Line Blocking 을 의미하며,
> 순서가 보장되어야하는 통신에 있어 가장 첫번째로오는 데이터에 대해 기다리는 현상이다.
> HTTP 1.1 에서는 application layer 에서 필요한 데이터들에 대해 동기적으로 수행되어, 첫번째 데이터가 오지않으면 다음 데이터들도 받아지지 않는 문제가 발생했다.
> HTTP 2.0 에서는 이 문제를 해결하고자 비동기적으로 데이터 요청을 전송하여 어느정도 해소하였으나, TCP 프로토콜을 기반으로 하기에,
> 근본적인 TCP layer 에서 발생하는 패킷들에 대한 HoL 문제는 해결하지 못한다.

## SSL/TLS

### SSL 과 TLS 는 OSI 7계층 중 어떤 Layer에 속할까?
> OSI 7계층으로 보면 특정 layer에 속한다고 말하기 어렵다. 물론 상위 3계층 내에서 이뤄지는 작업이므로 application, presentation, session 계층 각 어느 계층에 대해
> 속한다고 이야기 할 수 있을것이다. 다만 TCP/IP model 을 기준으로 본다면, 최상위 5계층인 application layer에 속한다고 할 수 있다.

### SSL 과 TLS 의 차이는?
> 일반적으로 둘은 혼용하여 많이 사용되었지만, 실제로 SSL 은 국제 표준화 기구에서 Deprecated 되었다.
> 큰 차이로 꼽자면 SSL은 웹 서버와 브라우저 간에 암호화된 링크를 제공하는 것에 집중하였으며,
> TLS 는 통신 자체에 대한 암호화에 집중하였다.

### HTTPS 의 원리는?
> TLS 는 대칭키 방식과 비대칭키 방식을 혼합하여 암호화 작업을 수행한다.
> 이때 TCP 기반의 3-way-handshake가 이루어진 후에, TLS handshake가 일어난다.
> 1. Client 는 Server 로 자신이 사용가능한 암호화 알고리즘과 SSL/TLS 버전을 담아 "HELLO" 요청을 보낸다.
> 2. Server 는 Client 로 선택한 암호화 알고리즘과 자신의 인증서, 비대칭키의 public key를 "HELLO" 요청으로 응답한다.
> 3. Client 는 받은 Server의 인증서를 CA에 검증한다.
> 4. 검증이 완료되면 Client 는 Server 로 부터 받은 public key 로 대칭키를 암호화하여 전송하고, "FINISHED" 요청을 보낸다.
> 5. Server 는 받은 대칭키를 본인의 private key로 복호화하고 마찬가지로 Client 에게 "FINISHED"를 응답한다.
> 이후에는 암호화 기반의 신뢰성 있는 통신이 이루어진다.

### 왜 비대칭과 대칭을 혼합해 사용하는가?
> 비대칭키로 양방향 전부 암호화하여 사용하는것이 가장 이상적인 방법일것이다.
> 하지만 비대칭 키의 암호화방식은 cpu resourse를 많이 사용하게 되어 빈번하게 발생하는 HTTPS 요청이 무거워질것이다.
> 따라서 전문은 빠르고 간단한 대칭키로 암호화하고, 대칭키를 비대칭키로 암호화하는 혼합의 방식을 사용한다.

### 어느 데이터까지 암호화가 될까? header 도 암호화가 될까?
> 우선 http 프로토콜 자체가 Application layer 에서 발생하는것이기 때문에, HTTP 요청의 header는 암호화가 된다.
> 하지만 TCP/IP packet의 header의 경우에는 어떤 encryption의 정책을 따르냐에 따라서 암호화가 될수도, 안될수도있다.

### 어떤 경우 TCP/IP packet의 header가 암호화되고 암호화가 안되나?
> 일반적인 경우에는 link encryption 방식으로 header 가 암호화되며, 이 방식은 src와 dest 간 모든 노드들이 암,복호화를 수행하게된다.
> 장점은 header 가 노출되지 않아 tracking 을 방지할 수 있다는 점이다.
> 반면 end-to-end encryption (E2EE) 방식은 src와 dest 만 암복호화를 수행해 둘만 message를 확인할 수 있으며, 더 안전한 보안을 제공하나,
> 이때 중간 노드들이 패킷을 전달하기 위해 header가 암호화되지 않는다. 이는 외부로 노출된 header에 의해 tracking 이 발생할 수 있다.

### E2EE 방식의 실제 사례는?
> 1대1 채팅의 경우에는 많은 기업들이 E2EE방식을 채택한것으로 알고있고, 특히 Apple의 imessage가 있다.
> 뿐만 아니라, 금융거래에있어서도 많이 활용된다.

