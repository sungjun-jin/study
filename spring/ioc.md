# Inversion Of Control
```java
public OwnerController {
    private OwnerRepository ownerRepository = new OwnerRepository(); // 직접 만들어 주입
}
```
의존성에 대한 제어권이 있다면 위와 같이 자기가 사용할 의존성을 직접 만들어서 관리할 수 있다.

```java
public OwnerController {
    private OwnerRepository ownerRepository;

    public OwnerController(OwnerRepository ownerRepository) {
        this.ownerRepository = ownerRepository;
    }
}

```
스프링에서 제공하는 위 방법은 내가 사용할 의존성을 외부에서 생성해 주입받는 기법이다. 사용할 의존성을 외부에서 직접 만들어 제공, 관리하기 때문에 제어권에 대한 역전이 일어났다고 할 수 있다.

스프링은 IoC 컨테이너를 사용해 필요한 의존성을 빈(Bean)으로 등록해 관리한다. 

## IoC Container

스프링 IoC Container는 빈 설정 소스로부터 빈 정의를 읽어들이고, 빈을 구성하고 제공한다.

스프링의 IoC Container (이하 스프링 컨테이너)는 BeanFactory와 ApplicationContext 인터페이스가 있다.

BeanFactory를 빈을 생성하고 의존성을 주입하는 가장 기본적인 스프링 IoC 컨테이너 인터페이스이다. 이를 확장한 ApplicationContext는 다국어, 프로필, 이벤트 발행 / 구독에 대한 처리가 추가된 인터페이스이다. 스프링 공식문서는 특별한 경우가 아니면 ApplicationContext를 활용하기를 권장하고 있다.

스프링 컨테이너가 담당하는 의존성 주입 관련한 주요 기능은 다음과 같다
- 빈을 생성하고 관리한다 
- 해당 빈의 의존성이 필요한 곳에 주입하여 제공한다

ApplicationContext에 등록된 빈들은 다음과 같이 가져올 수 있다.
```java
OwnerController bean = applicationContext.getBean(OwnerController.class);
System.out.println(bean);
```

## Bean
빈(bean)이란, 스프링 IoC 컨테이너가 관리하는 객체를 뜻한다. 

스프링 컨테이너는 ConcurrentHashMap 자료구조를 활용하여 빈을 관리한다.

스프링 컨테이너에 객체를 빈으로 등록했을때의 장점은 다음과 같다
- 필요한 의존성들을 (빈으로 등록된 객체에 한해서) 스프링 컨테이너가 알아서 주입해준다
- 빈의 스코프를 지정할 수 있다
    - Singleton : 어플리케이션에서 항상 한 개의 인스턴스를 사용한다 (기본값)
    - Request : 요청마다 인스턴스를 생성한다
    - Session : 세션마다 인스턴스를 생성한다
    - Prototype : 항상 새로운 인스턴스를 만든다

- 빈의 라이프사이클 콜백을 활용할 수 있다
    - postConstruct()
    - preDestroy()

스프링에서 빈을 등록할 수 있는 방법은 크게 두가지가 있다

### 컴포넌트 스캔 방식을 활용한 @Component 관련 어노테이션 사용 
@Repository, @Service, @Controller와 같은 어노테이션을 사용하는 방법이다. 해당 어노테이션을 타고 들어가보면 @Component 어노테이션이 적용되어있다. 

아래 예시로 @Controller 어노테이션을 살펴보면 @Component 어노테이션이 적용되어 있는것을 확인할 수 있다.
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Controller {
    @AliasFor(
        annotation = Component.class
    )
    String value() default "";
}
```

스프링은 빈의 라이프사이클 인터페이스를 활용해 @Component 어노테이션이 적용된 클래스를 찾고, 해당 클래스의 인스턴스를 생성하여 빈으로 등록하는 작업을 한다. 

@Component 어노테이션이 적용된 클래스를 찾는 과정을 **컴포넌트 스캔**이라고 한다. @SpringBootApplication이 적용된 어노테이션을 따라가 보면 @ComponentScan 어노테이션이 있다. 이 이노테이션에 등록된 패키지를 기준으로 해당 패키지와 하위 패키지에서 빈으로 등록할 클래스를 찾는다.

실제 스캐닝은 ConfigurationClassPostProcessor에 의해 처리가 된다.
```java
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    // 생략
}
```

### 자바 설정 파일 사용
@Confiugration 어노테이션을 활용해 직접 빈을 등록하는 방식이다. @Configuration 어노테이션을 적용한 클래스 내부에 @Bean 어노테이션을 적용한 메소드를 작성하면, 해당 메소드에서 반환하는 객체가 스프링 IoC 컨테이너에 빈으로 등록된다.

```java
@Configuration
public class SampleConfig {
    @Bean
    public SamleController sampleController {
        return new SampleController();
    }
}
```

### XML로 등록
스프링 초기에 사용하던 방법으로써 resources 패키지 하위에 xml 파일을 생성하여 직접 빈을 등록하는 방법이다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 빈 등록-->
    <bean id="koreanTire" class="com.sj.study.springmvc.di.KoreanTire"/>
    <bean id="americanTire" class="com.sj.study.springmvc.di.AmericanTire"/>
</beans>
```
