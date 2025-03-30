# Singleton
어떤 클래스에서 만들 수 있는 인스턴스 수를 하나로 제한하는 페턴. 주로 리소스를 많이 차지하는 객체를 메모리 절약을 위해 기존에 한번만 생성된 인스턴스를 가져와 다시 활용하는 기법이다.

싱글턴 패턴을 충족하기 위해서는 다음과 같은 조건을 충족해야 한다.
- 프로그램 실행 중에 최대 하나만 있어야 한다
- 싱글턴 객체에 전역적으로 접근이 가능해야 한다

## 싱글톤 구현 기법

### Eager Intialization
싱글톤 클래스의 static final 변수를 두고 객체를 미리 생성해두는는 방법이다. 
```java
public class EagerInit {
    private static final EagerInit INSTANCE = new EagerInit();

    private EagerInit() {}

    public static EagerInit getInstance() {
        return INSTANCE;
    }
}
```

#### 장점
- 클래스 변수가 static final이므로 수정의 여지가 없다
#### 단점
- 싱글톤 객체를 생성하는 시점에 발생할 수 있는 예외를 처리할 방법이 없다
- getInstance 메소드를 호출하지 않아도 클래스 로딩 시점에 싱글톤 객체가 생성되기 때문에 리소스의 낭비가 발생할 수 있다

### Static Block Initialization
Static 블록 내부에 객체를 초기화 하는 방식이다.
```java
public class StaticBlockInit {
    private static StaticBlockInit INSTANCE;

    private StaticBlockInit() {}

    static {
        try {
            INSTANCE = new StaticBlockInit();
        } catch (Exception e) {
            throw new RuntimeException("Singleton 초기화 실패");
        }
    }

    public static StaticBlockInit getInstance() {
        return INSTANCE;
    }
}
```
#### 장점
- 싱글톤 객체를 생성하는 시점에 발생할 수 있는 예외를 처리할 수 있다는 점에서 Eager Initialization 방식보다 개선되었다고 할 수 있다

#### 단점
- 여전히 getInstance 메소드를 호출하지 않아도 클래스 로딩 시점에 싱글톤 객체가 미리 생성되기 때문에 리소스의 낭비가 발생할 수 있다

### Lazy Initialization
싱글톤 예시로 가장 많이 나오는 방식이며, 싱글톤 객체를 가져오는 메소드를 호출했을때 싱글톤 객체의 null 여부에 따라서 싱글톤 객체를 생성하여 반환하는 방식이다.

```java
public class LazyInit {
    private static LazyInit instance;

    private LazyInit() {}

    public static LazyInit getInstance() {
        if(instance == null) {
            instance = new LazyInit();
        }

        return instance;
    }
}
```
#### 장점
- 싱글톤 객체를 실제로 사용할 시점에 getInstance 메소드를 호출하여 싱글톤 객체를 생성한다는 측면에서 Eager Initialization과 Static Block Initialization 방식보다는 메모리 관리가 효율적이라는 점이 있다

#### 단점
- 여러 쓰레드가 동시에 접근했을때 여러개의 싱글톤 객체가 생성될 수 있다는 치명적인 단점이 존재한다

아래 예시를 들어 LazyInit 클래스에 getInstance 메소드를 호출하는 3개의 쓰레드를 실행시켰을때 반환되는 싱글턴 객체를 출력해보자.

```java
public class TestThread extends Thread {
    @Override
    public void run() {
        LazyInit singleton = LazyInit.getInstance();
        System.out.println(singleton); // 싱글톤 객체 정보 출력
    }
}
```

```java
public static void main(String[] args) {
    TestThread t1 = new TestThread();
    TestThread t2 = new TestThread();
    TestThread t3 = new TestThread();

    t1.start();
    t2.start();
    t3.start();
}
```

실행 결과 맨 아래 두번째 싱글턴 객체가 생성되었다.
```
> Task :Java.main()
com.study.sj.singleton.LazyInit@2a428578
com.study.sj.singleton.LazyInit@2a428578
com.study.sj.singleton.LazyInit@37470dd0 --> 두번째 싱글턴 객체
```

### Thread Safe Initialization
Lazy Initialization 방식에서 발생한 thread unsafety 문제를 해결하기 위해 싱글톤 객체를 생성하고 반환하는 메소드를 synchronized 메소드로 수정한 방식이다.

```java
public class ThreadSafeInit {
    private static ThreadSafeInit instance;

    private ThreadSafeInit() {}

    public static synchronized ThreadSafeInit getInstance() {
        if(instance == null) {
            instance = new ThreadSafeInit();
        }

        return instance;
    }
}
```

위와 같이 수정했을 경우 다시 여러 쓰레드에서 싱글턴 객체를 가져오는 메소드를 테스트 해보자.

```java
public class TestThread extends Thread {
    @Override
    public void run() {
        ThreadSafeInit singleton = ThreadSafeInit.getInstance();
        System.out.println(singleton);
    }
}
```

```java
public static void main(String[] args) {
    TestThread t1 = new TestThread();
    TestThread t2 = new TestThread();
    TestThread t3 = new TestThread();

    t1.start();
    t2.start();
    t3.start();
}
```

실행 결과 최초로 생성된 싱글톤 객체가 일관되게 반횐되는 모습을 확인할 수 있다.
```
com.study.sj.singleton.ThreadSafeInit@37470dd0
com.study.sj.singleton.ThreadSafeInit@37470dd0
com.study.sj.singleton.ThreadSafeInit@37470dd0
```
#### 장점
- 여러개의 쓰레드가 접근했을때 여러개의 싱글톤 객체가 생기는 문제를 해결했다.

