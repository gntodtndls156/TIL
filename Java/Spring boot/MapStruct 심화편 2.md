
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

