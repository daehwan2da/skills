> 이슈 #5, Spring/WebFlux

## WebFlux란 무엇이고 왜 생겨 났는가?
> Spring WebFlux는 기존 Spring Web MVC 와 달리 non-blocking, 비동기 처리에 집중하기 위해 생겨난 모듈이다.  
> 기술이 발전하고 인터넷 사용이 증가함에따라 동시 접속과 트래픽에 대한 위험도 높아졌다. 이때 기존 Spring Web MVC의 방식으로는 자원의 한계에 도달할수 있기 때문에,
> 적은 수의 resource로도 많은 요청을 동시에 처리하여 트래픽에 강해질 필요가 있었다. 그래서 reactive programming 기반의 Spring WebFlux 모듈이Spring framework 5 부터 등장하였다.  

### Spring framework 5 부터 달라진 점은 무엇인가?
> JDK 8+ 으로 호환이 가능하며, kotlin 지원이 추가되어 소위 '코프링'이 시작되었다.  
> 또한 hibernate, Junit 등 여러 프레임워크의 버전이 상위호환되었으며  
> 가장 큰 변화는 WebFlux 를 통한 Reactive Programming 지원이다.  

### Spring WebFlux 는 어떤 방식으로 적은 리소스로 많은 트래픽을 감당하는가?
> latency가 가장 많이 발생하는 주된 원인은 I/O 이다. 이런 I/O 처리 방식을 non-blocking I/O 대기시간을 대폭 감소하며,  
> Event-Driven 방식으로 비동기적으로 데이터를 처리할 수 있도록 구성했기 때문에 가능하다.

### Blocking 방식에서 non-blocking 처럼 여러 작업을 동시에 수행하도록 하는 방법은 없는가?
> 있다. 병렬 프로그래밍을 수행하며, 특히 멀티 thread 기반의 Spring 환경에서 수행이 가능하다.  
> thread를 필요한 만큼 할당하여 병렬적으로 작업을 수행하면된다.  
> 하지만 이 방식은 자원의 한계에 도달할 수 있고, 특히 Context Switching 에 대한 overhead 가 발생한다.  

### Reactive programming 이란?
> 기존 blocking, 동기 방식에서 벗어나 non-blocking 그리고 비동기 처리를 수행하는 것을 의미한다.  
> 특히 모든 요청을 적시에 응답할 수 있어야하며, 지속적으로 유동적인 부하와 자원에 대해 응답성이 유지되고,  
> 느슨한 결합 (loose coupling) 방식을 사용하여 비동기적으로 전달되는 메시지 기반의 프로그래밍 방식을 의미한다.  

### 느슨한 결합이란? 혹은 그 반대는 무엇을 의미하는가?
> 느슨한 결합은 시스템간 혹은 서비스간 의존을 최소화하여 Runtime 의존성을 최소화하기 위한 방식이다.  
> 즉, 하나의 component의 변경이 다른 component의 변경을 요구하는 위험을 줄이는 것을 목표로 한다.  
> 강한 결합은 반대로 서로 의존이 심해 다른 component에 끼치는 영향이 심한 것을 의미한다.  

### 느슨한 결합을 위한 메커니즘이 무엇이 있는가?
> 대표적으로 observer pattern에 기반한 pub/sub model이 있고, 이 pub/sub model은 이벤트 기반 비동기 처리에 있어 핵심적인 모델이된다. 

### observer pattern이란?
> 객체의 상태 변화를 관찰하는 관찰자들을 객체에 등록하여 객체의 상태가 변화하면 관찰자들에게 변화한 상태를 통지하는 디자인 패턴이다.  

## Spring WebFlux와 기존의 Spring Web MVC 와의 차이는 무엇일까?
> 가장 큰 차이는 동기, 비동기에 대한 패러다임과 메커니즘의 차이일것이다.
> Spring Web MVC 는 blocking 이고 동기적인 처리가 기본적으로 수행되며, Servlet 에 의존적이고 Servlet Container 에 의존적이다.  
> Spring WebFlux는 non-blocking 과 비동기를 기반으로 하며, Servlet의 의존을 제거하여 Servlet container 없이도 서버 구동이 가능하다.

