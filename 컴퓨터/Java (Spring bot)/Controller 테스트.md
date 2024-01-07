---
tags:
  - Junit5
Completed: false
---


```java
@Test  
@DisplayName("Member Length is 0")  
@Order(1)  
void SelectCase_1() throws Exception {  
    MvcResult result = mvc.perform(get("/api/member/")).andExpect(status().isOk()).andReturn();  
  
    List<Member> members = objectMapper.readValue(result.getResponse().getContentAsString(), new TypeReference<>() {  
    });  
    assertEquals(0, members.size(), "테스트 데이터의 개수가 예상과 다릅니다.");  
}  
  
@Test  
@DisplayName("Member Create")  
@Order(2)  
void CreateCase_1() throws Exception {  
    MemberCreate memberCreate = new MemberCreate();  
    memberCreate.setEmail("test@gmail.com");  
    memberCreate.setPassword("1234");  
    memberCreate.setLastName("Sung");  
    memberCreate.setFirstName("won");  
  
    String value = objectMapper.writeValueAsString(memberCreate);  
    mvc.perform(post("/api/member/").contentType(MediaType.APPLICATION_JSON).content(value)).andExpect(status().isOk());  
}  
  
@Test  
@DisplayName("Member Length is 1")  
@Order(3)  
void SelectCase_2() throws Exception {  
    MvcResult result = mvc.perform(get("/api/member/")).andExpect(status().isOk()).andReturn();  
  
    List<Member> members = objectMapper.readValue(result.getResponse().getContentAsString(), new TypeReference<>() {  
    });  
    assertEquals(1, members.size(), "테스트 데이터의 개수가 예상과 다릅니다.");  
}
```

