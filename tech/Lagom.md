## Lagom
* Lightbend에서 만든 라곰 (Lagom)
  * MSA
  * CQRS
  * event sourcing
    * 필요한것: 스냅샷


* 추가 패턴
  * SAGA pattern: 마이크로서비스들끼리 이벤트를 주고받아 특정 마이크로서비스에서의 작업이 실패하면 이전까지 작업이 완료된 마이크로서비스들에게 보상 (complementary) 이벤트를 소싱함으로써 분산 환경에서 atomicity를 보장하는 패턴 (SAGA pattern에서의 핵심은 마이크로서비스들끼리 이벤트를 주고 받는다는 것이기 때문에, 이 패턴은 기본적으로 event sourcing 패턴 위에 적용되는 패턴)

### 출처
* https://www.slideshare.net/ssuser45ecc2/lagom-framework
  * https://blog.naver.com/bansuk78/221074256200
* https://june-coder.tistory.com/32
* https://velog.io/@dvmflstm/SAGA-pattern%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%B6%84%EC%82%B0-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0