### 왜 Spring Web MVC 는 Servlet 에 의존적인가?
> Spring Web MVC 는 Servlet spce 에 기반하여 만들어졌으며, 대표적으로 DispatcherServlet이 구동되기 때문에 Tomcat 과 같은 WAS에 의존적이다.  

### tomcat 이 없으면 Spring Web MVC는 구동이 안되나?
> main 함수의 실행을 통해 application 으로서의 구동은 될것이나, socket생성과 port listening 등 WAS로써의 역할은 수행되지 못할것이다.  

### tomcat 과 netty의 차이는?
> 가장 큰 차이는 Servlet container로의 역할을 수행하느냐 에 있다. tomcat은 Servlet Container의 구현체이며, netty는 Servlet에 의존적이지 않은 웹서버를 구동한다.  
> 다음으로 tomcat은 요청 당 하나의 thread가 동작하며, netty의 경우네는 Event-Driven 기반으로 하나의 event 를 받는 thread와 worker thread를 분리하여 동작하게 된다.  

### Spring Web MVC 는 비동기 처리를 지원하지 않는가?
> 아니다. Servlet 3.0 부터 비동기처리를 지원하는 API 들이 제공되었기 때문에, Spring Web MVC 에서도 비동기처리 수행이 가능하다.  
> 하지만 앞서 말한 tomcat 의 특성상 요청당 쓰레드를 배정하면 thread pool에 더이상 할당해줄 thread 가 없을때 시스템 성능상 큰 문제가 발생하게 된다. (thread pool hell) 
> 이는 전체 시스템에 대해 reative 하다고 볼 수 없다.

### Spring Web MVC 로 reactive programming 을 하지않고도 많은 트래픽을 받아낼수 있는 방법이 있을까?
> application level 보다는 인프라 전체적인 시각에서 트래픽을 해소해보려는 시도를 할 필요가 있다.
> cpu, core, memory 등 자원 자체를 향상시키는 스케일업과 로드밸런싱을 활용하여 여러 인스턴스를 운용하는 스케일 아웃이 방법이 될것이며, 
> CDN, redis 등 캐시 서버의 적절한 배치도 트래픽을 완화할 방법이 될것이다.   
> 하지만 이는 상대적으로 비용, 인적으로 리소스가 많이 필요한 작업이 될것이다.


## java 에서 stream() 과 reactive 에서 이야기하는 stream 은 다른것인가?
> 근본적으로 수행하는 역할에 대해서는 연쇄작업을 chainning 하는 방식을 한다는 것에 공통점이 있을수 있으나,  
> 실제 활용되는 곳과 그 대상에는 차이가 존재한다.  
> java 에서의 stream은 애초에 java.utils 에 포함된 라이브러리로, JDK 8 부터 함수형 프로그래밍에 최적화된 방식을 위해 도입되었으며,  
> 단순히 주어진 데이터에 대해 식이 연쇄작업으로 수행되는 것을 도와준다.  
> 그러나 reactive 에서의 stream, 즉 reactive stream은 JDK 9부터 도입되었으며, 이는 java 진영의 서로 다른 reactive programming 라이브러리
> RxJava 나 projectReactor에 대해 표준을 제공한다.  이는 데이터 I/O에 관여하며 non-blocking back-pressure 를 기반으로 한 비동기 스트림의 표준이다.  

### back-pressure 란?
> 비동기 처리에있어, 한 component가 지속적으로 데이터를 끊임 없이 보내고 받게되면, 어느 순간에는 자원의 한게에 도달할 수 있다.  
> pub-sub model에 있어 만약 구독자가 받지 못할 만큼의 데이터가 전달되면 out of memory의 문제가 발생할 수 있다.  
> 따라서 미리 발행자에게 자신이 얼마만큼 더 수용이 가능한지 전달하여 발행자는 유동적으로 구독자들에게 데이터를 stream 바탕으로 전달하게 되는 것을 의미한다.  

