# Annotation

## 어노테이션과 역할

어노테이션은 클래스, 메소드, 변수 등 선언시에 @를 사용하는 것을 말한다. 역할은 다음과 같다

- 컴파일러에게 정보를 알려준다
- 컴파일할 때 부가적인 코드를 생성한다
- 실행할 때 별도의 처리를 한다

## 자바의 어노테이션

**어노테이션은 프로그램에 영향을 줄 수도 있고 안 줄 수도 있다**. 다음은 자바에서 정해져 있는 3가지 어노테이션 목록이다.

- Override : 해당 메소드가 부모 클래스의 메소드를 Override 했다는 것을 명시적으로 선언한다.
- Deprecated: 더 이상 사용하지 않는 클래스나 메소드에 사용한다
- SupressWarnings: 특정코드에서 컴파일러가 경고를 제외할때 사용한다

### Override

먼저 Override 어노테이션의 예시를 들어보자. 부모 클래스를 작성한다.

```java
public class Parent {
    private String name;

    public Parent(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }
    
    public void printName() {
        System.out.println("printName() - Parent : " + this.name);
    }
}
```

자식 클래스에서 printName 메소드를 오버라이드 하고 메소드 상단에 `@Override` 어노테이션을 추가한다.

```java
public class Son extends Parent{
    public Son(String name) {
        super(name);
    }

    @Override
    public void printName() {
        System.out.println("printName() - Son : " + this.getName());
    }
}
```

자식 객체를 생성해 printName을 호출하면 정상적으로 오버라이딩된 메소드가 실행된다.

```java
Son son = new Son("손흥민");
son.printName();
```

```
printName() - Son : 손흥민
```

여기서 자식 클래스의 printName 메소드에 String arg라는 매개변수를 추가하고 재실행 해보자.

```java
public class Son extends Parent{
    // 생성자 생략

    @Override
    public void printName(String arg) {
        System.out.println("printName() - Son : " + this.getName());
    }
}
```

“어노테이션으로 오버라이드 한다고 했는데 그런 메소드가 부모 클래스에 없다” 라는 내용의 컴파일 에러가 발생한다.

```java
error: method does not override or implement a method from a supertype
    @Override
```

### Deprecated

예시로 사용하고 있던 printName 메소드에 `@Deprecated` 어노테이션을 추가하고 나서 호출해보자.

```java
public class Son extends Parent{
    // 생성자 생략

    @Deprecated
    public void printName() {
        System.out.println("printName() - Son : " + this.getName());
    }
}
```

컴파일도 잘 되고 실행도 잘 되지만, Deprecated API를 사용하는 메소드가 있으니 `-Xlint:deprecation` 이라는 옵션을 추가해서 컴파일을 다시 해보라는 경고 메세지가 나온다.

```
Note: 생략/AnnotationTest.java uses or overrides a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
> Task :processTestResources NO-SOURCE
> Task :testClasses
> Task :test
printName() - Son : 손흥민
```

### SupressWarnings

방금 Deprecated 예제에서 활용해 테스트 메소드에 SupressWarning 어노테이션을을 추가하고 deprecated 메소드를 호출해보자.

SupressWarning() 어노테이션을 활용해 속성을 지정해줄 수 있다. 지정할 수 있는 속성들은 다음과 같다.

- **all** : 모든 경고
- **cast** : 캐스트 연산자 관련 경고
- **dep-ann** : 사용하지 말아야 할 주석 관련 경고
- **deprecation** : 사용하지 말아야 할 메서드 관련 경고
- **fallthrough** : switch문에서 break 누락 관련 경고
- **finally** : 반환하지 않는 finally 블럭 관련 경고
- **null** : null 분석 관련 경고
- **rawtypes** : 제너릭을 사용하는 클래스 매개 변수가 불특정일 때의 경고
- **unchecked** : 검증되지 않은 연산자 관련 경고
- **unused** : 사용하지 않는 코드 관련 경고

여기서는 deprecated 속성을 지정해준다.

```java
@Test
@SuppressWarnings("deprecation")
public void suppressWarningTest() {
    Son son = new Son("손흥민");
    son.printName();
}
```

결과는 deprecated 된 메소드도 정상적으로 호출된다. deprecated 관련 경고 메세지도 나타나지 않는다.

