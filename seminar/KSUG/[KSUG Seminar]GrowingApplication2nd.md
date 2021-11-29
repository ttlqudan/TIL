## [KSUG Seminar] Growing Application - 2nd. 애플리케이션 아키텍처와 객체지향
* 영상 참고: https://www.youtube.com/watch?v=26S4VFUWlJM&t=2139s

* 레이어 아키
  * presentation - domain - data source

* 절차지향
  * service에서 필요한 데이터 다 긁어와서 흐름대로 처리

* 객체지향 (domain model)
  * CRC
  * 객체끼리 메시지를 전달하며 각자 업무 수행, 객체간에 협력
  * 객체가 책임을 갖고 적절한 객체한테 위임한다.
  * 객체는 상태를 갖는다. 객체 본인이 본인 상태를 바꾼다.

* 절차나 객체나 결과는 같다.

* application logic
  * 도메인 레이어 캡슐화
  * 메일, DB, queue
  * 이건 순수한 객체내 안넣음 -> 그래서 서비스 레이어가 필요함
  * presentation - serfice - domain - infrastructure


* 객체-관계 임피던스 불일치
  * 객체: 유연성, 수정 용이성
  * 데이터: 중복제거
  * -> 데이터 매퍼 (ORM)를 둔다. DB와 객체의 결합도를 끊는다.

* 데이터 레이어
  * 트렌젝션 스크립트: 테이블랑 객체 일치


* 변하지 않는것은 모든 것이 변한다는 사실이다.
* OCP (open-closed principal)
  * 추상화를 기준으로 분리하면 변화에 따라 변화한곳만 수정
* DI (dependency injection)
  * 추상화 레벨에서만 디펜던시를 가짐

* if- else 를 도메인 모델로.. 복잡성을 알고리즘에서 분리하고 객체의 관계로 만들기
* 최대한 단순하고 깔끔하게 짜야댐 -> 요구사항은 언제 어떻게 바뀔지 모름
  * 변경하기 쉬운 코드로 리펙토링을 계속 하기


* 클래스를 늘리고 작게 만들다 보면 추상화 수준이 보인다.
