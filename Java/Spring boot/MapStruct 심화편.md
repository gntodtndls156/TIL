
## Background 

Mapstruct는 Java 객체 간의 Mapping을 자동화하는 라이브러리입니다. DTO와 Entity class 등과 같이 비슷한 구조를 가진 객체 간의 Mapping을 단순화하고 효율적으로 처리할 수 있도록 지원합니다.
이러한 라이브러리를 이용해 다양한 방법을 탐구하고 기록하고자 합니다.

[Using Multiple Source Objects with MapStruct | Baeldung](https://www.baeldung.com/mapstruct-multiple-source-objects)

MapStruct 라이브러리를 통해 자동으로 변환해주는 데 어떻게 변환하는지 궁금하여 확인했습니다.

```java title: MapStructImpl
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

## Multiple Parameter