```
> Task :compileJava UP-TO-DATE
> Task :processResources NO-SOURCE
> Task :classes UP-TO-DATE
> Task :compileTestJava
> Task :processTestResources NO-SOURCE
> Task :testClasses
> Task :test
printName() - Son : 손흥민
BUILD SUCCESSFUL in 379ms
```

## 메타 어노테이션
메타 어노테이션 (Meta Annotation)은 어노테이션을 정의할 때 어노테이션에 대한 정보를 제공하고 어떻게 처리할지를 나타낸다. 종류는 다음과 같다
- @Target
- @Retention
- @Documented
- @Inherited


### @Target
어노테이션을 어떤 것에 적용할지를 선언할 때 사용한다.
```java
@Target(ElementType.METHOD)
```

Target 괄호 안에 속성을 지정해주면 된다. 지정할 수 있는 속성들은 다음과 같다.
- **CONSTRUCTOR** : 생성자 선언 시
- **FIELD** : enum 상수를 포함한 필드 값 선언 시
- **LOCAL_VARIABLE** : 지역 변수 선언 시
- **METHOD** : 메소드 선언 시
- **PACKAGE** : 패키지 선언 시
- **PARAMETER** : 매개 변수 선언 시
- **TYPE** : 클래스, 인터페이스, enum 등 선언 시

### @Retention
영어로 "유지", "보유"라는 뜻이다. 어노테이션 정보가 언제까지 유지되는지 결정한다.
```java
@Retention(RetentionPolicy.RUNTIME)
```
지정할 수 있는 속성들은 다음과 같다.
- **SOURCE** : 어노테이션 정보가 소스 코드 수준까지만 유지된다. 컴파일시 사라진다
- **CLASS**: 어노테이션 정보가 컴파일된 클래스 파일에 포함되지만, 런타임 시에는 사용되지 않는다
- **RUNTIME** : 어노테이션 정보가 런타임까지 유지된다

### @Documented
어노테이션에 대한 정보가 Javadocs(API) 문서에 포함된다는 것을 선언한다

### @Inherited
모든 자식 클래스에서 부모 클래스의 어노테이션을 사용 가능하다는 것을 선언한다

### @Interface
**어노테이션은 아니고 자바에서 제공하는 키워드다.** 사용자 정의 어노테이션을 정의할 때 사용한다. 

### 예제
커스텀 어노테이션을 다음과 같이 선언해보자. 
```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface CustomAnnotation {
    int number();
    String text() default "This is the first annotation";
}
```
선언한 어노테이션에 대한 정보는 다음과 같다
- `@Target(ElementType.METHOD)` : 어노테이션을 메소드에 사용할 수 있다
- `@Retention(RetentionPolicy.RUNTIME)` : 어노테이션의 정보는 런타임까지 유효하다
- `int number(), String Text(), default` : 어노테이션 내부에는 메소드가 있고 리턴 타입을 각각 지정해줄 수 있다. default 키워드를 사용하면 기본값도 지정해줄 수 있다

어노테이션 사용은 다음과 같이 할 수 있다.
```java
public class Son extends Parent{
    // 생성자 생략

    @CustomAnnotation(number = 10, text = "Son")
    public void printName() {
        System.out.println("printName() - Son : " + this.getName());
    }

    @CustomAnnotation(number = 10)
    public void print() {
        System.out.println("print() - Son");
    }
}
```

여기서 Son 클래스의 메소드별로 어노테이션에 선언한 number, text에 대한 값을 확인하는 방법은 다음과 같다. 
```java
public static void main(String[] args) {
    // Class에 선언되어 있는 메소드의 목록을 getDeclaredMethods()로 가져온다
    Method[] methods = Son.class.getDeclaredMethods();

    // 메소드별로 반복문을 돌면서 선언된 어노테이션이 있는지 확인하고, 있을 경우 어노테이션의 선언된 값들을 출력한다
    for(Method method: methods) {
        CustomAnnotation annotation = method.getAnnotation(CustomAnnotation.class);

        if(annotation != null) {
            int number = annotation.number();
            String text = annotation.text();

            System.out.println("Method : " + method.getName());
            System.out.println("number : " + number);
            System.out.println("text : " + text);
            System.out.println();
        } else {
            System.out.println("Method : " + method.getName());
            System.out.println("There is no annotation in this method");
        }
    }
}
```
메소드 별로 선언한 어노테이션에 대한 값이 출력된다.
```
> Task :Java.main()
Method : print
number : 10
text : This is the first annotation

Method : printName
number : 10
text : Son
```