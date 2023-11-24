#개발일지 #SpringBoot #Java

내가 작성한 코드는 이러하다.
```java
MvcResult result = mvc.perform(get("/api/member/"))
	.andExpect(status().isOk())
	.andReturn();
	
List<Member> members = objectMapper.readValue(
	result.getResponse().getContentAsString(), 
	new TypeReference<>() {}
); 

assertEquals(1, members.size());
```

이러한 코드는 올바르게 작성되었고 이상한 점이 없었지만 보완하면 좋은 점이 있다.
바로 <mark style="background: #FFB86CA6;">Assert 메시지를 추가하는 것</mark>이다.

![[Pasted image 20231120172516.png]]

예를들어 예제 코드는 아래와 같다.
```java
assertEquals(1, members.size(), "테스트 데이터의 개수가 예상과 다릅니다.");
```

세번째 매개변수에 메세지를 넣음으로써 디버깅 시, 왜 실패했는지를 파악할 수 있도록 도운다.
실제로 에러 메세지를 띄우는지 궁금해서 1를 2로 바꿔 실행해봤다. 그 결과는 아래와 같다.

![[Pasted image 20231120172739.png]]

메시지를 어떻게 작성하면 좋을지 생각했다. 어차피 테스트 케이스마다 로그를 볼 수 있으므로 굳이 어떤 테스트 케이스가 실행되었고 어떤 함수에서 에러가 났다는 것을 쓸 필요가 없다.
그렇다면, 이보다 더 깊은 에러 즉 겉이 아니라 속에서 예상했던 것과 다른 값이 발생했을 때를 위한 이름을 써야 한다.

예를 들어 개발자가 의도한 값을 출력하지 못했다면 “데이터베이스 확인 필요: Column Name에서 ‘이름‘ 이어야 된다’“는 식으로 작성하면 좋을 것 같다.