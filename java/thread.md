# Thread

## Thread 란
쓰레드는 프로세스안에서 실행되는 작업의 단위이다. 여러개의 작업을 동시에 수행하여 빠른 처리로 프로그램의 성능을 향상시키고자 고안되었다. 자바 프로세스가 점유하는 물리적인 메모리는 적어도 32 ~ 64MB 정도가 된다. 반면에 쓰레드는 1MB 이내의 메모리만 필요해서 "경량 프로세스"라고도 부른다.

## 자바에서 Thread를 생성하는 방법

자바에서 Thread를 생성하는 방법은 Runnable 인터페이스 구현과 Thread 클래스 상속이 있다.

### Runnable 인터페이스 구현
        
```java
public class MyRunnable implements Runnable{
    @Override
    public void run() {
        System.out.println("This is MyRunnable run() method");
    }
}
```
`java.lang` 패키지의 Runnable 인터페이스를 구현한다. run 메소드 내부에 작업에 실행할 코드를 작성 해준다.

```java
public static void main(String[] args) {
    MyRunnable runnable = new MyRunnable();
    Thread myRunnableThread = new Thread(runnable);

    myRunnableThread.start();
}
```
Thread 클래스의 생성자에 Runnable 객체를 매개변수로 Thread 객체를 생성한다.
Thread 객체의 start 메소드 호출하면 쓰레드가 실행된다.

### Thread 클래스 상속
        
```java
public class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("This is MyThread: run() method");
    }
}
```
java.lang 패키지의 Thread 클래스를 상속받고 run 메소드 내부에 작업에 실행할 코드를 오버라이딩 해준다.

```java
public static void main(String[] args) {
    MyThread myThread = new MyThread();
    myThread.start();
}
```
객체를 생성하고 start 메소드 호출한다.

### 정리
두 클래스 공통적으로 run 메소드 내부에서 쓰레드에서 실행될 코드를 작성해야 한다. 또 run 메소드에 코드를 작성해도 쓰레드를 시작하기 위해서는 start 메소드를 호출해야 한다. 

자바는 다중상속이 불가능 하므로 Thread를 상속한 클래스에서 다른 부모 클래스를 상속받아야 하는 상황이면 Runnable 인터페이스를 구현하면 된다.

## 쓰레드 예시

### 쓰레드는 서로 독립적으로 실행된다
main 메소드에서 위에 예시로 들었던 쓰레드들을 각각 5개씩 생성하고 실행해보자.
```java
public static void main(String[] args) {
    for(int i=0;i<5;i++) {
        MyThread myThread = new MyThread();
        MyRunnable myRunnable = new MyRunnable();

        myThread.start();
        new Thread(myRunnable).start();
    }

    System.out.println("Main Method Finished");
}
```

main 메소드를 실행시켜보면 결과는 다음과 같다.
```
This is MyThread: run() method
This is MyThread: run() method
This is MyRunnable run() method
This is MyRunnable run() method
This is MyThread: run() method
This is MyRunnable run() method
This is MyThread: run() method
This is MyRunnable run() method
This is MyThread: run() method
Main Method Finished
This is MyRunnable run() method
```

코드 상으로는 생성된 myThread, MyRunnable 객체가 for 루프를 돌면서 번갈아 가면서 run 메소드가 실행되도록 작성했지만 main 메소드를 반복 실행해보면 결과가 그때마다 다르다 심지어 메인 메소드가 종료되어도 마지막 남은 MyRunnable 스레드가 실행되는것도 확인할 수 있다. 

start 메소드를 호출하면 jvm은 하나의 쓰레드를 추가하고 run 메소드 내부에 구현되어 있는 코드를 실행한다. **각각의 스레드는 서로의 종료여부에 상관없이 독립적으로 실행된다**. 


