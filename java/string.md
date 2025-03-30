# String

String 클래스는 자바에서 가장 많이 사용하는 클래스이다. String은 주소값을 가지는 참조형 타입으로써 한번 선언되면 값을 다시 바꿀 수 없는 불변객체이다. String 클래스의 주요 기능에 대해 정리해보자.

## getBytes
String 클래스의 getBytes 메소드는 문자열을 byte 배열로 변환해준다. 매개변수에 따라 시스템의 기본 Character Set이나 별도의 Character Set을 지정해 줄 수 있다. Character Set(문자표)이란 문자를 이진수로 표현하는 규칙이다. 각 문자는 특정 이진수로 일대일 매핑이 된다. 대표적으로 ASCII, EUC-KR, UTF-8, UTF-16등이 있다. 

```java
public static void main(String[] args) {
    String korean = "한글";

    try {
        byte[] bytes = korean.getBytes();
        byte[] bytesUtf8 = korean.getBytes("UTF-8");
        byte[] bytesUtf16 = korean.getBytes("UTF-16");

        for(byte b: bytes) {
            System.out.print(b + " ");
        }

        System.out.println();

        for(byte b: bytesUtf8) {
            System.out.print(b + " ");
        }

        System.out.println();

        for(byte b: bytesUtf16) {
            System.out.print(b + " ");
        }
    } catch (UnsupportedEncodingException e) {
        throw new RuntimeException(e);
    }
}
```

실행 결과
```
> Task :Java.main()
-19 -107 -100 -22 -72 -128 
-19 -107 -100 -22 -72 -128 
-2 -1 -43 92 -82 0 
```

## 문자열이 같은지 비교하는 메소드
String 클래스에서 제공하는 문자열이 같은지 비교하는 메소드는 크게 equals, compareTo, contentEquals로 분류된다.

### equals, equalsIgnoreCase
두 문자열이 같은지 비교한다.
```java
public static void main(String[] args) {
    String a = "Hello";
    String b = "World";
    String c = "Hello";
    String d = "hello";

    System.out.println(a.equals(b));
    System.out.println(a.equals(c));
    System.out.println(a.equals(d));
}
```
실행 결과
```
> Task :Java.main()
false
true
false
```

대소문자를 무시하고 두 문자열이 같은지 비교한다. 문자열의 비교는 대소문자 비교가 포함되어 있으므로 주로 문자열을 비교할때는 비교하고자 하는 문자열간의 소문자, 대문자의 형식을 맞춰놓고 비교하거나 equalsIgnoreCase를 사용한다.
```java
public static void main(String[] args) {
    String a = "Hello";
    String d = "hello";

    System.out.println(a.equalsIgnoreCase(d));
}
```

실행 결과
```
> Task :Java.main()
true
```

### compareTo, compareToIgnoreCase
두 문자열을 비교했을 때 비교하고자 하는 문자열의 알파벳 순서상의 차이를 정수로 반환한다. 알파벳 순으로 앞에 있으면 양수를 뒤에 있으면 음수를 반환한다. 마찬가지로 대소문자를 무시하는 compareToIgnoreCase 메소드가 있다.

```java
public static void main(String[] args) {
    String a = "a";
    String b = "b";
    String c = "c";

    System.out.println(b.compareTo(a));
    System.out.println(b.compareTo(b));
    System.out.println(b.compareTo(c));
}
```

실행 결과
```
> Task :Java.main()
1
0
-1
```

### contentEquals
매개 변수로 넘어오는 CharSequence 인터페이스를 구현한 객체가 문자열와 같은지 비교한다. CharSequence 인터페이스를 구현한 클래스는 대표적으로 String, StringBuilder, StringBuffer가 있다.

```java
public static void main(String[] args) {
    String a = "Hello World";

    StringBuffer stringBuffer = new StringBuffer();
    stringBuffer.append("Hello").append(" World");

    System.out.println(a.contentEquals(stringBuffer));

    StringBuilder stringBuilder = new StringBuilder();
    stringBuilder.append("Hello").append(" World");

    System.out.println(a.contentEquals(stringBuilder));

    System.out.println(a.contentEquals(new String("Hello World")));
}
```

