> 이슈 #6 : Spring/java

## JDK 8 에서의 변화는?
> 가장 큰 변화는 람다와 stream API 를 바탕으로 한 함수형 프로그래밍의 지원이다.  
> 이 외에도 Optional 을 통한 null 관리, LocalDate, Concurrent Programming 을 위한 ComplatableFuture 등이 추가되었다.

### API 개발시 null 처리는 어떻게 하는가?
> 기본적으로 Spring Application 이라면, Spring Validation dependency 를 통해 Service Layer로 오기 전 Handling 과정에서 null 허용이 안되는 값에 대해  
> 검증이 수행될것이고, 내부적으로 발생하는 null에 대해서는 method 의 반환값을 Optional 로 하거나, Null Object pattern 을 활용해 직접 null이 로직상에서  
> 노출되는 일을 줄이고자 Wrapping 작업을 수행한다. 추가로 이런 과정에 있어 별도의 Validator 클래스를 구성하여 검증을 수행하는 작업도 좋을것이다.  


### 함수형 프로그래밍은?

## JDK 11 을 많이 사용하는 이유는?
> 우선 JDK 11은 LTS 버전이며, Java8 기반의 함수형 프로그래밍에 더해 10버전에서 추가된 타입추론 var도 사용이 가능하다.  
> 그래도 가장 JDK 11 을 선호하는 이유는 향후 몇년간 JDK 8보다는 지원기간이 길 것이기 때문이라고 생각한다.

## POJO?
> Plain Old Java Object 의 줄임말로, 자바 기본에 충실한 객체를 의미한다. 과거 EJB 시절 특정 의존없이는 순수히 돌아갈수 없던 자바 클래스들에 대해서  
> 이런 외부 의존을 제거하고 코드자체만으로 순수히 사용하기 위한 객체를 가르킨다.  
> POJO 가 강조되는 이유는 객체 자체에 더 집중할 수 있으며, 무엇보다 단위테스트를 수월하게 수행하여 코드의 신뢰성을 담을 수 있기 때문이다.  

### Spring 에서 이런 POJO 를 위한 방법은?
> DI 와 IoC 를 통해 구현된다고 생각된다. 특히 객체지향의 DIP 와 큰 연관이 있는 부분이라고도 생각한다. 
> 이 외에도 AOP, PSA 등이 POJO 를 지키기 위한 Spring 의 원칙이다.

