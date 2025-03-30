# Lambda

람다 표현식은 익명 클래스의 가독성이 떨어지는 단점을 보완하기 위해 만들어졌다. 람다 표현식은 인터페이스에 메소드가 "한 개"인 것들만 적용 가능하다. 예를들면 자바에서 제공하는 인터페이스 중 메소드가 하나인 인터페이스는 다음과 같다.

- java.lang.Runnable
- java.util.Comparator
- java.io.FileFilter
- java.util.concurrent.Callable
- java.security.PrivilegeAction
- java.nio.file.PathMatcher
- java.lang.reflect.InvocationHandler

## Lambda 표현식의 구성
람다의 표현식에 대한 구성은 다음과 같다
```
매개 변수 목록 - 화살표 토큰 - 처리 식
ex) (int x, int y) -> x + y
```

## 예시 1
한 개의 메소드를 가진 인터페이스를 구현해보자.
```java
public interface Calculate {
    int operation(int a, int b);
}
```

해당 인터페이스를 구현한 익명 클래스는 다음과 같이 사용할 수 있다.
```java
public static void main(String[] args) {
    Calculate add = new Calculate() {
        @Override
        public int operation(int a, int b) {
            return a + b;
        }
    };

    System.out.println(add.operation(1, 2));
}
```

익명 클래스는 사용하는 대신 람다 표현식을 사용하면 다음과 같이 깔금한 코드 작성이 가능하다.

```java
public static void main(String[] args) {
    Calculate add = (a, b) -> a + b;
    System.out.println(add.operation(1,2));
}
```

## 인터페이스에 선언된 메소드가 두 개 이상일 경우
앞서 작성한 Calculate 인터페이스의 메소드는 단 한개만 선언되어 있기 때문에 람다 표현식을 통해 변수 a, b를 받은 처리 식을 바로 작성해주면 Calculate 인터페이스의 operation 메소드의 int a, int b의 매개변수를 받아 처리식으로 결과를 반환하겠다는 의미를 가진다.

만약 Calculate 인터페이스에 또 다른 함수를 추가하면 기존 코드에 컴파일 오류가 발생한다.
```java
public interface Calculate {
    int operation(int a, int b);
    int anotherOperation(int a, int b); // 새로운 메소드
}
```

```
error: incompatible types: Calculate is not a functional interface
        Calculate sub = (a, b) -> a - b;
                        ^
    multiple non-overriding abstract methods found in interface Calculate
```

따라서 람다 표현식을 사용하는 인터페이스의 메소드의 수를 한 개로 제한하기 위해 아래와 같이 @FunctionalInterface라는 어노테이션을 사용할 수 있다.
```java
@FunctionalInterface
public interface Calculate {
    int operation(int a, int b);
}
```

## 예시 2
Runnable 인터페이스를 구현한 익명 클래스를 람다 표현식으로 수정해보자. 우선 익명 클래스로 run 메소드를 오버라이딩 해보자.
```java
public static void main(String[] args) {
    Runnable runnable = new Runnable() {
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName());
        }
    };

    new Thread(runnable).start();
}
```

다음으로 람다 표현식을 사용해보자. 아래와 같이 인터페이스에 선언된 매개변수가 없는 경우 소괄호는 빈 값으로 둔다.
```java
public static void main(String[] args) {
    Runnable runnable = () -> {
        System.out.println(Thread.currentThread());
    };
    
    new Thread(runnable).start();
}
```

만약 위 처럼 표현식이 한줄로 작성되어 있을 경우에는 아래와 같이 중괄호를 생략할 수 있다.
```java
public static void main(String[] args) {
    Runnable runnable = () -> System.out.println(Thread.currentThread());

    new Thread(runnable).start();
}
```

실행 결과
```
> Task :Java.main()
Thread-0
```

# Stream
스트림은 Java 8에서 추가된 기능으로써 배열과 컬렉션과 같은 연속된 데이터를 처리하는데 사용한다. 

## 구조
Stream의 구조는 스트림 생성 -> 중개 연산(Optional) -> 종단 연산으로 구성된다
```java
list.stream() // 스트림 생성
        .filter(x -> x < 10) // 중개 연산
        .count(); // 종단 연산
```

