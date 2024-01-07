---
tags:
  - Junit5
  - PayPos
---
```java
@SpringBootTest    
@ExtendWith({RestDocumentationExtension.class, SpringExtension.class})  
@AutoConfigureMockMvc  
@TestMethodOrder(MethodOrderer.OrderAnnotation.class)  
class MemberApiTest {  
  
    @Autowired  
    private MockMvc mvc;  
  
    private final ObjectMapper objectMapper = new ObjectMapper();  
  
    @BeforeEach  
    public void setup_RestDoc(WebApplicationContext context,  
            RestDocumentationContextProvider provider) {  
        this.mvc = MockMvcBuilders.webAppContextSetup(context)  
                .apply(MockMvcRestDocumentation.documentationConfiguration(provider)).build();  
    }
}
```
# 분석

- `@SpringBootTest`
	- 테스트에 필요한 거의 모든 의존성을 제공하기 때문에 테스트 시간이 오래 걸릴 수 있다는 의견이 있다.
	- 사용 시 `@ExtendWith(SpringExtension.class)`를 이미 포함하고 있기 때문에 `@ExtensionWith`를 사용하지 않는다.
	- 또한 `@Autowired` 어노테이션이 허용하여 객체 의존성 주입이 가능하도록 한다.
- `@ExtendWith`
	- JUnit5 에서 사용되며, 테스트 확장을 지정한다.
	- Rest API 문서 생성
	- Spring의 테스트 지원
- `@BeforeEach`
	- 각 테스트 메서드가 실행되기 전에 한 번씩 실행되는 메서드를 지정한다.
	- REST API 문서를 생성하기 위해 필요한 MockMvc 인스턴스를 설정함.
- `WebApplicationContext`
	- Spring에서 제공하는 웹 애플리케이션의 루트 컨텍스트이다. 웹 애플리케이션의 설정과 빈들을 관리하는데 사용된다.
	- 루트 컨텍스트: 전체 애플리케이션에 관한 설정과 빈들을 포함하고 있다.
- `RestDocumentationContextProvider`
	- REST 문서 생성하기 위한 컨텍스트를 제공한다.
- `MockMvcBuilders.webAppContextSetup(context)`
	- 주어진 `WebApplicationContext`를 사용하여 Spring MVC의 테스트 환경을 설정한다.
	- MockMvc는 가상의 HTTP 요청을 보내고 응답을 검증하는 데 사용된다.
- `.apply(MockMvcRestDocumentation.documentationConfiguration(provider))`
	- `MockMvcRestDocumentation`의 문서화 구성을 적용한다. 여기서는 `RestDocumentationContextProvider`를 제공하여 문서 생성에 필요한 설정을 수행한다.
	- 주로 API의 동작을 테스트하는 동시에 API 문서를 생성하기 위한 설정을 수행한다.

## Reference

[[MockMvc 기반 Spring REST Docs 작성]]