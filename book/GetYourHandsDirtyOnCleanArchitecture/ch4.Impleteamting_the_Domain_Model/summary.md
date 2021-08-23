# ch4. Implementng a domain model

## implementing domain model

* ref: https://github.com/thombergs/buckpal/blob/master/src/main/java/io/reflectoring/buckpal/account/domain/Account.java
** Account : 실제 계좌의 현재 스냅샷
** Activity : 모든 인출, 입금 데이터 저장
** ActivityWindow : Account 는 최근 몇일의 window만 가짐 (모든걸 미리 다 로딩 안함)
** baselineBalance : ActivityWindow안에 첫 Activity 이전 잔액

## use case
- takes input
- validates business rules
- manipulates the model state
- returns output

- https://github.com/thombergs/buckpal/blob/master/src/main/java/io/reflectoring/buckpal/account/application/service/SendMoneyService.java
  - validate business rules > manipulate model state > return output

### input
- https://github.com/thombergs/buckpal/blob/master/src/main/java/io/reflectoring/buckpal/account/application/port/in/SendMoneyCommand.java
  - 추가로 money > 0
  - 왜 validation check가 여기에 있지? caller로 부터 잘못된 input을 알려줌
  - power Of constructor: 잘못된 객체 생성 못함 (immutable), param 많으면 builder 패턴 사용
  - usercase 별로 다른 input model 을 사용하자 (예: register account + update account details)


### validate business rules
- state 를 가지면 business validate , 안가지면 input validate
  - Account 모델에 mayWithdraw method
  - SendMoneyService 에 account exist (+ 초과인출 체크)

## etc
- rich vs anemic domain model
  - rich: business rules are located in entities
  - anemic : only have field, state
- usecase 별로 다른 output model ? yes
  - 최소한으로만 return (실제 method 혹은 api 의 역할에 대한 return)
  - model share하면 갖가지 이유로 field 늘어남, use case별로 decoupling 하자

- read only use case
  - https://github.com/thombergs/buckpal/blob/master/src/main/java/io/reflectoring/buckpal/account/application/service/GetAccountBalanceService.java
  - 분리한다. 이를 통해 CQRS 가능.

## 정리
- 모델은 각 use case 별로 in/out 독립
  - side effect 제거
  - 유지보수 쉽게
  - 여러 개발자가 다른 use case 동시에 일 가능
- input validation, business validation (use case in/out)을 강력히 하기

## reference

code: https://github.com/thombergs/buckpal/tree/master/src/main/java/io/reflectoring/buckpal/account
validation: https://beanvalidation.org/