### 스트림의 연산
스트림에서 제공하는 연산의 종류는 다음과 같다. 연산은 스트림을 사용하면서 반드시 있어야 하는 것은 아니다. 일반적으로 forEach, filter, map을 가장 많이 사용한다. 
| 연산자 | 설명 |
| --- | --- |
| filter(pred) | 데이터를 조건으로 거를 때 사용 |
| map(mapper) | 데이터를 특정 데이터로 변환 |
| forEach(block) | for 루프를 수행하는 것 처럼 각각의 항목을 꺼냄 |
| flatMap(flat-mapper) | 스트림의 데이터를 잘게 쪼개서 새로운 스트림 제공 |
| sorted(comparator) | 데이터의 정렬 |
| toArray(array-factory) | 배열로 변환 |
| any / all / nonMatch(pred) | 일치하는 것을 찾음 |
| findFirst / Any(pred) | 맨 처음이나 순서와 상관없는 것을 찾음 |
| reduce(binop) / reduce(base, binop) | 결과를 취합 |
| collect(collector) | 원하는 타입으로 데이터를 리턴 |


## 예시
다음의 구조를 가지는 StudentDTO 클래스를 만들어서 스트림을 활용해보자.
```java
public class StudentDTO {
    private String name;
    private int age;

    public StudentDTO(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

먼저 forEach 연산을 통해 StudentDTO 객체의 이름을 출력해보자.
```java
public static void main(String[] args) {
    List<StudentDTO> studentList = new ArrayList<>();

    studentList.add(new StudentDTO("John", 20));
    studentList.add(new StudentDTO("Steve", 25));
    studentList.add(new StudentDTO("Conor", 30));

    studentList.stream()
            .forEach(studentDTO -> System.out.println(studentDTO.getName()));
}
```

실행 결과
```
> Task :Java.main()
John
Steve
Conor
```

### map + forEach
map과 forEach 연산을 같이 사용할 수 있다. 다음 예시를 보자
```java
public static void main(String[] args) {
    List<StudentDTO> studentList = new ArrayList<>();

    studentList.add(new StudentDTO("John", 20));
    studentList.add(new StudentDTO("Steve", 25));
    studentList.add(new StudentDTO("Conor", 30));

    studentList.stream()
            .map(studentDTO -> studentDTO.getName()) // 새로운 문자열 스트림을 반환
            .forEach(name -> System.out.println(name));
}
```

- map 연산은 map 연산식이 적용된 새로운 스트림 객체를 반환한다. 위 예시에서는 `studentDTO.getName()` map 연산식이 적용된 새로운 문자열 스트림을 반환한다.
- 마지막으로 forEach 연산에서 처리되는 스트림은 StudentDTO 스트림이 아니라 앞에 map 연산이 처리된 문자열 스트림이다.

실행 결과
```
> Task :Java.main()
John
Steve
Conor
```

### filter
스트림에서 filter 연산은 filter 연산식에 조건에 맞는 데이터를 가져올때 사용한다.

```java
public static void main(String[] args) {
    List<StudentDTO> studentList = new ArrayList<>();

    studentList.add(new StudentDTO("John", 20));
    studentList.add(new StudentDTO("Steve", 25));
    studentList.add(new StudentDTO("Conor", 30));

    List<StudentDTO> resultList = studentList.stream()
            .filter(studentDTO -> studentDTO.getName().equals("Conor"))
            .toList();

    for(StudentDTO s: resultList) {
        System.out.println(s.getName());
    }
}
```

실행 결과
```
> Task :Java.main()
Conor
```

## 메소드 참조
메소드 참조란 람다 표현식이 단 하나의 메소드를 호출하는 경우에 람다 표현식에서 매개변수를 제거하고 사용할 수 있는 표현식이다. 다음은 메소드 참조의 종류이다.

위 예시에 있던 StudentDTO 리스트를 순회하며 이름을 출력하는 아래와 같은 코드를 메소드 참조를 통해 수정할 수 있다.
```java
studentList.forEach(studentDTO -> System.out.println(studentDTO));

// 메소드 참조 사용
studentList.forEach(System.out::println);
```

메소드 참조의 종류는 총 4가지가 있다.
| 종류 | 예 |
| --- | --- |
| static 메소드 참조 | ContainingClass::staticMethodName |
| 특정 객체의 인스턴스 메소드 참조 | containingObject::instanceMethodName |
| 특정 유형의 임의의 객체에 대한 인스턴스 메소드 참조 | ContainingType::methodName |
| 생성자 참조 | ClassName::new |
