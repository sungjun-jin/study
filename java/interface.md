# Interface

인터페이스는 클래스를 작성할 때 기본이 되는 틀을 제공하면서, 다른 클래스 사이에 중간 매개 역할을 담당하는 추상 클래스다.

인터페이스를 사용하면 구체적인 구현에 대해서는 잘 모르고 관심이 없는 사용자는 인터페이스라는 매개체를 통해 실제 구현체와 소통하게 된다.

자바에서는 클래스를 통한 다중 상속을 지원하지 않는다. 하지만 인터페이스를 통해 다중 상속을 할 수 있다.

정리하자면 인터페이스를 사용하는 이유는 다음과 같다.
- 개발자는 인터페이스의 규격에 맞춰 기능을 구현하는데 집중을 할 수 있다
- 개발자에 따른 메소드 이름과 매개 변수 선언의 격차를 줄일 수 있다
- 공통적인 인터페이스를 선언해 놓으면, 선언과 구현을 구분할 수 있다
- 다중 상속을 구현할 수 있다

## 인터페이스의 활용

회원 관련 기능을 담당하는 인터페이스를 구현해보자.
```java
public interface MemberManager {
    boolean addMember(MemberDto memberDto);
    boolean removeMember(String name, String phone);
    boolean updateMember(MemberDto memberDto);
}
```

인터페이스는 `public interface <인터페이스 명>`으로 선언한다. 내부는 메소드의 반환 타입, 메소드명, 매개변수만 선언해준다.
인터페이스의 내부 메소드의 접근 제한자는 기본적으로 public이다.

MemberManager 인터페이스를 사용하려면 실제 구현을 담당하는 클래스를 만들어야 한다. 구현체임을 명시하도록 뒤에 Impl이라는 키워드롤 붙혀 MemberManagerImpl 클래스는 만들어보자.
```java
public class MemberManagerImpl {
}
```

MemberMangerImpl 클래스에 인터페이스를 적용시켜보자.
```java
public class MemberManagerImpl implements MemberManager {
    @Override
    public boolean addMember(MemberDto memberDto) {
        return false;
    }

    @Override
    public boolean removeMember(String name, String phone) {
        return false;
    }

    @Override
    public boolean updateMember(MemberDto memberDto) {
        return false;
    }
}
```

`implements` 키워드를 사용해 인터페이스를 적용한다. 인터페이스를 적용하면 앞서 MemberManager에서 선언했던 3개의 메소드를 실제로 구현(오버라이딩)해야 한다. 그냥 두게 되면 컴파일 오류가 발생한다.

실제로 인터페이스를 main 메소드에서 활용해보자. 

인터페이스 자체로는 객체로 만들 수 없다. 인터페이스의 객체를 생성하려고 시도하면 다음과 같은 컴파일 에러가 발생한다.
```java
public static void main(String[] args) {
    MemberManager memberManager = new MemberManager();
}
```

실행 결과
```
error: MemberManager is abstract; cannot be instantiated
        MemberManager memberManager = new MemberManager();
```

인터페이스를 사용하려면 인터페이스 타입의 변수에 실제 구현체를 할당해줘야 한다.
```java
public static void main(String[] args) {
    MemberManager memberManager = new MemberManagerImpl();
    boolean result = memberManager.removeMember("Jon Jones", "010-2222-2222");
}
```

인터페이스 타입의 변수에 실제 구현체를 할당한다. MemberManager 인터페이스에 선언되어 있는 메소드들은 전부 MemberManagerImpl에 구현되어 있기 때문에. 위 예시처럼 removeMember 메소드를 호출하면 실제 MemberManagerImpl 클래스가 구현한 removeMember 메소드가 실행된다.

### 인터페이스의 다중 상속
자바는 클래스는 기본적으로 한개의 클래스만을 상속받을 수 있지만, 인터페이스를 활용하면 여러개의 클래스를 상속받을 수 있다.

위에 MemberManager 인터페이스 외에도 MemberHistory 인터페이스를 만들어보자.
```java
public interface MemberHistory {
    public boolean saveMemberHistory(MemberDto memberDto);
}
```

MemberHistory 인터페이스를 기존 MemberManagerImpl에 추가해보자.
```java
public class MemberManagerImpl implements MemberManager, MemberHistory{
    @Override
    public boolean addMember(MemberDto memberDto) {
        return false;
    }

    @Override
    public boolean removeMember(String name, String phone) {
        return false;
    }

    @Override
    public boolean updateMember(MemberDto memberDto) {
        return false;
    }

    // MemberHistory 인터페이스의 메소드
    @Override
    public boolean saveMemberHistory(MemberDto memberDto) {
        return false;
    }
}
```

기존 MemberManager 인터페이스 옆에 MemberHistory 인터페이스를 추가했다. 추가했기 때문에 MemberManagerImpl 클래스는 MemberHistory에 선언되어 있는 saveMemberHistory 메소드를 추가로 구현해줘야 한다.

```java
public static void main(String[] args) {
    MemberHistory memberHistory = new MemberManagerImpl();
    boolean result = memberHistory.saveMemberHistory(memberDto);
}
```

동일하게 MemberHistory 인터페이스 타입의 변수에 실제 구현체를 할당해서 실제 구현된 메소드를 호출할 수 있다.

## JDK 8 이후 추가된 기능

### default method
추상 메소드만 가능했던 기존 인터페이스에서 default 인터페이스는 실제 구현을 한 메소드이다. 만약 어떤 인터페이스를 외부에 노출해놓고 시간이 지난 후에 인터페이스에 새로운 메소드를 추가해야 할 일이 생긴다면, 추가된 메소드 때문에 기존에 인터페이스를 구현한 클래스에 문제가 생길 것이다. 이런 상황을 예방하기 위해 인터페이스에 실제 구현된 default 메소드를 사용할 수 있다. 기존에 인터페이스를 구현한 클래스들은 default 메소드를 상속받은 것 처럼 그대로 사용하거나 목적에 맞게 오버라이딩 해서 사용할 수 있다.

### static method

인터페이스 내부에서 static 메소드의 정의와 구현을 허용한다.