### 모든 쓰레드가 종료되어야 JVM이 종료된다
무한 반복문을 돌면서 1초마다 현재 밀리초를 출력하는 클래스를 만들어보자. 이 클래스는 Thread 클래스를 상속받아 별도의 쓰레드로 동작한다.
```java
public class EndlessThread extends Thread{
    @Override
    public void run() {
        while(true) {
            try {
                System.out.println(System.currentTimeMillis());
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
    }
}
```
```java
public static void main(String[] args) {
    EndlessThread endlessThread = new EndlessThread();
    endlessThread.start();
}
```
```
> Task :Java.main()
1704618636724
1704618637725
1704618638726
1704618639729
.
.
.
```
main 메소드로 쓰레드를 생성해 실행시켜보면 강제로 자바 프로세스를 종료하지 않는 이상은 무한으로 1초마다 밀리초가 출력되는 것을 확인할 수 있다. 


예외로 데몬 쓰레드라는 것이 있다. 쓰레드가 실행되기전에 setDaemon 메소드를 통해 데몬 쓰레드 여부를 지정할 수 있다
```java
public static void main(String[] args) {
    EndlessThread endlessThread = new EndlessThread();
    endlessThread.setDaemon(true);
    endlessThread.start();
}
```

위 main 메소드를 실행해보면 아까와 다르게 jvm 프로세스가 바로 종료되는 모습을 확인 할 수 있다.

데몬 쓰레드는 자신을 제외한 모든 일반 쓰레드가 종료되면 자신의 작업이 끝나지 않아도 바로 종료된다. 이런 특성 때문에 데몬 쓰레드는 모니터링, 자동 저장과 같은 보조적인 역할을 하는 쓰레드에 사용된다.

## Thread의 속성을 확인하고 지정하는 주요 메소드들
| 메소드 | 설명 |
| --- | --- |
| void run() | 구현해야하는 메소드 |
| long getId() | 쓰레드의 고유 ID를 리턴한다. JVM에서 자동으로 생성해준다. |
| String getName() | 쓰레드의 이름을 리턴한다. |
| void setName(String name) | 쓰레드의 이름을 지정한다. |
| int getPriority() | 쓰레드의 우선 순위를 확인한다. |
| void setPriority(int newPriority) | 쓰레드의 우선 순위를 지정한다. |
| boolean isDaemon() | 쓰레드가 데몬인지 확인한다. |
| void setDaemon(boolean on) | 쓰레드를 데몬으로 설정할지 아닌지 설정한다. |
| StackTraceElement[] getStackTrace() | 쓰레드의 스택 정보를 확인한다. |
| Thread.State getState() | 쓰레드의 상태를 확인한다. |
| ThreadGroup getThreadGroup() | 쓰레드의 그룹을 확인한다. |

### Priority
쓰레드는 우선순위(Priority)의 개념이 있다. 쓰레드가 대기하는 상황에서 우선순위가 높으면 다른 쓰레드보다 더 먼저 실행될 수 있다. 

우선순위는 Thread 클래스 내부에 private int형 멤버 변수로 선언되어 있다. 위에서 살펴봤던 setPriority(int newPriority) 인자에 숫자를 넣어줄 수 있겠지만 자바에서 제공하는 우선순위 상수를 사용하는것을 권장한다.
- MAX_PRIORITY: 가장 높은 우선 순위이며 값은 10이다
- NORM_PRIORITY: 일반적인 우선 순위이며 값은 5다
- MIN_PRIORITY: 가장 낮은 우선 순위이며 값은 1이다

## 여러개의 쓰레드가 공유된 자원에 접근했을때 발생하는 문제
여러 개의 쓰레드가 공유된 자원에 접근하면 의도하지 않는 데이터가 발생하는 등의 문제가 생길 수 있다. 아래 예시를 확인해보자
```java
public class Calc {
    private int amount;

    public void plus(int value) {
        amount += value;
    }

    public int getAmount() {
        return amount;
    }
}
```
정수 value를 인자로 받아 값을 멤버 변수 amount에 더하는 plus라는 메소드를 가지고 있는 클래스이다. 

