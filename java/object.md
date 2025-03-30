# Object

모든 자바 클래스의 최상위 클래스이다. 11개의 메소드만으로 구성되어 있으며 필드는 가지지 않는다. 자바의 모든 클래스는 Object 클래스의 메소드를 바로 사용할 수 있다.

Object 클래스는 객체를 처리하기 위한 기본적인 메소드들을 제공한다. 객체와 쓰레드를 위한 메소드로 나뉜다. 종류는 다음과 같다

객체를 위한 메소드 종류는 다음과 같다.

| 메소드 | 설명 |
| --- | --- |
| protected Object clone() | 객체의 복사본을 만들어 리턴한다. |
| public boolean equals(Object obj) | 현재 객체와 매개변수의 객체가 같은지 확인한다. |
| protected void finalize() | 더 이상 객체를 참조하는 대상이 없을 때, 가비지 컬렉터에 의해서 호출된다. JDK 9 이후로는 deprecated 처리가 되었다. |
| public Class<?> getClass() | 객체의 Class 클래스 객체를 리턴한다. |
| public int hashCode() | 객체의 대한 해시코드를 리턴한다. |
| public String toString() | 객체를 문자열로 표현하는 값을 리턴한다 |

쓰레드를 위한 메소드 종류는 다음과 같다.
| 메소드 | 설명 |
| --- | --- |
| public void notify() | 객체의 모니터에 대기하고 있는 단일 쓰레드를 깨운다. |
| public void notifyAll() | 객체의 모니터에 대기하고 있는 모든 쓰레드를 깨운다. |
| public void wait() | 다른 쓰레드가 현재 객체에 대한 notify()나 notifyAll()을 호출할 때까지 현재 쓰레드가 대기하고 있도록 한다. |
| public void wait(long timeout) | wait() 메소드과 동일한 기능을 제공한다. 매개변수에 지정한 밀리초 만큼 대기한다. 지정한 시간을 넘어 섰을 때에는 현재 쓰레드는 다시 깨어난다. |
| public void wait() | wait() 메소드과 동일한 기능을 제공하지만, 밀리초 + 나노초 만큼만 대기한다. |

## 주요 메소드

Object 클래스가 제공하는 주요 메소드들을 알아보자.

### toString
객체를 문자열로 표현해주는 메소드이다. 내부 구현은 다음과 같다
```java
public String toString() {
	return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```
getClass를 사용해 Class 객체안에 getName 메소드를 호출한다. 이 값은 현재 패키지 이름과 클래스 이름이다. '@'는 단순한 구분자 이며 나머지 값은 객체의 해시코드를 16진수로 변환한 값이다.

간단하게 이름과 성을 가지는 Human 클래스를 만들어 toString 메소드를 호출해보자
```java
public class Human {
  private String firstName;
  private String lastName;

  public Human(String firstName, String lastName) {
      this.firstName = firstName;
      this.lastName = lastName;
  }
}
```

```java
public static void main(String[] args) {
    Human human = new Human("Jon", "Jones");
    System.out.println(human.toString());
}
```

실행 결과
```
com.study.sj.objects.Human@251a69d7
```

아래와 같이 공식 문서에서는 사람이 읽기 편하고 간결하게 모든 클래스에 대해서 toString() 메소드의 오버라이딩이 권장된다고 나와있다.
```
API Note:
(중간 생략) In general, the toString method returns a string that "textually represents" this object. The result should be a concise but informative representation that is easy for a person to read. It is recommended that all subclasses override this method.
```

Human 클래스에 있는 toString() 메소드를 알맞게 오버라이딩 해보자.
```java
public class Human {
    private String firstName;
    private String lastName;

    // 생성자 생략

    public String toString() {
      return "Human{" +
              "firstName='" + firstName + '\'' +
              ", lastName='" + lastName + '\'' +
              '}';
    }
}
```
```java
public static void main(String[] args) {
    Human human = new Human("Jon", "Jones");
    System.out.println(human.toString());
}
```
실행 결과
```
Human{firstName='Jon', lastName='Jones'}
```

### equals
객체의 동등함 (데이터가 같은 지)를 비교할때 사용하는 메소드이다. 내부 구현은 다음과 같다.
```java
public boolean equals(Object obj) {
    return (this == obj);
}
```
Object 클래스 내부 구현에서는 인자로 들어오는 객체와 단순히  == 연산자를 통해 두 객체의 주소값를 비교하고 있다.

equals 메소드를 오버라이딩 하기 전에는 어떤 문제가 발생하는지 알아보자.

성과 이름을 가지는 Human 클래스를 그대로 활용해보자.
```java
public class Human {
    private String firstName;
    private String lastName;

    public Human(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}

```

