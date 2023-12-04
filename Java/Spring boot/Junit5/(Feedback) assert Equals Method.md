#개발일지 #SpringBoot #Java

## Before

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

위의 코드는 `MvcResult.class`를 통해 API 주소 호출 결과 값을 가져올 수 있습니다. 따라서 그 후의 코드는 올바르게 작성되었고 이상한 점이 없었지만, 보완하면 좋은 점이 있습니다.

바로 <mark style="background: #FFB86CA6;">Assert 메시지를 추가하는 것</mark>입니다.

## After

![[Pasted image 20231120172516.png]]

`assertEquals` 함수를 찾았습니다. 첫 번째는 예상 값을, 두 번째는 실제 값을, 세 번째는 다르면 메세지를 띄우는 파라미터를 가지고 있습니다.
```java
assertEquals(1, members.size(), "테스트 데이터의 개수가 예상과 다릅니다.");
```

세 번째 매개변수에 메세지를 넣음으로써 디버깅 시, 왜 실패 했는지 파악할 수 있도록 합니다.
실제로 에러 메세지를 띄우는지 궁금해서 1를 2로 바꿔 실행해봤습니다. 그 결과는 아래 사진과 같습니다.

![[Pasted image 20231120172739.png]]

더 나아가, 테스트 메시지를 어떻게 작성하면 좋은지 생각했습니다. 테스트 케이스마다 로그를 볼 수 있으며 굳이 어떤 테스트 케이스가 실행되었고 어떤 함수에서 에러가 났다는 메시지를 쓸 필요가 없을 것 같습니다. 그래서 이보다 더 깊은 에러 즉 세심하게 변수 하나를 체크하는 방법으로 예상했던 값과 다른 값이 발생했을 때를 위한 메시지를 써야 할 것 같습니다.

예를 들어, 개발자가 의도한 값을 출력하지 못했다면, “데이터베이스 확인 필요: Column Name에서 ‘이름’ 이어야 된다”는 식으로 작성하여 쉽게 파악할 수 있도록 함이 올바를 것입니다.