MyThread 쓰레드를 생성해서 run 메소드 내부에 calc 객체의 멤버 amount에 +1씩 10000번 더하는 코드를 작성해보자.  
```java
public class MyThread extends Thread {
    private Calc calc;

    public MyThread(Calc calc) {
        this.calc = calc;
    }

    @Override
    public void run() {
        for(int i=0;i<10000;i++) {
            calc.plus(1);
        }
    }
}
```

메인 메소드에 총 2개의 쓰레드를 생성해서 각각의 쓰레드가 종료될떄까지 기다린 후 calc 객체의 최종 amount를 출력해보자. 각각의 쓰레드가 10000번씩 1을 더했으므로 calc 객체의 amount는 20000이 되는 것을 예측할 수 있다. 
```java
public static void main(String[] args) {
    Calc calc = new Calc();

    MyThread thread1 = new MyThread(calc);
    MyThread thread2 = new MyThread(calc);

    thread1.start();
    thread2.start();

    try {
        // thread1,2가 종료될때까지 대기
        thread1.join();
        thread2.join();
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    }

    System.out.println("최종 값은 : " + calc.getAmount());
}
```

실행 결과
```
> Task :Java.main()
최종 값은 : 15907
```

실행 결과는 예측했던 20000의 값에 많이 벗어나는것을 확인할 수 있다. 심지어 메인 메소드를 실행할때마다 결과값은 다르다. 각각의 쓰레드가 calc 객체의 amount을 동시에 읽어들여서 Thread Unsafety가 발생하는 문제이다. 

## Synchronized
Thread Safe 하다는 것은 여러 쓰레드가 공유된 자원에 접근했을 때 아무런 문제가 없는 상황을 뜻한다. 자바는 멀티쓰레드 환경에서 동기화를 지원하기 위해 가장 기초적인 장치인 Intrinsic Lock을 사용한다. Synchronized 키워드를 사용해 특정 객체의 고유락을 통해 여러 쓰레드들을 동기화 할 수 있다.

synchronized 키워드는 두 가지 방법으로 사용할 수 있다

- method 자체를 synchronized 키워드로 선언하는 방법 → synchronized method
- 메소드 내의 특정 문장만 synchronized로 블록으로 감싸는 방법 -> synchronized block

### synchronized method

```java
public class Calc {
    private int amount;

    public synchronized void plus(int value) {
        amount += value;
    }

    // getter 생략
}
```
메소드를 synchronized로 선언하면 현재 메소드를 점유하고 있는 쓰레드를 제외한 나머지 쓰레드는 대기상태가 된다.

### synchronized block with 모니터 객체
```java
public class Calc {
    private int amount;

    public void plus(int value) {
        synchronized (this) {
            amount += value;
        }
    }

    // getter 생략
}
```

```java
public class Calc {
    private int amount;

    Object lock = new Object(); // 잠금 객체

    public void plus(int value) {
        synchronized (lock) {
            amount += value;
        }
    }

    // getter 생략
}

```
메소드의 일부만 synchronized 블록을 사용할 수 있다. 

Synchronized 블록을 사용할때는 synchronized 괄호안에 객체를 사용하여 블록 내의 코드를 하나의 쓰레드만 점유하도록 할 수 있다. 이 객체를 모니터 객체 (monitor object)라고 한다. this를 사용할 수 있고 별도의 모니터 객체를 생성해 사용할 수 있다. 일반적으로는 두번째 예시처럼 별도의 모니터 객체를 만들어 사용한다. 

모니터 객체는 여러 쓰레드가 동시에 객체로 접근하는 것을 막는 역할을 함과 동시에 Object에 제공하는 thread 관련 메소드를 synchronized 블록 내부에서 사용해 점유한 쓰레드에 대한 통제권을 가질 수 있다.

