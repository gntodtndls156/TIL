---
tags:
  - Validation
Completed: true
---
# Reference
- (1) https://mangkyu.tistory.com/174 → (2) https://mangkyu.tistory.com/206
- https://www.baeldung.com/java-validation (Chapter.5)
- https://medium.com/sjk5766/valid-vs-validated-%EC%A0%95%EB%A6%AC-5665043cd64b
---
# #1

@Validated와 @Valid는 서로 다른 목적으로 사용되는 어노테이션이므로, 
일반적으로 동시에 사용하는 것을 필요로 하지 않습니다.
# #2
```embed
title: "@Valid vs @Validated 정리"
image: "https://miro.medium.com/v2/resize:fit:490/0*PM-CYIZWKM1pUo_t.png"
description: "아래 결론부터 정리하고 내용을 보도록 하겠다."
url: "https://medium.com/sjk5766/valid-vs-validated-%EC%A0%95%EB%A6%AC-5665043cd64b"
```

## 그룹 유효성 & 검증 그룹

Bean Validation(JSR-303)에서 제공하는 기능 중 하나로, 특정 시점에 어떤 검증 규칙을 적용할지를 지정하는 데 사용됩니다.
### Bean Validation(JSR-303)

**자바 표준(JSR-303)**, 객체의 속성 값이 특정 조건을 충족하는지 여부를 검증하기 위한 규격입니다.
주로 자바 클래스(POJO: Plain Old Java Object)의 필드나 메서드 매개변수 등에 주석 기반으로 검증 규칙을 정의하고, 이를 이용하여 **데이터의 유효성을 검증하는 기능을 제공**합니다.
- 어노테이션을 이용한 검증 규칙 정의
	- `@NotNull`, `@Size`, `@Pattern`
- 검증 그룹
	- `@GroupSequence`, `@GroupSequenceProvider`, `groups`
- 내장 및 사용자 정의 검증 어노테이션
	- 사용자 정의 검증 어노테이션을 만들어 필요에 맞게 확장할 수 있습니다.
- 유효성 검증 실행 및 오류 보고
	- Bean Validation은 주로 Java EE 환경에서 사용되며, 자동으로 실행되어 객체의 유효성을 검증합니다.
	- 오류가 발생한 경우에는 `ConstraintViolation`을 통해 오류 상세 정보를 얻을 수 있습니다.
### 예시

```java
public class User {
    
    @NotNull(groups = Create.class)
    private String username;

    @NotBlank(groups = {Create.class, Update.class})
    private String password;

    // 다른 필드들...

    public interface Create {}  // 검증 그룹
    public interface Update {}  // 검증 그룹
}
```

이 경우, 
`@NotNull` 어노테이션은 `Create` 그룹에만 적용되고,
`@NotBlank` 어노테이션은 `Create` 그룹과 `Update` 그룹에 적용됩니다. 
이렇게 지정된 그룹은 검증을 수행할 때 해당 그룹에 속하는 규칙만 적용됩니다.

검증 그룹을 사용하면 특정 시나리오에 필요한 검증 규칙을 정의하고, 필요에 따라 여러 그룹을 조합하여 유연하게 검증할 수 있습니다. 
#### 방법 1 + 예시

```java title:"Spring MVC에서 자동 활성화"
@PostMapping("/users")
public ResponseEntity<String> createUser(@Valid @RequestBody User user, BindingResult result) {
    if (result.hasErrors()) {
        // 유효성 검증 실패 시 처리 로직
        return ResponseEntity.badRequest().body("Validation failed");
    }
    // 유효성 검증 성공 시 처리 로직
    return ResponseEntity.ok("User created successfully");
}
```

Spring MVC에서는 `@Valid` 어노테이션을 사용하여 컨트롤러 메서드에 전달된 객체를 검증할 수 있습니다. 검증이 실패하면 `BindingResult` 객체에 오류가 저장되어 이를 활용할 수 있습니다.
#### 방법 2

```java title:"프로그래밍적인 검증"
Validator validator = Validation.buildDefaultValidatorFactory().getValidator();
Set<ConstraintViolation<User>> violations = validator.validate(user, Create.class);

if (!violations.isEmpty()) {
    // 유효성 검증 실패 시 처리 로직
}
```

검증 그룹이나 특정 시점에서 프로그래밍적으로 검증을 수행하려면 `Validator` 인터페이스를 사용할 수 있습니다. 이 인터페이스 구현체 중 하나인 `LocalValidatorFactoryBean`은 Spring에서 자주 사용됩니다.

이 방법을 사용하는 이유는
1. 복잡한 비즈니스 규칙이 있다.
2. 다양한 검증이 필요하다.
3. 다른 검증 로직과의 통합이 필요하다.
#### 권장하는 방법

프로젝트의 요구에 따라 다르게 구성이 될 수 있으며, 간단한 유효성 검증이면서 특별한 비즈니스 로직이 없는 경우, `@Valid` 을 사용하는 첫 번째 방법이 더 간단하고 편리할 것입니다.
