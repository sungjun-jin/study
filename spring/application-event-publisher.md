# ApplicationEventPublisher

ApplicationEventPublisher란 스프링에서 이벤트를 발행(publish)하고 구독(subscribe)하는 매커니즘을 제공하는 인터페이스다.

## 이벤트 발행 예시

### Spring 4.2 이전

#### 이벤트 객체 생성하기
```java
public class FundingEvent extends ApplicationEvent {
    // 후원자
    private String backer;
    // 기부 금액
    private int amount;

    public FundingEvent(Object source, String backer, int amount) {
        super(source);
        this.backer = backer;
        this.amount = amount;
    }

    public String getBacker() {
        return backer;
    }

    public int getAmount() {
        return amount;
    }
}
```

이벤트 객체를 생성하기 위해서는 ApplicationEvent 추상 클래스를 상속받아 구현해야 한다. ApplicationEvent의 생성자는 `Object source` 매개변수를 받는다. 이 매개변수를 이벤트를 발행한 객체를 의미한다.

#### 이벤트 발행하기
```java
@Component
public class AppRunner implements ApplicationRunner {
    private final ApplicationEventPublisher publisher;

    public AppRunner(ApplicationEventPublisher publisher) {
        this.publisher = publisher;
    }

    @Override
    public void run(ApplicationArguments args) {
        // 이벤트 발행하기
        FundingEvent fundingEvent = new FundingEvent(this, "기부자", 1_000_00);
        publisher.publishEvent(fundingEvent);
    }
}
```

작성한 이벤트는 ApplicationContext이 발행한다. 


#### 이벤트 처리하기
```java
@Component
public class CrowdFunding implements ApplicationListener<FundingEvent> {

    @Override
    public void onApplicationEvent(FundingEvent event) {
        System.out.println("후원자 :" + event.getBacker() + "기부 전달 금액 : " + event.getAmount());
    }
}

```
이벤트를 처리하는 클래스를 빈으로 등록한다. ApplicationListener 인터페이스의 타입 매개변수에 처리하고자 하는 이벤트 클래스를 적용한다. ApplicationListener 인터페이스의 onApplicationEvent 메소드를 구현하여 이벤트를 처리한다.


### Spring 4.2 이후
```java
public class FundingEvent {
    // 후원자
    private String backer;
    // 기부 금액
    private int amount;

    public FundingEvent(String backer, int amount) {
        this.backer = backer;
        this.amount = amount;
    }

    public String getBacker() {
        return backer;
    }

    public int getAmount() {
        return amount;
    }
}
```
스프링 4.2 이후로는 이벤트 클래스를 순수 자바 클래스(POJO)로 작성이 가능해졌다. 


#### 이벤트 발행하기
```java
@Component
public class AppRunner implements ApplicationRunner {
    private final ApplicationEventPublisher publisher;

    public AppRunner(ApplicationEventPublisher publisher) {
        this.publisher = publisher;
    }

    @Override
    public void run(ApplicationArguments args) {
        // 이벤트 발행하기
        FundingEvent fundingEvent = new FundingEvent("기부자", 1_000_00);
        publisher.publishEvent(fundingEvent);
    }
}
```

작성한 이벤트는 ApplicationContext가 동일하게 발행한다. Spring 4.2 이전과 다른점은 이벤트 객체를 생성할때 `Object source` 매개변수가 필요없어졌다.


#### 이벤트 처리하기
```java
@Component
public class CrowdFunding {
    @EventListener
    public void handleFundEvent(FundingEvent event) {
        System.out.println("후원자 :" + event.getBacker() + "기부 전달 금액 : " + event.getAmount());
    }
}
```
이벤트를 처리하는 클래스를 빈으로 등록한다. 이벤트 처리를 담당하는 메소드에 @EventListener 어노테이션을 적용하고 매개변수로 처리하고자 하는 이벤트 객체를 가져와 이벤트를 처리한다.

## 이벤트 처리 순서 지정
```java
@Component
public class CrowdFunding {
    @Order(1)
    @EventListener
    public void handleFundEventNotification(FundingEvent event) {
        System.out.println("후원자 :" + event.getBacker() + "기부 전달 금액 : " + event.getAmount());
    }

    @Order(2)
    @EventListener
    public void handleFundEventMoneySend(FundingEvent event) {
        System.out.println("기부 전달 금액 : " + event.getAmount() + " 송금");
    }
}
```

이벤트를 핸들링하는 메소드가 2개 이상이라면 @Order 어노테이션을 사용해 핸들링 메소드의 순서를 지정할 수 있다. 어노테이션에 매개변수 값이 작을수록 높은 우선순위를 가지고 있다.

실행 결과
```
후원자 : 기부자 기부 전달 금액 : 100000
기부 전달 금액 : 100000 송금
```

## 비동기로 이벤트 처리

```java
@Component
public class CrowdFunding {
    @Async
    @EventListener
    public void handleFundEventNotification(FundingEvent event) {
        System.out.println(Thread.currentThread());
        System.out.println("후원자 : " + event.getBacker() + " 기부 전달 금액 : " + event.getAmount());
    }

    @Async
    @EventListener
    public void handleFundEventMoneySend(FundingEvent event) {
        System.out.println(Thread.currentThread());
        System.out.println("기부 전달 금액 : " + event.getAmount() + " 송금");
    }
}
```
기본적으로 이벤트 핸들러 메소드는 한개의 쓰레드로 처리된다. 비동기적으로 이벤트를 처리하고 싶으면 핸들러 메소드에 @Async 어노테이션을 적용한다.

```java
@SpringBootApplication
@EnableAsync
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```
스프링 어플리케이션이나 쓰레드풀 설정에 @EnableAsync를 적용한다. 위 예시는 스프링 어플리케이션에 @EnableAsync를 적용한 예시다.

실행 결과
```
Thread[task-1,5,main]
Thread[task-2,5,main]
기부 전달 금액 : 100000 송금
후원자 : 기부자 기부 전달 금액 : 100000
```

