---
tags:
  - Junit5
  - PayPos
---
```java
@Test  
@DisplayName("Initial member list is empty")  
@Order(1)  
void shouldReturnEmptyMemberList() throws Exception {  
    MvcResult result = mvc.perform(get("/api/member/")).andExpect(status().isOk()).andReturn();  
  
    List<Member> members = objectMapper.readValue(result.getResponse().getContentAsString(),  
            new TypeReference<>() {  
            });  
    assertEquals(0, members.size(), "Initial member list should be empty.");  
}
```

# 규칙

- DisplayName
	- 직관적인 이름을 사용하여야 합니다: `Initial member list is empty`, `Create a new member`
- `assertEquals`
	- 반드시 메시지를 작성해야 합니다: `Initial member list should be empty`
	- [[assertEquals 작성법]]
- REST API 문서 생성해야 합니다.
	- [[MockMvc 기반 Spring REST Docs 작성]]