## cross join 이 발생?

* cross join 이란?
  * A 테이블과 B 테이블 조인 시 각 row 별로 경우의 수가 모두 나오는 것
  * 예 : A 테이블 {가,나} 데이터와 B 테이블 {a,b,c}의 경우 가a,가b,가c,나a,나b,나c 데이터 생성

* 발생하는 경우
  * Hibernate에서 query dsl 에서 join 을 하지 않고 entity에 있는 join 된 entity 값으로 where 조건에서 사용할 경우
  * Hibernate의 경우 암묵적인 조인은 Cross Join을 사용하는 경향

* 해결책
  * 명시적으로 join 을 사용한다.

* 참고
  * https://jojoldu.tistory.com/533
  * http://egloos.zum.com/sweeper/v/3002332