main 메소드에서 똑같은 성과 이름을 가진 객체를 2개 생성해보고 equals 메소드를 호출해 비교해보자
```java
public static void main(String[] args) {
    Human human1 = new Human("Jon", "Jones");
    Human human2 = new Human("Jon", "Jones");

    boolean result = human1.equals(human2);
    System.out.println(result);
}
```

equals 메소드를 호출해도 결과는 false가 나온다. 똑같은 성과 이름을 가진 객체여도 Object의 equals 메소드는 두 객체의 주소값이 일치하는지만 비교한다. 두 객체의 성과 이름이 같아도 저장된 주소는 다르기 때문에 false의 결과가 나온다. 이렇게 되면 객체 내부의 데이터가 같음을 비교하는 equals의 취지와 맞지 않다. 

내부 데이터를 비교할 수 있도록 equals 메소드를 재정의 했다. 여기서는 성과 이름을 비교하고 구체적인 조건은 주석을 확인해보자.
```java
public class Human {
    private String firstName;
    private String lastName;
    
    public Human(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    @Override
    public boolean equals(Object obj) {
        // 객체의 주소값이 일치하면 당연히 똑같은 객체
        if (obj == this) {
            return true;
        }

        // 인자로 들어오는 객체가 null 이거나 Human 클래스가 아니면 false
        if (obj == null || !(obj instanceof Human)) {
            return false;
        }

        // 인자로 들어오는 객체를 Human 타입으로 캐스팅
        Human human = (Human) obj;

        // 성과 이름을 비교하여 둘 다 똑같으면 true
        return human.firstName.equals(this.firstName) && human.lastName.equals(this.lastName);
    }
}
```

다시 main 메소드를 실행해보면 결과가 true로 나온다.
```java
public static void main(String[] args) {
    Human human = new Human("Jon", "Jones");
    Human human2 = new Human("Jon", "Jones");

    boolean result = human.equals(human2);
    System.out.println(result);
}
```

실행 결과
```
> Task :Java.main()
true
```

## hashCode
어떤 객체를 대표하는 해시값을 반환한다. 해시란 가변적인 데이터를 고정된 길이의 데이터로 변환하는 함수이다. 자바에서 제공하는 해시값을 사용하는 HashTable, HashMap, HashSet과 같은 클래스에서 사용한다. 

내부 구현은 native 메소드로 되어 있다.
```java
@IntrinsicCandidate
public native int hashCode();
```

Object 클래스의 hashCode 메소드는 객체가 저장된 주소값을 16진수로 반환한다. 이대로 사용하게 되면 위에 equals 예시와 비슷하게, 내부 데이터가 같은 동등한 객체 임에도 불구하고 다른 hashCode 값을 반환하게 된다. 

자바의 해시 관련 컬렉션에서는 데이터를 다룰 때 equals 메소드와 hashCode 메소드 결과의 일관성이 필요하다. 따라서 hashCode 메소드 또한 개발자에 의해 적절하게 오버라이딩 해줘야 한다.

Object의 hashCode를 그대로 사용했을 때 main 메소드로 두 객체의 hashCode를 출력하고 같은지 비교해보자.
```java
public static void main(String[] args) {
    Human human1 = new Human("Jon", "Jones");
    Human human2 = new Human("Jon", "Jones");

    int human1Hash = human1.hashCode();
    int human2Hash = human2.hashCode();

    System.out.println("human1Hash : " + human1Hash);
    System.out.println("human2Hash : " + human2Hash);

    System.out.println(human1Hash == human2Hash);
}
```

실행 결과는 당연히 false 이다.
```java
> Task :Java.main()
human1Hash : 622488023
human2Hash : 1933863327
false
```

hashCode 메소드를 적절하게 오버라이딩 해주자.
```java
public class Human {
    private String firstName;
    private String lastName;

    public Human(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    @Override
    public int hashCode() {
        return Objects.hash(firstName, lastName);
    }
}
```

다시 main 메소드를 실행하면 실행 결과는 다음과 같다. (main 메소드 생략)
```
human1Hash : 74071887
human2Hash : 74071887
true
```

개발자가 hashCode()를 직접 오버라이딩 할때는 다음과 같은 조건을 만족해야 한다

- hashCode 메소드의 반환값은 자바 어플리케이션 실행 도중에는 항상 동일한 int 값을 반환해야 한다. 하지만 새롭게 자바 어플리케이션을 실행할때마다 값이 동일할 필요는 없다.
- equals의 결과가 true가 나온 두 객체의 hashCode 메소드의 반환값은 동일해야 한다
- equals의 결과가 false가 나온 두 객체의 hashCode 메소드의 반환값은 항상 다를 필요는 없지만 다른 값을 반환하도록 오버라이딩 하면 해쉬테이블의 성능을 개선시킬 수 있다