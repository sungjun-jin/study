# Spring Validator & Java Bean Validation

## Spring Validator
스프링에서 제공하는 Validator는 어플리케이션에서 사용하는 객체의 데이터를 검증하는 인터페이스이다.

### Validator 인터페이스 구현
```java
public class BookValidator implements Validator {
    @Override
    public boolean supports(Class<?> clazz) {
        return Book.class.equals(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
        // ValidationUtils 사용
        ValidationUtils.rejectIfEmptyOrWhitespace(errors,"title", "notEmpty", "empty title is not allowed");

        Book book = (Book) target;

        // 커스텀 validation 로직 구현
        if(book.getTitle().length() >= 1000) {
            errors.reject("titleLength", "title length is more than 1000");
        }
    }
}
```

Validator 인터페이스의 supports, validate 메소드를 구현해준다. 각 메소드의 역할은 다음과 같다.

- supports : 매개변수로 넘어온 클래스 인스턴스(clazz)가 검증 대상인지 판별한다. 주로 클래스 타입을 비교한다. 
- validate : 매개변수로 넘어온 인스턴스(target)에 대한 데이터를 검증한다. 만약 검증에 오류가 발생하면 매개변수로 넘어온 error 객체에 등록 된다.
 - ValidationUtils를 활용해 객체 내부의 필드를 검증할 수도 있다
 - validation 로직을 사용자가 직접 커스텀 할 수 있다. 

### Validator 인터페이스 사용 예시
```java
@Component
public class AppRunner implements ApplicationRunner {

    @Override
    public void run(ApplicationArguments args) {
        Book book = new Book();
        book.setTitle("1230401283798123");

        BookValidator validator = new BookValidator();
        Errors bindingResult = new BeanPropertyBindingResult(book, "book");

        if(validator.supports(book.getClass())) {
            validator.validate(book, bindingResult);
        }


        System.out.println(bindingResult.hasErrors());

        bindingResult.getAllErrors().forEach(System.out::println);
    }
}
```

구현한 Validator는 일반 객체로 사용할 수 있고 빈으로 등록해서 사용할 수 있다.

## 어노테이션을 활용한 Validation
`@Valid`, `@Validated` 어노테이션을 사용하면 객체의 데이터를 쉽게 검증할 수 있다. **`@Valid`는 Java에서 제공하는 기능이고 `@Validated`는 스프링에서 제공하는 기능이다.** 각각의 차이점이 있으니 우선 @Valid 어노테이션부터 알아보자.

### 의존성 추가
Spring Boot에서는 아래 의존성을 추가하여 사용할 수 있다.
```gradle
implementation 'org.springframework.boot:spring-boot-starter-validation'
```

### @Valid 사용해보기
Bean Validation은 JAVA EE 표준으로 자바에서 제공하는 validator 이다. @Valid 어노테이션을 기반으로 객체 내부의 데이터에 대한 검증을 실행한다.
Bean Validation은 JSR-303(Bean Validation 1.0), JSR-380(Bean Validation 2.0)의 자바 표준 스펙으로 현재 구성되어 있다. 

```java
public class SignUpRequest {
    @Email
    private final String email;

    @NotBlank
    private final String password;
}
```

검증하고자 하는 필드에 검증 어노테이션을 적용한다. 위 예시에서는 이메일 형식을 검증할 수 있는 `@Email`, null이나 공백 문자를 허용하지 않는 `@NotBlank`를 사용했다. 이 밖에 사용할 수 있는 어노테이션의 종류들은 https://www.baeldung.com/java-validation 링크를 참고하자.

```java
@PostMapping("/sign-up")
public ResponseEntity<Void> signUp(@RequestBody @Valid SignUpRequest request) {
}
```
해당 클래스를 사용하는 컨트롤러에 `@Valid` 어노테이션을 적용한다.

#### @Valid 동작 원리
DispatcherServlet이 클라이언트 요청을 받으면 요청을 처리할 핸들러를 매핑한다. 이 때 클라이언트의 요청값을 매핑된 핸들러의 매개변수로 바인딩하는 ArgumentResolver가 동작한다. 만약 `@Valid` 어노테이션이 있으면 validation을 진행한다. 여기서 validation이 실패한다면 **MethodArgumentNotValidException** 예외를 발생시킨다.

ArgumentResolver의 구현체는 대표적으로 컨트롤러의 매개변수가 `@RequestBody`인 경우 RequestResponseBodyMethodProcessor, `@ModelAttribute`인 경우 ModelAttributeMethodProcessor를 사용한다.

위와 같은 이유로 `@Valid` 어노테이션을 통한 요청값 검증은 **컨트롤러 레벨에서만** 가능하다. 

### @Validated 사용해보기
@Validated 어노테이션을 사용하면 컨트롤러 레벨뿐만 아니라 모든 영역에서 스프링 빈의 validation을 수행할 수 있다.

```java
@Validated
@Service
public class MemberFacadeService {
    public void signUp(@Valid SignUpRequest signUpRequest) {
        String encryptedPassword = passwordEncoder.encode(signUpRequest.getPassword());

        MemberRegisterDto memberRegisterDto = new MemberRegisterDto(signUpRequest.getEmail(), encryptedPassword, MemberType.MEMBER);
        memberService.saveMember(memberRegisterDto);
    }
}
```

위 예시처럼 클래스에 @Validated 어노테이션과, 메소드의 파라미터에 @Valid 어노테이션을 적용하면 validation을 수행할 수 있다.

#### @Validated 동작 원리
`@Validated` 어노테이션은 스프링에서 제공하는 기능이다. 해당 어노테이션을 **스프링 빈**에 적용하면 검증 기능이 추가된 AOP Proxy 클래스가 생성된다. 여기서 validation이 실패한다면 **ConstraintViolationException** 예외를 발생시킨다.


```
jakarta.validation.ConstraintViolationException: signUp.signUpRequest.email: 올바른 형식의 이메일 주소여야 합니다, signUp.signUpRequest.password: 공백일 수 없습니다
	at org.springframework.validation.beanvalidation.MethodValidationInterceptor.invoke(MethodValidationInterceptor.java:170) ~[spring-context-6.1.4.jar:6.1.4]
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:184) ~[spring-aop-6.1.4.jar:6.1.4]
	at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.proceed(CglibAopProxy.java:765) ~[spring-aop-6.1.4.jar:6.1.4]
	at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:717) ~[spring-aop-6.1.4.jar:6.1.4]
	at me.study.sj.paymoneydemo.facade.MemberFacadeService$$SpringCGLIB$$0.signUp(<generated>) ~[main/:na]
```