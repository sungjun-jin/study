# Generic
제네릭은 클래스, 인터페이스나 메소드를 사용할 때 내부 데이터에 대한 타입을 외부(사용하는 입장)에서 지정하는 방법이다. 제네릭을 활용하면 컴파일러가 외부에서 지정한 타입에 대한 검사를 할 수 있다. 

## 제네릭을 사용하기 전
아래와 같이 CastingDto 클래스는 Object 타입의 멤버 변수를 getter / setter와 함께 가지고 있다. 
```java
public class CastingDto {
    private Object object;

    public Object getObject() {
        return object;
    }

    public void setObject(Object object) {
        this.object = object;
    }
}
```

CastingDto를 메인 메소드에서 활용해보자. 멤버 변수의 타입이 Object이므로 아무 타입이든 저장할 수 있다. 간단한 예시로 String, StringBuilder 타입의 데이터를 setObject 메소드로 초기화하고 getObject 메소드로 가져와보자.

```java
public static void main(String[] args) {
    CastingDto stringDto = new CastingDto();
    stringDto.setObject("Hi");
    String str = (String) stringDto.getObject();

    CastingDto stringBuilderDto = new CastingDto();
    stringBuilderDto.setObject(new StringBuilder());
    StringBuilder sb = (StringBuilder) stringBuilderDto.getObject();
}
```

CastingDto 클래스의 멤버 변수는 Object 타입이라 setObject 메소드의 매개변수에는 아무 타입이나 들어갈 수 있다. 하지만 getObject 메소드를 사용할때는 Object 타입을 반환한다. 따라서 반환되는 객체에 대한 타입을 정확히 알고 형변환을 일일이 해줘야 하는 번거로움이 있다. 

CastingDto를 사용하는 프로그래머가 내부 멤버변수 object의 타입을 직접 결정할 수 없을까? 제네릭을 사용하면 가능하다.

## 제네릭 클래스

제네릭 문법을 적용하고 하는 클래스 이름 옆에 `<T>`를 사용한다. 여기서 T는 **타입 파라미터**라고 한다. "내부 멤버 변수 object의 타입은 사용자가 정해라" 라는 의미를 가진다고 볼 수 있다. 

제네릭에서 <>안에 들어 갈 수 있는 자료형은 **참조 자료형**뿐이다 (Integer, Double, Float등의 wrapper 클래스가 존재하는 이유 증 하나다).

```java
public class CastingGenericDto<T> {
    private T object;

    public T getObject() {
        return this.object;
    }

    public void setObject(T object) {
        this.object = object;
    }
}
```

T외에도 E, K, V 등과 같이 내부 데이터의 속성에 따라 자바에서 따르고 있는 컨벤션을 따른다.
- E : 요소 자바 컬렉션에서 주로 사용된다
- K : 키
- N : 숫자
- T : 타입
- V : 값
- S,U,V : 두 번째, 세 번째, 네 번쨰에 선언된 타입

### 타입 파라미터와 구체화 과정
String 타입으로 GenericDto를 선언하면 타입 파라미터로 String 타입이 전달되고 내부에서 T 타입을 활용하는 모든 멤버 변수와 메소드에 전파가 된다. 이를 구체화 (Specialization) 이라고 한다.

예를들어 아래와 같이 타입 변수 T에 String 타입을 전달하면
```java
GenericDto<String> stringGenericDto = new GenericDto<>();
```

다음과 같이 GenericDto 클래스 내부에 타입이 구체화된다. 전파된 타입을 기반으로 컴파일 단계에서 타입 체크를 한다.
```java
public class CastingGenericDto<String> {
    private String object;

    public String getObject() {
        return this.object;
    }

    public void setObject(String object) {
        this.object = object;
    }
}
```

작성한 제네릭 클래스를 메인 메소드에서 다시 활용해보자.
```java
public static void main(String[] args) {
    GenericDto<String> stringGenericDto = new GenericDto<>();
    stringGenericDto.setObject("Hi");
    String str = stringGenericDto.getObject();

    GenericDto<StringBuilder> stringBuilderGenericDto = new GenericDto<>();
    stringBuilderGenericDto.setObject(new StringBuilder());
    StringBuilder sb = stringBuilderGenericDto.getObject();
}
```

GenericDto의 <>안에 내부 멤버 변수로 활용될 object의 타입을 명시해주면 내부 멤버 변수는 해당 타입으로 지정된다. 따라서 getter 메소드를 사용할때 해당 타입으로 반환이 되므로 별도의 형변환 과정이 필요가 없다.

## 복수 타입 파라미터
복수 타입 파라미터를 가진 제네릭 클래스도 선언할 수 있다. 위 예시로 들었던 GenericDto에 타입 파라미터를 2개 받는 제네릭 클래스로 변경하여 String, StringBuilder 타입을 가지는 제네릭 클래스를 선언해보자.

```java
public class GenericDto<T, U> {
    private T firstObject;
    private U secondObject;

    public T getFirstObject() {
        return this.firstObject;
    }

    public U getSecondObject() {
        return this.secondObject;
    }

    public void setFirstObject(T firstObject) {
        this.firstObject = firstObject;
    }

    public void setSecondObject(U secondObject) {
        this.secondObject = secondObject;
    }
}
```

이를 메인 메소드에서 활용하면 다음과 같다.
```java
public static void main(String[] args) {
    GenericDto<String, StringBuilder> genericDto = new GenericDto<>();

    genericDto.setFirstObject("Hi");
    genericDto.setSecondObject(new StringBuilder());

    String str = genericDto.getFirstObject();
    StringBuilder sb = genericDto.getSecondObject();
}
```

## 제네릭 메소드
제네릭 메소드는 메소드의 **선언부**에 타입 파라미터가 들어간 형식으로 선언할 수 있다.
```java
public class GenericDto<T> {
    private T object;

    public T getObject() {
        return this.object;
    }

    public void setObject(T object) {
        this.object = object;
    }
    
    // 제네릭 메소드
    public <T> Class getObjectClass(T object) {
        return object.getClass();
    }
}
```

제네릭 메소드에 사용되는 타입 파라미터는 별도로 동작을 한다. 따라서 위 코드에서 사용하는 타입 파라미터는 모두 T이지만, 제네릭 메소드 getObjectClass의 타입 파라미터는 클래스에서 사용하는 타입 파라미터가 아니라 제네릭 메소드를 호출할때 전달받은 타입 파라미터로 동작한다. 아래 예시로 확인해보자.

```java
public static void main(String[] args) {
    GenericDto<String> genericDto = new GenericDto<>();

    // 메소드 타입 파라미터에 Integer 전달, 타입 파라미터는 메소드 매개변수의 타입으로 컴파일러가 추론이 가능하므로 생략할 수 있다.
    Class result = genericDto.getObjectClass(Integer.valueOf(10));
    System.out.println(result);
}
```

실행 결과
```
> Task :Java.main()
class java.lang.Integer
```

위 예시처럼 클래스 타입 파라미어와 메소드 타입 파라미터는 별도로 동작하는 모습을 확인할 수 있다.

## 제네릭 타입 범위 한정하기
 제네릭 클래스의 <> 안에 extends 키워드를 사용하여 타입 파라미터를 해당 클래스와 하위 타입의 클래스 타입으로 제한할 수 있다. 이를 제한된 타입 매개변수 (Bounded Type Parameter)라고 한다.

```java
public class Calculator<T extends Number> {
    public T add(T a, T b) {
        // 내부 구현 생략
    }
    public T minus(T a, T b) {
        // 내부 구현 생략
    }
    public T multiply(T a, T b) {
        // 내부 구현 생략
    }
    public T divide(T a, T b) {
        // 내부 구현 생략
    }
}
```
위 처럼 사칙연산의 기능을 하는 Caculator 제네릭 클래스를 만들었을때, 타입 파라미터를 Number클래스를 상속받은 클래스만 허용할 수 있다.

메인 메소드에서 String 타입 파라미터를 전달해 위 제네릭 클래스의 객체를 생성하려고 하면 다음과 같은 컴파일러 에러가 발생한다.
```java
    public static void main(String[] args) {
        // 가능
        Calculator<Integer> intCal = new Calculator<>();
        Calculator<Double> doubleCal = new Calculator<>();

        // 불가능
        Calculator<String> strCal = new Calculator<>();
    }
```

실행 결과
```
error: type argument String is not within bounds of type-variable T
        Calculator<String> strCal = new Calculator<>();
```

### <T super 타입>이 없는 이유
제네릭은 `<T extends 타입>`과 같은 상한 경계 타입 파라미터를 허용하지만 반대로 `<T super 타입>` 처럼 하한 경계에 의한 타입 범위를 제한할 수 없다. 이유는 허용을 하게되면 결국에는 Object 클래스까지 허용 범위가 올라가게 되면서 `<T extends Object>`와 같은 쓸모없는 코드가 되어버리기 때문이다.

## 제네릭의 형변환과 와일드카드

### 형변환
배열과는 다르게 제네릭은 타입 매개변수로 구체화된 내부 요소들간의 형변환 문제를 일으키지 않기 위해 상속관계의 타입간 형변환을 금지하고 있다. 

```java
public static void main(String[] args) {
    // 가능
    Object[] objects = new String[10];

    for(Object e: objects) {
        System.out.println(e);
    }

    // 불가능
    ArrayList<Objects> objectsList = new ArrayList<String>();

    for(Object e: objectsList) {
        System.out.println(e);
    }
}
```
위 예시를 객체지향의 다형성에 의해 보면 Object 배열에 String 배열은 충분히 들어갈 수 있다. 

### 공변성
공변성은 상속 관계의 클래스들 중에서 서로 형변환이 가능한 성질을 나타낸다. 예를들어 부모클래스 A와 자식 클래스 B가 있을 때, 공변, 불공변, 무공변 / 불공변이 될 수 있는 성질은 다음과 같다. 제네릭은 무(불)공변에 해당한다
- `B[]`가 `A[]`의 **하위** 타입이고, `List<B>`가 `List<A>` **하위** 타입이면 -> 공변
- `B[]`가 `A[]`의 **상위** 타입이고, `List<B>`가 `List<A>` **상위** 타입이면 -> 반공변
- `List<A>`와 `List<B>`가 서로 관계가 없을 때 -> 무공변 / 불공변


### 와일드카드
따라서 제네릭간의 형변환이 가능하려면 와일드카드 문법을 사용해야 한다. 와일드 카드 타입이란 어떤 타입이 제네릭 타입이 되더라고 상관 없다는 의미를 가지고 있다.

```java
List<? extends Object> list = new ArrayList<String>(); // Object 클래스나 Object 클래스의 하위 타입만 가능
List<? super String> list2 = new ArrayList<Object>(); // String 클래스나 String 클래스의 상위 타입만 가능
```

```java
public static void main(String[] args) {
    // 이제 가능
    List<?> objectsList = new ArrayList<String>();

    for(Object e: objectsList) {
        System.out.println(e);
    }
}
```

와일드카드 타입 또한 extends, super 키워드를 사용해 와일드카드 타입의 매개변수형을 제한할 수 있다.
- `<?>` : 제한 없음
- `<? extends 타입>` : 해당 타입이나 **하위** 타입만 가능 -> 상위 클레스 제한
- `<? super 타입>` : 해당 타입이나 **상위** 타입만 가능 -> 하위 클레스 제한

####와일드카드에 대한 오해
```java
public class MyGeneric<?> {
    // 불가능
}
```
제네릭의 와일드카드 문법은 클래스 설계에 사용할 수 없다. 
```java
public static void main(String[] args) {
    MyGeneric<? extends Number> myGeneric = new MyGeneric<>(); // 가능
}
```
와일드카드 문법은 이미 만들어진 제네릭 클래스나 메소드를 사용할때 이용한다.

### 타입 소거
제네릭 타입은 컴파일이 완료된 후 없어진다. 이는 제네릭 문법이 도입되기 전에 존재하던 자바 버전의 코드를 호환하기 위함이다. 컴파일러는 클래스나 메소드에 제네릭 타입 소거를 적용하고 필요하면 자동 형변환과 Bridge Method를 추가한다.

#### 타입 소거
제네릭 클래스로 예시를 들어보자.

- 타입 파라미터 `<T>`는 Object로 치환된다

    소거 전
    ```java
    public class MyGeneric<T> {
        T object;

        public T getObject() {
            return object;
        }

        public void setObject(T object) {
            this.object = object;
        }
    }
    ```

    소거 후
    ```java
    public class MyGeneric {
        Object object;

        public Object getObject() {
            return object;
        }

        public void setObject(Object object) {
            this.object = object;
        }
    }
    ```

- Bounded 타입 파라미터의 `<T extends Class>`는 Class로 치환된다

    소거 전
    ```java
    public class MyGeneric<T extends Class> {
        T object;

        public T getObject() {
            return object;
        }

        public void setObject(T object) {
            this.object = object;
        }
    }
    ```

    소거 후
    ```java
    public class MyGeneric {
        Class object;

        public Class getObject() {
            return object;
        }

        public void setObject(Class object) {
            this.object = object;
        }
    }
    ```

#### 자동 형변환
제네릭 타입을 소거한 후 타입이 일치하지 않는 부분은 컴파일러가 자동적으로 형변환을 추가한다.

#### Bridge Method 생성
제네릭 타입을 확장한 클래스에 대해서 타입에 대한 다형성을 유지하기 위해 컴파일러는 Bridge Method를 생성한다.
```java
public class MyGeneric<T> {
    T object;

    public T getObject() {
        return object;
    }

    public void setObject(T object) {
        this.object = object;
    }
}
```

```java
public class StringGeneric extends MyGeneric<String> {
    @Override
    public void setObject(String str) {
        this.object = str;
    }
}
```

타입 소거가 일어나면 MyGeneric 클래스 내부의 타입 파라미터 T는 전부 Object 클래스로 치환된다.
```java
public class MyGeneric {
    Object object;

    public Object getObject() {
        return object;
    }

    public void setObject(Object object) {
        this.object = object;
    }
}
```

이렇게 되면 자식 클래스가 사전에 Override한 setObject 메소드의 시그니처가 부모 클래스의 sebObject 메소드와 불일치하게 된다. 따라서 이런 간극을 없애기 위해 컴파일러는 자식 클래스에 bridge method를 생성한다.

```java
public class StringGeneric extends MyGeneric {

    public void setObject(String str) {
        this.object = str;
    }

    // bridge method 생성
    public void setObject(Object str) {
        this.setObject((String) str);
    }
}
```