> 이슈 #1 : Spring/Servlet
## Servlet 은 무엇인가?
> Servlet 은 웹 페이지에서의 동적인 처리를 위한 Java 에서 제공하는 표준 인터페이스이다.
> 우리는 이런 Servlet을 구현한 여러 구현체들을 바탕으로 web application을 생성하고 구동한다.  
> 이런 Servlet은 독립적으로 수행되지는 않으며, Servlet Container에의해 생명주기가 관리되어진다.

Q. Servlet은 main 메서드 없이 수행이 가능한가?  
A. 가능하다. Servlet의 생명주기는 Servlet Container가 관리하기 때문이다.  
예를 들어 tomcat으로 HttpServlet을 만들었다면, 이는 별도의 main method 없이도 동작되게된다.

### Servlet의 구현체로 무엇이 있나?
> 대표적으로 Spring 에서 활용되어지는 Dispatcher Servlet이 있다.
> 구체적으로는 Servlet 을 구현한 GenericServlet 이 부모가 되어 HttpServlet이 상속받고, FrontServlet 에 이어  
> DispatcherServlet 까지 이어 상속받게 된다.  즉, DispatcherServlet은 사용자의 요청이 생기면 동작하는 FrontController의 역할을 수행한다.  

## Servlet 과 Servlet Container 에 대한 소개와 둘 간의 관계는?
> Servlet은 java에서 제공하는 웹의 동적인 처리를 위한 표준 스펙이자 인터페이스이고, Servlet Container는 이런 Servlet들의 생명주기를 관리하는  
> 하나의 컨테이너이다. Servlet Container에 의해 각 Servlet 들이 생성되고 소멸되게된다.

### Servlet Container 는 어떻게 Servlet을 관리를 통해 무슨 이점을 얻는가?
> 우선 네트워크 프로그래밍에 있어 가장 기본적인 단계들을 개발자가 생략하고 작업을 수행할 수 있다. 
> 즉, Servlet Container는 웹서버와 통신간 필요한 소켓, 포트리스닝, 스트림생성, 쓰레드 생성등의 작업을 도맡아준다.  
> 그 과정에서 여러 Servlet들이 관리가되어진다.  

## Spring 과 Servlet은 무슨 관계인가?
> Spring에서 사용자 요청을 손쉽게 처리하기 위해, mapping 하기 위해 DispatcherServlet을 활용한다.
> Spring이 없다해서 Servlet만으로도 WAS는 동작할 수 있습니다. 단지 각 요청에 대해, 각 url 에 대해 Servlet 을 구현해줘야하는 복잡성이 생기게 된다.  
> 즉, Spring 은 Servlet을 효율적으로 활용하는 하나의 매커니즘이라고 볼 수 있다.  

## Spring Container와 Servlet Container는 무슨 차이가 있나?
> Spring Container는 Bean instance 들을 대상으로 DI와 IoC작업이 수행되며 관리 작업을 수행하고,  
> Servlet Container는 Servlet instance 들을 대상으로 생명주기를 관리한다.  
> 명확히는 둘을 직접비교하기에는 Servlet Container의 범위가 더 넓다고 판단되며, 둘은 instance들을 관리한다는 공통적인 역할을 수행하지만,  
> 실제 관리되어지는 대상과 수행되어지는 작업에있어서는 차이가 존재한다.  

## Spring 에서 사용자의 요청과 Servlet에서의 처리과정은 ?
> Client 로 부터 요청이 오면, Servlet Container는 HttpServletRequest와 HttpServletResponse 두 인스턴스를 생성하고, 이 두 객체가 모든 처리를 거치게된다. 
> 이때 각각의 Servlet을 거치고 FrontController인 DispatcherServlet 에 도달하게 되면 실제 비즈니스 로직이 구현된 빈들로 작업이 이전되게 된다.  

## DispatcherServlet?
> Spring framework 에서 DispatcherServletAutoConfiguration.class 를 바탕으로 DispatcherServlet이 Bean으로 등록되어지게되며,
> Client의 요청이 수신되면 locale을 비롯한 여러 전처리 작업을 거친후 HandlerMapping 작업이 수행된다. 이때부터 개발자가 등록한 Controller Bean에  
> 어떤 path 로 갈지 정해지게 되며, 해당 Controller로 요청을 전가하게된다. 이과정에서 interceptor를 가는 과정에서 한번, 오는 과정에서 한번씩 거치게 되고, 
> 처리된 작업은 ViewResolver 혹은 HttpMessageConveter를 바탕으로 response 가 처리되어지고, dispatcherServlet을 빠져나가게된다.

## tomcat과 netty의 차이는 무엇인가?
> 가장 큰 차이는 Servlet Based 매커니즘의 차이일것이다.  
> tomcat 은 Servlet Based로, Servlet container 의 구현체로써의 역할을 수행한다. 따라서 Servlet에 의존적이며,  
> 특히 한 요청당 하나의 thread를 할당하는 방식이 적용되었다.  
> netty 는 이런 Servlet based 에서 벗어나 servlet free 한 모습을 보인다. 따라서 Servlet에 의존적이지 않으며,  
> event-driven 형식으로 하나의 event를 받는 thread와 다수의 worker thread로 구성되어 비동기적인 처리를 수행한다.  

### 그럼 tomcat 으로는 비동기 처리가 안되나?
> 그렇지 않다. Servlet 3.0 부터는 비동기 처리를 지원하는 API 들이 등장하였어서, 이후의 버전에 대해서는 비동기 처리를 수행할 수 있다.  
> 하지만 한 요청당 하나의 thread를 할당하는 방식은 유지되어, 이는 성능상 문제가 될 수 있다.

### 어떤 성능상 문제가 발생하는가?
> 동시 요청이 많아 thread pool 의 thread 가 모두 할당되면, 이후 사용자들에 대해서는 대기 큐에서 기다리게 된다. 이런 처리는 병목을 발생시켜 latency 를 증가시킨다.  