#### 단점
- getInstance 메소드의 호출이 잦은 어플리케이션은 synchronized 메소드로 인해 대기 쓰레드가 발생하여 성능이 떨어질 수 있다

### Double Checked Locking
싱글톤 객체의 null 체크 구문이 이중으로 되어있어 Double Checked Locking이라고 부른다. 밖에서 하는 체크는 이미 객체가 존재할 경우 빠르게 반환하기 위한 목적이며 안에서 하는 체크는 객체가 최초로 생성할때 단 한개의 인스턴스만 생성하도록 보장하기 위함이다.

Thread Safe Initialization 방식에서 synchronized 블록을 최초로 객체를 생성하는 시점에만 적용해 임계 구역을 축소하여 성능을 개선시켰다.

`instance == null`을 확인하는 구문이 synchronized 블록 밖에 있기 때문에 instance 변수에 volatile 키워드를 사용해 메인 메모리에서 변수를 동기화하여 캐시와 메인 메모리의 데이터가 불일치하는 경우를 예방한다.

```java
public class DoubleCheckLockInit {
    private static volatile DoubleCheckLockInit instance;

    private DoubleCheckLockInit() {}

    public static DoubleCheckLockInit getInstance() {
        if (instance == null) {
            synchronized (DoubleCheckLockInit.class) {
                if (instance == null) {
                    instance = new DoubleCheckLockInit();
                }
            }
        }

        return instance;
    }
}
```
#### 장점
- synchronized 구간을 최소화해 성능을 개선했다

#### 단점
- volatile 키워드가 변수에 필요하지만 volatile은 자바 1.5버전 이후에 등장하여 하위 버전과 호환이 되지 않는다.

### Bill Pugh Solution (LazyHolder) 
클래스안에 정적 멤버 클래스(Holder)를 두어 싱글톤 객체를 생성하는 방법이다. 가장 일반적으로 사용하는 방식이다.
```java
public class LazyHolder {
    private LazyHolder() {}

    private static class Holder {
        private static final LazyHolder INSTANCE = new LazyHolder();
    }

    public static LazyHolder getInstance() {
        return Holder.INSTANCE;
    }
}
```

#### 장점
-  외부 클래스가 참조되어도 내부 클래스는 메모리에 로딩되지 않는다
-  외부 클래스의 getInstance 메소드를 호출할때 내부 클래스의 INSTANCE 변수가 참조되는 시점에 클래스가 로딩되어 싱글톤 객체의 lazy loading이 가능하다
-  클래스 로딩은 단 한번만 일어난다는 특징을 활용해 싱글톤 객체를 보장한다

### Enum
```java
public enum EnumSingleton {
    INSTANCE;

    public static EnumSingleton getInstance() {
        return INSTANCE;
    }
}
```

#### 장점
- 컴파일러에 의해서 한번 실행되는 enum 클래스 생성자의 특성을 이용해 싱글톤 패턴을 구현한 기법이다. 따라서 thread safe하다.
#### 단점
- `java.lang.Enum` 클래스 외에 상속을 허용하지 않는다
- 싱글톤 객체를 사용하지 않아도 메모리에 미리 생성되기 때문에 리소스의 낭비가 발생할 수 있다
- Enum은 기본적으로 상수 집합을 정의하는데 사용되기 때문에, 예외 처리를 위한 try-catch 블록과 함게 사용될 수 없다. 


## 클래스를 사용하는 싱글턴 패턴의 문제점
위 enum 방식을 제외하고 클래스를 사용하는 방식으로 싱글턴 패턴을 구현했을때 아래와 같은 문제점이 발생할 수 있다.

- 리플렉션에 의해 여러개의 객체가 생성될 수 있다.
    ```java
    public static void main(String[] args) {
        try {
            // LazyHolder 클래스의 생성자를 가져온다
            Constructor<LazyHolder> constructor = LazyHolder.class.getDeclaredConstructor();

            // 생성자를 외부에서 접근할 수 있게 수정
            constructor.setAccessible(true); 

            LazyHolder lazyHolder = constructor.newInstance();
            LazyHolder lazyHolder1 = constructor.newInstance();
            LazyHolder lazyHolder2 = constructor.newInstance();

        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
    ```

- 역직렬화 과정에서 여러개의 객체가 생성될 수 있다. 싱글톤 클래스가 Serializalbe 인터페이스를 구현한 경우 해당 싱글톤 클래스를 역직렬화하면 인스턴스가 생성된다
    ```java
    public static void main(String[] args) {
        String fileName = "LazyHolder.ser";

        LazyHolder lazyHolder = LazyHolder.getInstance();

        try {
            // 직렬화
            ObjectOutputStream out = new ObjectOutputStream(new BufferedOutputStream(new FileOutputStream(fileName)));
            out.writeObject(lazyHolder);
            out.close();

            ObjectInputStream in = new ObjectInputStream(new BufferedInputStream(new FileInputStream(fileName)));
            LazyHolder lazyHolder1 = (LazyHolder) in.readObject();
            in.close();

            System.out.println(lazyHolder == lazyHolder1); // false

        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
    ```
    - 이를 방지하기 위해 아래와 같이 기존 싱글톤 인스턴스를 반환하는 방식으로 readResolve 메소드를 정의하여 해결한다
        ```java
        public class LazyHolder implements Serializable {
            private LazyHolder() {}

            private static class Holder {
                private static final LazyHolder INSTANCE = new LazyHolder();
            }

            public static LazyHolder getInstance() {
                return Holder.INSTANCE;
            }

            public Object readResolve() {
                return Holder.INSTANCE;
            }
        }
        ```