```java
public class Calc {
    private int amount;
    private int interest;

    Object plusLock = new Object();
    Object interestLock = new Object();
    
    public void getInterest() {
        synchronized (interestLock) {
            return this.interest
        }
    }

    public void plus(int value) {
        synchronized (plusLock) {
            amount += value;
        }
    }
}
```

모니터 객체는 여러개를 만들어 각각의 synchronized 블록에 사용할 수 있다. 여러 개의 synchronized 블록에 같은 모니터 객체를 사용하면 두 블록 모두 하나의 쓰레드에서만 접근 가능하게된다. 그러므로 각각의 블록에 별도의 모니터 객체를 사용하면 각 블록 당 하나씩 쓰레드가 접근할 수 있게 된다.

### Thread의 객체 참조
Synchronized 키워드가 붙은 메소드는 쓰레드별로 같은 객체를 참조했을때만 유효하다. 아래와 같이 같은 calc 객체를 참조하고 있는 thread1, thread2는 synchronized 키워드 효과를 내지만, 별도의 calc1을 참조하고 있는 thread3 객체는 다른 두 쓰레드에 대해서 synchronized 키워드의 효과가 적용되지 않는다.

```java
public static void main(String[] args) {
    Calc calc = new Calc();
    Calc calc2 = new Calc();

    MyThread thread1 = new MyThread(calc);
    MyThread thread2 = new MyThread(calc); // synchronized

    MyThread thread3 = new MyThread(calc2); // synchronized x
}
```

## 쓰레드를 통제하는 주요 메소드
쓰레드를 통제하는 주요 메소드는 다음과 같다.
| 메소드 | 설명 |
| --- | --- |
| Thread.state getState() | 쓰레드의 상태를 확인한다. |
| void join() | 수행 중인 쓰레드가 중지할 때까지 대기한다. |
| void join(long millis) | 매개변수에 지정된 시간만큼(1/1,000초) 대기한다. |
| void join(long millis, int nanos) | 첫 번째 매개변수로 넘어온 시간(1/1,000초) + 두 번째 매개변수로 넘어온 시간(1/1,000,000,000초)만큼 대기한다. |
| void interrupt() | InterruptedException을 발생시키면서 수행중인 쓰레드에 중지 요청을 한다. |

이 중에서도 getState()를 통해 쓰레드의 현재 상태를 가져올 수 있다. 쓰레드의 현재 상태는 다음과 같이 상수로 정의되어 있다. 

| 상태 | 의미 |
| --- | --- |
| NEW | 쓰레드 객체는 생성되었지만, 아직 시작하지는 않은 상태 |
| RUNNABLE | 쓰레드가 실행 중인 상태 |
| BLOCKED | 쓰레드가 실행 중지 상태이며, 모니터 락이 풀리기를 기다리는 상태 |
| WAITING | 쓰레드가 대기중인 상태 |
| TIMED_WAITING | 특정 시간만큼 쓰레드가 대기 중인 상태 |
| TERMINATED | 쓰레드가 종료된 상태 |

 ![heap](./img/thread_life_cycle.png)
 위 상태를 기반으로 쓰레드의 라이프 싸이클은 NEW -> RUNNABLE, BLOCKED, WAITING, TIMED_WAITING -> TERMINATED의 형태를 가진다.

