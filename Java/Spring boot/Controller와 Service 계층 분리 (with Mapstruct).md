#개발일지 #SpringBoot 

## Background 

Mapstruct는 Java 객체 간의 Mapping을 자동화하는 라이브러리입니다. DTO와 Entity class 등과 같이 비슷한 구조를 가진 객체 간의 Mapping을 단순화하고 효율적으로 처리할 수 있도록 지원합니다.
이러한 라이브러리를 이용해 다양한 방법을 탐구하고 기록하고자 합니다.

## Multiple Parameter
### 과정

Controller의 Update 메소드에서 이런 코드가 있었습니다.

```java
return memberMapper.MEMBER_GET(
	memberService.updateMember(
		memberMapper.MEMBER_UPDATE(replace, email)
	)
);
```

Controller 계층에서 DTO 받고 Mapper를 통해 Entity 변환 후 Service 계층으로 넘겨줘야 한다고 배웠습니다. 왜냐하면 이러한 방식은 일반적이고, Controller 계층과 Service 계층의 분리가 되기 때문입니다. 하지만 뭔가 잘 못 된 것 같습니다. 가독성이 떨어지고 이해하기 어려운 코드입니다.

어떻게 하면 코드를 줄이면서 동시에 보기 좋게 만들 수 있을까? 고민하던 찰나, Controller 계층에서 DTO을 그대로 Service 계층으로 넘겨준다면? 하는 생각으로 코드를 다시 작성했습니다.

```java title:Controller
return memberMapper.MEMBER_GET(memberService.updateMember(email, request));
```

```java title:Mapper
@Mapping(target = "email", source = "source.email")  
@Mapping(target = "password", source = "source.password")  
@Mapping(target = "lastName", source = "source.lastName")  
@Mapping(target = "firstName", source = "source.firstName")  
@Mapping(target = "id", ignore = true)  
@Mapping(target = "email_authentication", ignore = true)  
@Mapping(target = "shops", ignore = true)  
Member MEMBER_UPDATE(@MappingTarget Member target, MemberReplace source);
```

```java title:Service
public Member updateMember(String email, MemberReplace source) {  
        if (memberRepository.existsByEmail(email)) {  
            Member target = memberRepository.findByEmail(email);  
            return memberMapper.MEMBER_UPDATE(target, source);  
        }  
        else {  
            System.out.println("Not found the Member");  
//            throw new MemberNotFoundException();  
            return null;  
        }  
    }
```

이렇게 한다면 보기 좋지만 Controller와 Service 계층의 독립성을 깨뜨리지 않을까? 생각이 들었습니다. 분석해보니, Mapper는 Controller와 Service 계층에서 동시에 수행하므로, 이 경우 Mapper가 Service에 의존하게 되는 구조가 되어버립니다. 따라서 두 계층 간의 결합도가 높아집니다.

다시 짚어봅시다.
Service 계층은 비즈니스 로직을 담당한다.
Controller 계층은 HTTP 요청을 처리하고 응답을 생성한다.
따라서 서로 독립적으로 존재하고 서로에게 의존성을 갖지 않도록 설계하는 것이 옳습니다.

### 결과

```java title:controller
@PutMapping("/{email}")  
public MemberGet replaceMember(@PathVariable(name = "email") String email,  
        @RequestBody MemberReplace request) {  
    Member updated_member = memberService.updateMember(  
            memberMapper.MEMBER_UPDATE(memberService.selectMember(email), request));  
    return memberMapper.MEMBER_GET(updated_member);  
}
```

Controller 계층에서만 Mapper를 써야 한다는 규칙은 변함 없었고, 
Service 계층에서 Mapper 의존하지 않으려면 작은 단위의 비즈니스 코드를 구현하고 Controller 계층에서 최대한 써 먹어야 한다는 것을 깨달았습니다.

```java title:Service
public Member updateMember(Member member) {  
    return memberRepository.save(member);  
}

public Member selectMember(String email) {  
    return memberRepository.findByEmail(email);  
}
```

두 개의 함수를 만들었고, Controller 계층에서 활용했습니다.

가독성과 간결한 코드를 추구하기 위해 규칙을 깨뜨리면 당장에 영향이 없겠지만, 나중에 부메랑이 되어 돌아올 수 있기 때문에 사전에 프로그래밍 규칙을 잘 지켜야 한다는 점을 상기했습니다.