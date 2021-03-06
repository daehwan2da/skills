> 이슈 #4, network/csrf

## cookie 와 session 은 무엇이고 무슨 차이가 있을까?
> cookie와 session은 주로 stateless 한 HTTP 통신에 대해 상태를 나타내주기 위한 방식이다.
> cookie 는 server가 client 에게 보내는 작은 데이터조각, 브라우저는 쿠키를 저장하고 요청에 맞게 쿠키를 서버에 전송한다.
> 이때 cookie는 server에서 session의 key값으로 인증과 관련되어 활용되어지기도 하지만, 사용자의 정보, 상태 및 tracking 에 활용되기도 한다.  
> session 은 server 에서 in-memory 형태로 저장되어지는 데이터로, 특정 사용자의 인증정보나 랜덤 시드값과 같이 동일한 사용자에 대한 인식정보로 활용된다. 

### cookie 의 lifecycle은 어떤가?
> 서버에서 정해준 방식을 따른다. 만약 서버에서 별도의 max-age 을 정해주지 않는다면, session-cookie 로 client 는 간주하여 브라우저가 종료되면 일괄 삭제하게된다. 
> 서버에서 max-age를 정해주면 정해진 유효기간까지 쿠키가 브라우저가 종료되어도 유지된다. 

### cookie는 서버로 항상 전송되어지는지? 
> 아니다. cookie 에 설정된 속성 값에 따라 전송여부가 결정된다.

### 어떤 속성에 따라 전송되고 왜 그런 정책이 필요한지?
> 우선 cookie 가 기본적으로 인증과 관련돼서 많이 활용되어지기 때문에 제 3자의 공격으로부터 막기위한 목적,  
> Secure 는 Https 요청에 대해서만 쿠키를 서버로 전송하는 조건이다.  
> HttpOnly는 xss 를 막기위한 정책으로, JS 를 통해 쿠키를 조작할 수 없고, 오로지 통신에 대해서만 쿠키가 활용된다는 것이다.  
> Samesite는 가장 핵심적인 정책이라 생각되는데, 이는 csrf를 막는 가장 중요한 정책이다.  
> Samesite는 속성 값에 따라 같은 site 인지 확인하고 쿠키 전송을 결정하게된다. none 값은 site와 관계없이 쿠키를 전송하겠다는 의미이고, 
> LAX 값은 document.location 혹은 href 같은 조건에 대해서만 서버로 쿠키를 전송하는 방식이다. strict는 같은 site가 아니라면 일체 쿠키를 전송하지 않겠다는 의미이다.
> 이런 정책들은 공격자가 쿠키를 활용한 xss, csrf 를 막기 위함이다.

### xss 와 csrf는 무엇인가?
> xss 는 cross site script 로 공격자가 상대방 브라우저에 악의적인 스크립트가 수행되도록하는 공격이고,  
> csrf는 사이트간 요청 위조로, 사용자의 의지와 관계없이 다른 사이트에서 서버로 요청을 보내는 공격을 의미한다.  

### Samesite는 어떻게 하면 좋은가?
> 우선 현재 크롬에서 default 로 LAX로 설정해두었고, 앞으로는 strict로 정책을 수립하겠다고 이야기한다. 따라서 정말 필요한 경우가 아니라면 처음부터 strict에 맞춰서 
> samesite 설정을 하면 좋다.

### 그러면 서버와 통신에 있어 쿠키 전송은 어떻게하나?
> site의 기준이 서브 도메인에 대해서는 무효하기 때문에, 서브도메인을 적극활용하면 좋다.  

## CORS 는 무슨 에러인가?
> origin 이 다른 서버에 대한 상호작용 여부를 감지하고 차단하는 정책으로, 브라우저에서 수행된다.  

### origin 의 기준이 무엇인가?
> schme + host + port, 따라서 http, https 간 혹은 서브도메인간에도 서로 다른 origin 으로 인식한다. 
> (단 서브도메인에서 상위도메인으로는 같은 origin이 인정된다. 예를 들어 https://comic.naver.com 에서는 https://naver.com 가 같은 origin)

### CORS 는 어떤 원리로 작동되는가? (혹은 서버간 통신에 있어서도 cors 가 적용되나?)
> 우선 서버간 요청 자체를 막는 정책은 아니다. 즉, 실제로 요청처리는 이루어진다. 다만 브라우저에서 CORS 조건을 확인하여 옳지않다면 응답정보를 
> 사용자가 접근하지 못하도록 한다.  
> 작동원리로는 주로 preflight 요청이 client 에서 server로 우선일어나고 후에 받은 정보를 바탕으로 client에서 실제 요청이 이루어지게 된다.

### preflight?
> 임시 요청을 의미한다. client 가 직접 server로 요청하기 전에 OPTIONS 메서드 바탕으로 서버에 요청을 보내 서버에서 현 요청에 대해 어떤 
> 메서드를 허용하는지, 어떤 origin 을 허용하는지, credential은 받는지 등 여부를 확인하고 이에 맞춰서 서버로 요청을 보내는 작업을 의미한다.

### 실제 프로젝트 하면서 CORS 에러는 어떻게 해결하나?
> 만약 CORS에 대해 자세히 모르고 인터넷에서 하라는대로 따라했더라면, 근본적인 cors 에러 해결은 어려울것이다. 우선 spring 을 전재로 설명하면 WebMvcConfigure를 구현한 설정파일을 구성하여 
> Cors 와 관련된 설정을 해주는데, 이때 쿠키를 활용한 통신을 한다면 credential을 사용하고 origin을 wild card로 설정하면안된다. 등등 여러 조건에 맞춰 cors 설정을 해준다. 
> 뿐만아니라, security를 사용하고 있다면 Options 요청에 대해 권한 여부 확인을 체크 해줘야한다. (preflight 요청과 관련함)

## CORS 정책만으로 csrf를 막을 수 있나?
> 그렇지 못하다. 특히 samesite 와 적절히 활용하지 못한다면 csrf 공격에 노출된다. CORS는 브라우저에서 처리되는 정책이지, 요청간 발생되는 에러가 아니다. 
> 따라서 만약 samesite가 none인 상태에서 제 3자가 쿠키를 바탕으로 서버에 요청을 보낸다면 실제 요청을 받아들여질것이다. 그러나 응답에 대해서는 브라우저에서 
> 처리되지 않을것이다. 예를 들어 제 3자가 쿠키를 바탕으로 비밀번호 변경요청을 했다면, 서버에서는 변경요청이 수행될것이고, 그 응답은 무의미하므로 이는 csrf를 방어하지 못하게된다. 
> 이를 막기 위해서는 samesite를 높은 수준으로 유지하고, CORS는 wildcard를 활용하지 않고 특정 path, origin, method 를 설정하여 공격을 막는다.  

