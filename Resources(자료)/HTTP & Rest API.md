## HTTP(HyperText Transfer Protocol)란 무엇인가?
HTTP는 클라이언트와 서버 간에 데이터를 주고받는 데 사용되는 프로토콜입니다
보통 클라이언트가 서버에 요청을 보내면 응답해주는 방식으로 동작합니다.

HTTP는 요청(Request)과 응답(Response) 로 구성되어 있습니다.

요층은 클라이언트가 서버에게 보내는 거고  서버는 그 요청에 해당하는 자원을 응답을 함.

HTTP 요청의 주요 구성 유소
1. 메서드
2.  URL
3. 헤더
4. 본문
HTTP 응답의 주요 구성 요소
1. **상태 코드(Status Code)**: 
2. **헤더(Header)**
3. **본문(Body)**

Http는 무상태성을 가지는데 서버는 클라이언트가 보낸 요청과 이 다음 요청에 대한 연관관계가 없다라고 생각하고 요청을 받고 처리를 합니다.

#### REST API란 무엇인가?
HTTP 요청을 할 때 어떤 URI에 어떤 method를 사용할지에 대한 규약
	
#### REST API의 각 메서드는 어떻게 사용하는가?

| HTTP 메서드   | 목적            | 설명                |
| ---------- | ------------- | ----------------- |
| **GET**    | 조회(Read)      | 리소스를 가져올 때 사용     |
| **POST**   | 생성(Create)    | 새로운 리소스를 생성할 때 사용 |
| **PUT**    | 수정(Update)    | 리소스를 완전히 교체할 때 사용 |
| **PATCH**  | 부분 수정(Update) | 리소스의 일부만 수정할 때 사용 |
| **DELETE** | 삭제(Delete)    | 리소스를 삭제할 때 사용     |
- REST API 설계 원칙 ([MS REST API 디자인 가이드](https://learn.microsoft.com/ko-kr/azure/architecture/best-practices/api-design))