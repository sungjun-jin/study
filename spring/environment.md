# Environment

ApplicationContext가 제공하는 기능중 하나인 EnvironmentCapable 스프링에서 제공하는 Profile, Property를 가져올 수 있는 인터페이스이다. 

## Profile

어플리케이션이 실행되는 Profile에 대해 사용할 빈(bean)들의 구성을 관리한다. 예를들어 개발 환경에 사용하는 빈과 운영 환경에 사용하는 빈을 profile을 통해 각각 그룹화 할 수 있다. 

## Profile 사용법

### Profile 가져오기
Enviroment 빈의 getDefaultProfiles, getActiveProfiles 메소드를 활용해 어플리케이션의 기본 profile과 현재 적용중인 profile을 가져올 수 있다.

```java
@Component
public class AppRunner implements ApplicationRunner {
    private final Environment environment;

    public AppRunner(Environment environment) {
        this.environment = environment;
    }

    @Override
    public void run(ApplicationArguments args) {
        System.out.println(Arrays.toString(environment.getDefaultProfiles()));
        System.out.println(Arrays.toString(environment.getActiveProfiles()));
    }
}
```

### Profile 지정하기
Profile은 클래스를 활용한 빈 등록 방식과 메소드를 활용한 빈 등록 방식에 둘 다 사용할 수 있다.
#### 클래스에 지정하기

- @Component 지정하기
    ```java
    @Profile("test")
    @Component
    public class TestConfiguration {
        
    }
    ```

- @Configuration에 지정하기
    ```java
    @Profile("test")
    @Configuration
    public class TestConfiguration {
        
    }
    ```

- Profile이 test가 아닌 경우에 빈 등록하기
    ```java
    @Profile("!test") // Profile 표현식 활용
    @Component
    public class TestConfiguration {
        
    }
    ```

#### 메소드에 지정하기
```java
@Configuration
public class Configuration {
    @Profile("test")
    @Bean
    public TestBookRepository testBookRepository() {
        return new TestBookRepository();
    }
}
```

### Profile로 Application 구동하기

#### Intellij IDE 사용하기
![active-profiles](./img/active_profiles.png)

run / debug configurations -> Active Profiles

#### VM Option 사용하기
```
-Dspring.profiles.active="test"
```

## Property

다양한 방법으로 정의하는 어플리케이션에 대한 설정 값이다. Key-Value로 구성되어 있다.

### Property 가져오기
Environment 빈의 getProperty 메소드를 사용한다.
```java
@Component
public class AppRunner implements ApplicationRunner {
    private final Environment environment;

    public AppRunner(Environment environment) {
        this.environment = environment;
    }

    @Override
    public void run(ApplicationArguments args) {
        System.out.println(environment.getProperty("app.name"));
    }
}
```

### Property 사용하기


#### VM Options 사용하기
`-Dkey="value"` 형식으로 사용한다 예를들어 key가 `app.name` 이고 value가 `property`면 다음과 같이 사용할 수 있다.

```
-Dapp.name=property
```

#### 파일로 Property 관리하기
resources 디렉토리 내부에서 application.properties, application.yml로 어플리케이션에서 사용할 property를 관리할 수 있다.

- application.properties
    ```properties
    server.port=8080

    spring.profiles.active=test
    spring.security.user.name=user
    spring.security.user.password=user

    ```

- application.yml
    ```yml
    server:
    port: 8080

    spring:
    profiles:
        active: test
    security:
        user:
        name: name
        password: password
    ```