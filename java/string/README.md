
## String 은 어떤 종류의 객체인가?
> String은 Reference type이자 immuatable 한 instance의 성격을 띈다.  

### String a = "abc" 와 String b = "abc" 에 대해서 a==b 가 성립되는가?
> 된다. 이유는 String 이 불변객체이면서, Heap 영역에 String pool이라는 별도의 메모리 상에서 관리되어지기 때문이다.

### String pool?
> String pool은 Heap 영역에 위치해서 String 의 cache 역할을 수행한다.   
> 즉, 같은 내용을 갖는 String에 대해서는 새롭게 객체를 생성하지 않고, 기존에 존재하는 인스턴스를 반환해준다.  

### 왜 이런 방식?
> 우선 String 이 immutable 함으로써 갖는 장점은 앞서 말한 캐싱을 바탕으로한 메모리 절약과 동기화에 강하다는 점이 있다.

### String 과 StringBuilder, StringBuffer 의 차이는?
> 우선 String과 나머지 둘의 차이는 불변 여부이다. String은 Heap의 String pool에 값이 불변인 상태로 저장되는 방식이고,  
> StringBuilder 와 StringBuffer 의 경우에는 단순 인스턴스 취급을 받는다. StringBuilder 와 StringBuffer 의 차이는 동기화와 이에따른 성능에 있다.  
> StringBuffer의 경우에는 synchronized block 이 적용되어 멀티쓰레드 환경에서 thread safe 함을 보장받을 수 있다. 허나 StringBuilder의 경우에는 
> 동기화와 관련된 처리가 되어있지않아 멀티쓰레드 환경에서 사용하기 어렵다.  성능은 StringBuilder가 뛰어나겠지만, 멀티쓰레드환경에서는 StringBuffer를 사용하는 것이
> 올바를 것이다. 

### String 이 불변객체라면, concat은 어떻게 이루어지는가?
> String 은 concat 을 자체적으로 + 로 연결하는 방식으로 구현하였다.  문제는 기존 JDK 1.5 이전 에서는 새로운 불변객체를 반복해서 만들어 concat이 이루어졌다는 것이다.  
> 즉 "a" + "b" + "c" 라고 하면 각각을 인스턴스로 만들고 "abc"가 또 인스턴스로 만들어져 메모리가 낭비되어졌다. 이를 해결하기 위해
> JDK 1.5 부터는 String 내부적으로 + 연산에 대해서 StringBuilder를 활용해 구현하는 것으로 알고있다.


