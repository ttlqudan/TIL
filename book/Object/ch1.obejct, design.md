### ch1. 객체, 설계

책 예제: https://github.com/eternity-oop/object

* 이론과 실무 .. SW에서는 실무가 앞서고 실무가 더 중요

* 제대로 실행 + 변경에 용이 + 코드를 읽은 사람과 의사소통 (이해하기 쉽다) - 마틴 파울러

* 예제: 티켓 판매 application

* 기초: 극장에서 절차지향적으로 관람객의 가방에서 돈을 빼내는 설계
  * 내가 이렇게 짜고 있는 느낌이 퐉 들었다.
  * 한 번에 이해하긴 좋고, 잘 동작은 가지만.. 변경에 취약 + 각 객체에 위임이 하나도 없다.

```java
public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}

```

* 설계 개선 -> 자율성 개선
  * 캡슐화: 객체 내부의 세부적 사항을 감추기
    * 외부 객체는 다른 객체의 인터페이스에만 의존하도록
  * 응집도: 밀접하게 연관된 작업만 수행하고 연관없는 작업은 다른 객체에 위임
  * 책임의 이동: 각 객체는 자신을 스스로 책임진다.
  * 결합도: 불필요한 의존성을 제거하여 결합도 낮추기

* 절차지향: 프로세스와 데이터를 별도의 모듈에 위치
* 객체지향: 데이터와 프로세스가 동일한 모듈에 위치

* 의인화 (anthropomorphism): 능동적이고 자율적인 존재로 객체를 설계하는 원칙

* 설계란? 코드를 배치하는것
  * 코드 수정은 버그 발생을 높인다. 각 객체에서 각 데이터를 처리하면 변경을 최소화 가능
  * 객체 끼리는 메시지를 전송한다 -> akka 의 actor와 비슷하다는 느낌을 받음
