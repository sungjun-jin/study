# Enum 
Enum 클래스는 열거형 클래스라고도 하며 관련된 상수들을 그룹화하여 관리할 수 있는 클래스이다. enum 타입에 선언된 상수들은 각각의 객체로 관리된다.

## 기본 사용법
Enum 타입은"enum 클래스이름.상수 이름"을 지정하면 객체 생성이 완료된다.
```java
public enum OverTimeValues {
    THREE_HOUR,
    FIVE_HOUR,
    WEEKEND_FOUR_HOUR,
    WEEKEND_EIGHT_HOUR,
}
```

enum 클래스는 `클래스이름.상수이름` 을 지정함으로써 객체 생성이 완료된다. 선언은 상수를 모두 대문자로 나열해주면 된다. 띄워쓰기는 '-'로 연결한다.

### Switch문을 통한 예시

정의된 class를 바탕으로 다음과 같이 switch문에서 사용할 수 있다.

```java
public static void main(String[] args) {
    OverTimeValues values = OverTimeValues.FIVE_HOUR;

    int amount = 0;

    switch (values) {
        case THREE_HOUR:
            amount = 10000;
            break;
        case FIVE_HOUR:
            amount = 20000;
            break;
        case WEEKEND_FOUR_HOUR:
            amount = 30000;
            break;
        case WEEKEND_EIGHT_HOUR:
            amount = 40000;
            break;
    }

    System.out.println("Amount : " + amount);
}
```
실행 결과
```
> Task :Java.main()
Amount : 20000
```

## Enum 클래스의 부모는 무조건 java.lang.Enum이다

Enum 클래스에서는 부모클래스인 `java.lang.Enum` 외에 상속을 허용하지 않는다. 만약 다른 클래스 상속을 시도하면 컴파일러가 `No extends clause allowed for enum` 에러를 내뱉는다.

```java
public abstract class Enum<E extends Enum<E>>
        implements Constable, Comparable<E>, Serializable {

    private final String name;

    public final String name() {
        return name;
    }

    private final int ordinal;

    public final int ordinal() {
        return ordinal;
    }

    protected Enum(String name, int ordinal) {
        this.name = name;
        this.ordinal = ordinal;
    }

    public final boolean equals(Object other) {
        return this==other;
    }

    public final int hashCode() {
        return super.hashCode();
    }
```

주석을 생략한 java.lang.Enum 클래스이다. 우선 생성자는 protected 접근 제어자로 되어 있지만 프로그래머가 직접 호출할 수 없고 컴파일러에 의해서 클래스가 참조되는 시점에 한번 실행된다. 상수 하나당 인스턴스는 1개이며 public static final로 공개된다.

name, ordinal의 멤버 변수는 final로 선언되어 있다. 나머지 Enum 클래스에서 사용하는 주요 메소드인 ordinal, equals, hashCode는 모두 final로 선언되어 오버라이딩을 막고 있다.

## Enum 클래스에서 사용하는 주요 메소드
### valueOf

```java
@Test
public void valueOf() {
    OverTimeValues values = OverTimeValues.valueOf("WEEKEND_FOUR_HOUR");
    assertThat(values).isEqualTo(OverTimeValues.WEEKEND_FOUR_HOUR);
}
```
간단한 테스트 예시다. static 메소드로써 상수의 이름을 매개변수로 넘겨주면 해당하는 Enum을 가져온다. 만약 못 찾으면 IllegalArgumentException 예외가 발생한다.
    
### values
```java
public static void main(String[] args) {
    OverTimeValues[] values = OverTimeValues.values();
    
}
``` 
해당 Enum 클래스에 선언된 모든 객체들을 배열로 반환한다.

```
> Task :Java.main()
THREE_HOUR
FIVE_HOUR
WEEKEND_FOUR_HOUR
WEEKEND_EIGHT_HOUR
```

### name
```java
OverTimeValues value = OverTimeValues.FIVE_HOUR;
String name = value.name();
System.out.println(name);
```
열거형 객체가 가지고 있는 문자열을 반환한다. 반환되는 문자열은 정의한 상수의 이름과 동일하다.
```
FIVE_HOUR
```

### ordinal
```java
public static void main(String[] args) {
    OverTimeValues value = OverTimeValues.FIVE_HOUR;
    int order = value.ordinal();
    System.out.println(order);
}
```
열거형 객체의 순서를 반환한다.
```
> Task :Java.main()
1
```

### equals
```java
public static void main(String[] args) {
    OverTimeValues fiveHour = OverTimeValues.FIVE_HOUR;
    OverTimeValues fiveHour1 = OverTimeValues.FIVE_HOUR;

    OverTimeValues threeHour = OverTimeValues.THREE_HOUR;

    boolean result = fiveHour.equals(fiveHour1);
    boolean result2 = fiveHour.equals(threeHour);

    System.out.println(result);
    System.out.println(result2);
}
```
두 enum 객체가 동일한지 판단한다. Enum 클래스의 부모 클래스의 equals 메소드는 `==` 연산자를 사용해 객체의 주소값을 비교한다.
```
> Task :Java.main()
true
false
```

## 클래스에 상수 추가하기

바로 enum 클래스에 상수 값을 지정해줄 수 있다. 이런 경우에 별도로 멤버 변수를 추가해줘야 한다. 멤버 변수를 추가하면 생성자에도 매개변수 통해 초기화하는 코드를 추가시켜줘야 한다. enum 클래스의 생성자에 대한 접근제어자는 기본값이 default(package-private)이다. public이나 protected 생성자는 사용이 불가능하다.

```java
public enum OverTimeValues {
    THREE_HOUR(10000),
    FIVE_HOUR(20000),
    WEEKEND_FOUR_HOUR(30000),
    WEEKEND_EIGHT_HOUR(40000),
    ;

    private final int amount;

    OverTimeValues(int amount) {
        this.amount = amount;
    }

        public int getAmount() {
        return amount;
    }
}
```

다시 한번 테스트를 돌려보면 바로 할당된 상수 값을 바로 가져올 수 있다.

```java
public static void main(String[] args) {
    OverTimeValues fiveHour = OverTimeValues.FIVE_HOUR;
    System.out.println(fiveHour.getAmount());
}
```

```
> Task :Java.main()
20000
```

## 상수별로 메소드 구현하기
```java
public enum OverTimeValues {
    THREE_HOUR {
        @Override
        public void printInfo(Date date) {
            System.out.println("금일 : " + date + " 평일 연장 근무 시간은 세시간 입니다.");
        }
    },
    FIVE_HOUR {
        @Override
        public void printInfo(Date date) {
            System.out.println("금일 : " + date + " 평일 연장 근무 시간은 다섯시간 입니다.");
        }
    },
    WEEKEND_FOUR_HOUR {
        @Override
        public void printInfo(Date date) {
            System.out.println("금일 : " + date + " 주말 연장 근무 시간은 네시간 입니다.");
        }
    },
    WEEKEND_EIGHT_HOUR {
        @Override
        public void printInfo(Date date) {
            System.out.println("금일 : " + date + " 주말 연장 근무 시간은 여덟시간 입니다.");
        }
    },
    ;

    public abstract void printInfo(Date date);
}
```

enum 클래스에 선언된 상수와 관련된 상태 기반 동작의 메소드를 따로 구현할 수 있다. 구현하는 방법은 다음과 같다.
1. 추상 메소드를 선언한다.
2. enum 객체마다 추상 메소드를 오버라이드하여 구현한다.
3. `enum 객체.메소드명`으로 구현한 메소드를 호출한다.

실행 결과는 다음과 같다.
```java
public static void main(String[] args) {
    Date time = Calendar.getInstance().getTime();
    OverTimeValues fiveHour = OverTimeValues.FIVE_HOUR;
    fiveHour.printInfo(time);
}
```

```
> Task :Java.main()
금일 : Thu Jan 11 15:28:02 KST 2024 평일 연장 근무 시간은 다섯시간 입니다.
```