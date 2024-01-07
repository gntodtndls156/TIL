---
tags:
  - MapStruct
Completed: true
---
# 💡

Mapstruct 라이브러리를 통해 DTO와 Entity 등과 같은 비슷한 구조를 가진 객체 간의 Mapping 하고 있었습니다. DTO에서 Entity로 변환하기 위해 값을 교체하거나 없는 값을 대입하는 일이 있을 때 파라미터 여러 개를 둬서 작업하기도 했습니다.

```java title:example
Member MEMBER_UPDATE(@MappingTarget Member target, 
					String email,
					String password,
					String lastName,
					String firstName
					);
```

지금까지 이런 방식을 통해 Mapping 자동화 했습니다만, Controller 계층에서 Mapping 함수를 호출할 때, `MEMBER_UPDATE(member, email, password ...`이처럼 작성해야 한다는 점이 번거로웠습니다. 어떻게 하면 조금 더 세련된 코드를 작성할 수 있을지 고민했습니다.

### Reference
- [Using Multiple Source Objects with MapStruct | Baeldung](https://www.baeldung.com/mapstruct-multiple-source-objects)

# Step

`MemberReplace.class` DTO 만든 후 Controller 계층에서 Request Body `MemberReplace.class`로 받습니다. 그런 후 Mapper 코드를 수정했습니다.

```java title:Controller
@PutMapping("/{email}")  
public MemberGet replaceMember(@PathVariable(name = "email") String email,  
        @RequestBody MemberReplace request) {  
    Member updated_member = memberService.updateMember(  
            memberMapper.MEMBER_UPDATE(memberService.selectMember(email), request));  
    return memberMapper.MEMBER_GET(updated_member);  
}
```

```java title:MemberReplace
@Data  
public class MemberReplace {  
    private String email;  
    private String password;  
    private String lastName;  
    private String firstName;  
}
```

참고 사이트에서 Mapping 하는 방법을 제시되어 있습니다.

```java title:exampleMapper
@Mapping(source = "customer.firstName", target = "forename") 
@Mapping(source = "customer.lastName", target = "surname") 
@Mapping(source = "address.street", target = "street") 
@Mapping(source = "address.postalcode", target = "postalcode") 
@Mapping(source = "address.county", target = "county") 
DeliveryAddress from(Customer customer, Address address);
```

`DeliveryAddress.class` 는 `Customer.class`와 `Address.class` 파라미터를 받아 반환하므로, 이 코드는 제가 생각하는 이상적인 코드입니다. `@Mapping` 여러 어노테이션을 작성해야 한다는 점이 번거롭지만 한번 작성하고 끝날 일입니다. Controller 계층에서 긴 코드를 반복해서 작성할 필요가 없어지니 비교하면 합리적으로 생산 효율을 높일 수 있습니다.

이 예제를 참고하여 작성했습니다.

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
# 🎈

빌드 후 MapStruct 라이브러리를 통해 자동 생성된 Mapper 구현 클래스를 살펴봤습니다.

```java title:MapStructImpl
@Override  
public Member MEMBER_UPDATE(Member target, MemberReplace source) {  
    if ( source == null ) {  
        return target;  
    }  
  
    target.setEmail( source.getEmail() );  
    target.setPassword( source.getPassword() );  
    target.setLastName( source.getLastName() );  
    target.setFirstName( source.getFirstName() );  
  
    return target;  
}
```

Builder 패턴을 활용하지 않고 Setter 함수를 통해 생성 된 것으로 보아, Builder 패턴을 활용하기엔 다소 무리가 있었던 모양입니다. 결국 Entity class에서 지양해야 할 Setter 어노테이션을 추가할 수 밖에 없지만, **Service에서 Setter 이용하지 않도록 규칙**을 새로 세우면 될 것입니다.

하지만 궁금해졌습니다. 왜 Mapstruct는 Builder 패턴을 이용하지 않고 Setter 함수를 이용할까? 입니다. 이 부분은 MapStruct 관련 커뮤니티에 질문해볼 생각입니다.
또한, **Entity의 Setter 함수를 만들지 않아야 하는 이유는 DTO 존재 이유가 사라지게 하기 때문**입니다. DTO는 Entity 보호하기 위한 일종의 장치이고 이를 통해 분명한 역할을 전달하므로 필요합니다.

그리고, **앞서 언급한 반복 코드를 줄이고 코드 작성 효율을 늘리기 위한 노력**으로 공부하다 보니, MapStruct 라이브러리로 이런 것이 가능하구나 깨닫게 되었습니다.