실행 결과
```
> Task :Java.main()
true
true
true
```

## StringBuilder vs StringBuffer
자바에서 String은 한번 객체를 생성하면 다시 바꿀 수 없는 불변한 객체다. 

코드를 작성할때 아래처럼 문자열 + 문자열과 같은 연산을 사용하면 어떻게 될까?

```java
public static void main(String[] args) {
    String a = "Hello";
    a = a + " World";

    System.out.println(a);
}
```

실행 결과
```
> Task :Java.main()
Hello World
```

1. a 변수에 초기화 한 “Hello” 문자열 객체가 초기화된다.
2. a 변수에 “ World”가 추가된 새로운 문자열 객체 = “Hello World”가 생성되고 다시 a 변수에 할당된다.

따라서 맨 처음 a 변수에 초기화 했던 “Hello” 문자열 객체는 더 이상 사용할 수 없다. “Hello” 문자열 객체는 메모리에 남게 되며 GC의 대상이 된다.

이런 비효율적인 문제를 해결하기 위해 자바에서는 StringBuilder, StringBuffer 클래스를 사용한다. append 메소드를 활용해서 문자열 객체를 계속 더하고 마지막에 문자열 객체를 한번에 생성해서 메모리 낭비를 줄인다.

```java
public static void main(String[] args) {
    StringBuilder sb = new StringBuilder();

    sb.append("Hello");
    sb.append(" World");

    System.out.println(sb);
}
```
```java
public static void main(String[] args) {
    StringBuffer sb = new StringBuffer();

    sb.append("Hello");
    sb.append(" World");

    System.out.println(sb);
}
```

실행 결과
```
> Task :Java.main()
Hello World
```

JDK 5 이상부터는 String에 더하기 연산을 할 경우 해당 연산을 자동으로 StringBuilder로 변환해준다. 반복문 내부에서는 변환을 해주지 않으므로 StringBuilder나 StringBuffer를 사용한 문자열 처리가 필요하다.

### StringBuilder vs StringBuffer

두 객체의 차이점은 StringBuffer 클래스는 내부 메소드가 synchronized 키워드로 구현되어 있어 thread-safe하다는 특성이 있다. 속도 측면에서는 StringBuilder가 더 빠르다. 하지만 여러 쓰레드에서 동시에 접근하는 일이 있을 경우에는 StringBuffer를 사용하는게 좋다.

## String Constant Pool
String 객체를 문자열 상수형으로 선언하면 해당 객체는 JVM의 String Constant Pool 영역에 저장된다.
```java
public static void main(String[] args) {
    // 문자열 상수
    String a = "Hello World";
}
```

자바는 문자열 객체를 재사용하기 위해서 힙 영역에 따로 String Constant Pool에 문자열 객체들을 관리한다. 동일한 문자열이 이미 pool에 존재하는 경우 해당 객체를 재사용한다. 아래 예시처럼 변수 a,b는 String Constant Pool에 있는 동일한 문자열 객체를 참조한다. 

```java
public static void main(String[] args) {
    // 문자열 상수
    String a = "Hello World";
    String b = "Hello World";

    System.out.println(a==b);
}
```

실행 결과
```
> Task :Java.main()
true
```

하지만 new 키워드를 사용해 문자열 객체를 생성할 경우 해당 객체는 String Constant Pool 영역에 저장되는것이 아니라 힙 영역에 저장된다. 따라서 메모리를 효율적으로 쓰려면 문자열 객체를 가급적이면 `= ""`를 활용해 상수형으로 선언하는것이 좋다. 아래 예시처럼 변수 b를 초기화 할 때 String Constant Pool에 있던 "Hello World" 객체를 재사용하는것이 아니라 힙 영역에 아예 새로운 문자열 객체를 생성해서 저장한다. 

```java
public static void main(String[] args) {
    String a = "Hello World";
    String b = new String("Hello World");

    System.out.println(a==b);
}
```

실행 결과
```
> Task :Java.main()
false
```