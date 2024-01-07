---
tags:
  - Security
  - JWT
Completed: false
---
# JWT (Json Web Token)

서버에서 Session을 생성하는 대신 데이터를 JSON Web Token로 인코딩하고 클라이언트에게 전송합니다. 그 클라이언트는 JSON Web Token를 저장하고 서버에 요청할 때 해당 Header에 JSON Web Token이 담겨져 있어야 합니다. 그래야 서버가 JSON Web Token를 확인하고 응답해줍니다.
![[Pasted image 20240104193413.png]]
세션 기반 인증 *Session-based Authentication*은 일반적으로 쿠키를 사용하여 세션 데이터를 저장하는데, 이와 비교하여 JWT를 사용하면 토큰을 클라이언트 측 저장소에 저장할 수 있습니다. 
- 브라우저에서는 로컬 스토리지
- iOS에서는 Keychain
- Android에서는 SharedPreferences
네이티브 앱 사용자를 위한 별도의 **백엔드 프로젝트나 추가적인 인증 모듈을 구축하지 않고도 효과적으로 토큰 기반의 인증을 구현할 수 있는 장점**이 있습니다. 토큰은 클라이언트 측에서 관리되므로 서버 측에서는 토큰의 유효성만 검증하면 되며, 이는 **확장성과 유지보수 측면에서 효율적**입니다.

JWT에서 중요한 부분은 Header, Payload, Signature이며, 표준 구조인 `header.payload.signature`로 조합합니다. 

클라이언트가 JWT를 Authorization 헤더에 Bearer 접두어와 함께 첨부하며, HTTP 요청 헤더에 인증 토큰을 전달하는 표준적인 방법 중 하나입니다. 이를 통해 서버는 클라이언트를 인증하고 권한을 부여할 수 있습니다.
```text
Authorization: Bearer [header].[payload].[signature]
```
# Spring boot, React Authentication example

> Demo
```embed
title: "Spring Boot + React: JWT Authentication & Authorization with Spring Security example"
image: "https://img.youtube.com/vi/CsgtYvlR7xk/maxresdefault.jpg"
description: "This is Demo for fullstack Spring Boot & React: JWT Authentication & Authorization with Spring Security, React Router, React Validation Form.The back-end ser…"
url: "https://www.youtube.com/watch?v=CsgtYvlR7xk&t=473s"
```

## React screens
- Anyone can access a public page before logging in: 
![[Pasted image 20240104232820.png]]
- A new User can signup:
![[Pasted image 20240105180303.png]]
- Form signup validation:

- After signup is successful, User can login:

- After login, App directs the User to Profile page:

- UI for Moderator login (the navigation bar will change by authorities):

- If a User who doesn’t have Admin role tries to access Admin/Moderator Board page:




