# Immutable Object

불변 객체란 객체가 생성된 이후에 내부 데이터를 바꿀 수 없는 객체를 의미한다. 불변 객체를 사용하면 객체에 대한 신뢰도가 높아지고 내부 데이터를 변경할 수 없기 때문에 멀티 쓰레드 환경에서 안전하게 객체를 사용할 수 있다.

Java의 대표적인 불변객체는 String, Integer, Long, Double 등이 있다.

## 불변객체를 만드는 방법
간단하게 serialNo 이라는 정수형 멤버 변수를 갖는 자동차(Car) 클래스를 예시로 들어보자.

```java
public class Car {
    private int serialNo;

    public Car(int serialNo) {
        this.serialNo = serialNo;
    }

    public int getSerialNo() {
        return serialNo;
    }
}
```

### setter를 제공하지 않는다
클래스에 setter를 제공할 경우 setter를 제공한 해당 멤버변수에 접근해 값을 수정할 수 있다. 
```java
public class Car {
    private int serialNo;

    // 생성자, getter 생략

    public void setSerialNo(int serialNo) {
        this.serialNo = serialNo;
    }
}
```

```java
public static void main(String[] args) {
    Car car = new Car(1234);
    System.out.println("Serial Number : " + car.getSerialNo());

    car.setSerialNo(5678);
    System.out.println("Serial Number : " + car.getSerialNo());
}
```

실행 결과
```
> Task :Java.main()
Serial Number : 1234
Serial Number : 5678
```

### 모든 멤버 변수를 private, final로 선언한다.
멤버 변수의 접근 제어자를 private으로 선언하면 클래스 외부에 접근이 불가능하다. 또한 멤버 변수에 final 키워드를 사용하면 초기화된 값 이후 수정이 불가능하다.

```java
public class Car {
    private final int serialNo = 1234;
    private final String name;

    public Car(String name) {
        this.name = name; // 생성자에서 초기화
    }
}
```

### 클래스를 final로 선언한다
불변 객체를 만들고자 하는 클래스의 상속을 허용하면 자식 클래스에 의해서 불변 상태가 깨질 여지가 있다. 따라서 클래스에 final 키워드를 사용해 상속이 불가능한 클래스로 만들어 준다.

### 멤버 변수에 참조 자료형이 있는 경우
불변 객체로 만들고자 하는 클래스에 멤버 변수에 참조 자료형의 멤버 변수가 있는 경우, 해당 멤버 변수의 불변 객체 여부를 확인해줘야 한다.

자동차(Car) 클래스 내부에 브랜드(Brand)라는 클래스 타입의 참조 자료형 멤버 변수를 선언해주자.

```java
public class Car {
    private final Brand brand;

    public Car(Brand brand) {
        this.brand = brand;
    }

    public Brand getBrand() {
        return brand;
    }
}
```

```java
public class Brand {
    private String name;

    public Brand(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```
Brand 클래스의 name 이라는 멤버 변수는 final도 아니고 setter도 제공되므로 불변 객체가 아니다. 이런 경우 아무리 Car 클래스에서 멤버 변수를 final로 선언해도 외부에서 충분히 참조형 멤버 변수의 참조를 가져와 수정할 수 있다.

```java
public static void main(String[] args) {
    Car car = new Car(new Brand("Hyundai"));

    System.out.println(car.getBrand().getName());

    Brand brand = car.getBrand();
    brand.setName("Kia");

    System.out.println(car.getBrand().getName());
}
```

실행 결과
```
> Task :Java.main()
Hyundai
Kia
```

#### 방어적 복사
참조 자료형 멤버 변수의 불변성을 보장하기 위한 방법 중 하나이다. 크게 두 가지 방법이 있다.

<strong>1. 참조 자료형 변수를 반환 할 때 새로운 객체를 만들어 반환</strong>

위 예시처럼 참조형 멤버 변수에 상태를 바꿀 수 있는 setter 메소드가 있을 경우 사용하면 좋은 방법이다.

```java
public class Car {
    private final Brand brand;

    // 생성자 생략

    public Brand getBrand() {
        return new Brand(this.brand.getName());
    }
}
```
참조 자료형을 반환하는 getBrand 메소드에 방어적 복사를 적용하고 프로그램을 실행을 해보면 결과는 다음과 같다.
```java
public static void main(String[] args) {
    Car car = new Car(new Brand("Hyundai"));

    System.out.println(car.getBrand().getName());

    Brand brand = car.getBrand(); // 새로운 주소의 Brand 객체
    brand.setName("Kia");

    System.out.println(car.getBrand().getName());
}
```

실행 결과
```
> Task :Java.main()
Hyundai
Hyundai
```

<strong>2. 참조 자료형 변수를 초기화 할 때 새로운 객체를 만들어 초기화 </strong>
객체를 생성할때 내부 참조형 멤버 변수를 외부에서 가지고 있다면 사용 할 수 있다.

```java
public static void main(String[] args) {
    Brand brand = new Brand("Hyundai"); // 초기화에 필요한 내부 멤버 변수를 외부에서 가지고 있음
    Car car = new Car(brand);

    System.out.println(car.getBrand().getName());
    brand.setName("Kia");

    System.out.println(car.getBrand().getName());
}
```
위와 같은 경우 생성자를 통해 내부 참조형 멤버 변수를 초기화 할때 새로운 객체를 만들어 저장하면 외부 참조가 끊기게 된다.

```java
public class Car {
    private final Brand brand;

    public Car(Brand brand) {
        this.brand = new Brand(brand.getName());
    }

    public Brand getBrand() {
        return this.brand;
    }
}
```

실행 결과
```
> Task :Java.main()
Hyundai
Hyundai
```

#### Collection.unmodifiableList
내부 멤버 변수에 리스트가 있는 경우 사용할 수 있는 방법이다. 리스트를 반환하는 getter 메소드를 작성할 때 
`Collection.unmodifiableList`로 감싸서 반환하면 외부에서 리스트 내부 요소에 대한 수정을 방지할 수 있다. `Collection.unmodifiableList`로 리턴되는 리스트는 add, remove, set, addAll과 같은 리스트의 내부 요소를 수정하는 메소드를 호출했을 경우 UnsupportedOperationException 예외를 발생시킨다.

예를 들어 멤버 변수에 Owner 목록을 가지는 자동차 클래스가 있다. 리스트 타입의 owners 멤버 변수를 반환하는 getter는 Collection.unmodifiableList로 감싸서 반환된다.
```java
public class Car {
    private final List<Owner> owners;

    public Car(List<Owner> owners) {
        this.owners = owners;
    }

    public List<Owner> getOwners() {
        return Collections.unmodifiableList(this.owners);
    }
}
```

```java
public class Owner {
    private final String name;

    public Owner(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }
}
```

```java
public static void main(String[] args) {
    List<Owner> owners = new ArrayList<>();

    owners.add(new Owner("John"));
    owners.add(new Owner("Lee"));

    Car car = new Car(owners);

    car.getOwners().add(new Owner("son"));
}
```
호출하는 외부 메소드에서 반환된 owner 리스트를 수정하게 될 경우 다음과 같은 예외가 발생한다.

```
Exception in thread "main" java.lang.UnsupportedOperationException
	at java.base/java.util.Collections$UnmodifiableCollection.add
    ...생략
```