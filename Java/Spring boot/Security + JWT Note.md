#개발일지 #SpringBoot 

노트의 취지: 구현하는 도중에 모르는 개념을 알게 되거나, 처음 본 어노테이션에 대해 작성하면서 외우는 목적으로 합니다.
# 2023 11 23

## Reference
- ⭐⭐ [Github: Simple Project](https://github.com/ali-bouali/spring-boot-3-jwt-security/tree/main)

## Page

### `@EnableWebSecurity` : [참고](https://jjangadadcodingdiary.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0%EC%97%90%EC%84%9C-EnableWebSecurity-%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98%EC%9D%98-%ED%99%9C%EC%9A%A9-%EB%B0%A9%EB%B2%95%EA%B3%BC-%EA%B8%B0%EB%8A%A5)

`@Configuration`와 함께 사용한다

스프링 리큐리티의 설정을 정의한 구성 클래스에서 선언하며, <mark style="background: #FFF3A3A6;">자동으로 스프링 시큐리티 필터 체인을 생성하고 웹 보안을 활성화</mark>합니다.

`WebSecurityConfigurer` 빈을 생성하고 구성 클래스에서 `configure()` 메서드를 오버라이딩하여 웹 보안 설정을 구성할 수 있습니다. 그 외에도 로그인 페이지 구성 및 폼 기반 인증 등 다양한 보안 기능 추가할 수 있습니다.

```java title:SecurityConfig.java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http
			.authorizeRequests()
				.antMatchers("/public/**").permitAll()
				.anyRequest().authenticated()
			.and()
				.formLogin()
			.and()
				.logout();
	}
}
```

위의 코드에 대해 분석 해봅시다.
1. `authorizeRequests()` 메서드는 요청에 대한 접근 권한을 구성합니다.
2. `/public` 경로에 대한 접근은 인증 없이 허용합니다.
3. `anyRequest().authenticated()` 은 나머지 모든 요청에 대해서는 인증이 필요로 합니다.
4. `formLogin()` 메서드는 폼 기반 로그인을 활성화합니다. 즉, 사용자는 로그인 폼을 통해 인증됩니다.
5. `logout()` 메서드는 로그아웃 기능을 활성화합니다.

따라서 위의 코드를 종합하면 `/public/`로 시작하는 경로에 대한 모든 요청을 허용하고, 나머지 요청에 대해서 사용자가 폼 기반 로그인을 사용해 인증되어야 합니다.



### Simple Project를 따라 해보자

구현 순서 → `config/SecurityConfiguration.java`
ㅇ