## Object 클래스에 구현된 쓰레드와 관련있는 메소드들
Object 클래스에서는 객체를 처리하는 메소드 이외에 쓰레드를 처리하는 메소드들이 있다. 다음은 Object 클래스에서 쓰레드를 처리하기 위한 메소드들의 목록이다.
| 메소드 | 설명 |
| --- | --- |
| void wait() | 다른 쓰레드가 Object 객체에 대한 notify() 메소드나 notifyAll() 메소드를 호출할 때까지 현재 쓰레드가 대기하고 있도록 한다. |
| void wait(long timeout) | wait() 메소드와 동일한 기능을 제공하며, 매개변수에 지정한 시간만큼만 대기한다.즉, 매개변수 시간을 넘어섰을 때에는 현재 쓰레드는 다시 깨어난다. 시간은 밀리초로 1/1,000초 단위이다. |
| void wait(long timeout, int nanos) | wait() 메소드와 동일한 기능을 제공한다. 이 메소드는 밀리초+나노초 만큼만 대기한다. 뒤에 있는 나노초는 0~999,999 사이의 값만 지정할 수 있다. |
| void notify() | Object 객체의 모니터에 대기하고 있는 단일 쓰레드를 깨운다. |
| void notifyAll() | Object 객체의 모니터에 대기하고 있는 모든 쓰레드를 깨운다. |

### Thread Group
ThreadGroup이란 서로 관련된 쓰레드를 그룹으로 묶어서 다루기 위한 용도로 사용한다. 쓰레드는 반드시 하나의 쓰레드 그룹에 포함된다. ThreadGroup에서 제공하는 메소드들은 다음과 같다.

| 메소드 | 설명 |
| --- | --- |
| int activeCount() | 실행 중인 쓰레드의 개수를 리턴한다. (리턴값은 배열에 저장된 스레드의 개수) |
| int activeGroupCount() | 실행 중인 쓰레드 그룹의 개수를 리턴한다. |
| int enumerate(Thread[] list) | 현재 쓰레드 그룹에 있는 모든 쓰레드를 매개 변수로 넘어온 쓰레드 배열에 담는다. |
| int enumerate(Thread[] list, boolean recurse) | 현재 쓰레드 그룹에 있는 모든 쓰레드를 매개 변수로 넘어온 쓰레드 배열에 담는다. 두 번째 매개 변수가 true이면 하위에 있는 쓰레드 그룹에 있는 쓰레드 목록도 포함한다. |
| int enumerate(ThreadGroup[] list) | 현재 쓰레드 그룹에 있는 모든 쓰레드 그룹을 매개 변수로 넘어온 쓰레드 그룹 배열에 담는다. |
| int enumerate(ThreadGroup[] list, boolean recurse) | 현재 쓰레드 그룹에 있는 모든 쓰레드 그룹을 매개 변수로 넘어온 쓰레드 그룹 배열에 담는다. 두 번째 매개 변수가 true이면 하위에 있는 쓰레드 그룹 목록도 포함한다. |
| String getName() | 쓰레드 그룹의 이름을 리턴한다. |
| ThreadGroup getParent() | 부모 쓰레드 그룹을 리턴한다. |
| void list() | 쓰레드 그룹의 상세 정보를 출력한다. |
| void setDaemon(boolean daemon) | 지금 쓰레드 그룹에 속한 쓰레드들을 데몬으로 지정한다. |

## Thread Local
자바에서 제공하는 ThreadLocal 클래스는 쓰레드 내부에서 사용할 수 있는 고유한 저장소이다. 쓰레드 내부에서 공유되며 다른 쓰레드에서 참조할 수 없다.

ThreadLocal은 쓰레드마다 고유한 ThreadLocalMap 객체를 생성해 변수를 저장하고 조회한다.
```java
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        map.set(this, value);
    } else {
        createMap(t, value);
    }
}

public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}
```
사용법은 다음과 같다

ThreadLocal에 값 저장하기
```java
ThreadLocal<String> threadLocal = new ThreadLocal<>();
threadLocal.set("INFO");
```

ThreadLocal에서 값 가져오기
```java
String str = threadLocal.get();
```

ThreadLocal에서 주의할 점은 변수 사용이 완료되었으면 꼭 remove() 메소드를 호출해 변수를 삭제해야 한다. WAS와 같이 쓰레드풀을 사용하는 경우 사용이 완료된 쓰레드에 ThreadLocal 변수가 남아있을 여지가 있기 때문이다.