# Abstract Class
추상 클래스는 일반 메소드와 구현이 없는 추상 메소드를 가지고 있는 미완성된 설계도이다. 추상 클래스를 상속받은 자식 클래스는 부모 클래스의 추상 메소드를 반드시 구현해야 한다. 주로 공통적인 기능을 하는 메소드는 추상 클래스에서 미리 구현해놓고, 추상 메소드들은 상속받은 클래스에 알맞게 재정의하여 사용한다.

또한 추상 클래스는 객체 생성이 불가능하므로 구현하고 있는 시스템에서 독자적인 객체 생성을 제한하고 싶을 때 사용하면 좋다. 

인터페이스와 다르게 일반 클래스처럼 인스턴스 변수와, 생성자, private 메소드를 가질 수 있다.

추상 클래스는 다음과 같이 작성할 수 있다.
```java
public abstract class MemberManagerAbstract {
    public abstract boolean addMember(MemberDto memberDto);
    
    public abstract boolean removeMember(String name, String phone);

    public abstract boolean updateMember(MemberDto memberDto);

    public void printLog(String data) {
        System.out.println("Data=" + data);
    }
}
```

`abstract` 키워드를 사용해 추상 클래스나 추상 메소드를 정의할 수 있다. 추상 클래스는 구현된 메소드를 가질 수 있다. 내부에 추상 메소드가 한개라도 있다면 클래스 옆에 abstract 키워드를 붙혀 추상 클래스로 만들어야 한다.


자식 클래스는 추상 클래스를 다음과 같이 상속받는다.
```java
public class MemberManagerImpl extends MemberManagerAbstract {
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

MemberManagerAbstract 클래스에 이미 구현된 메소드인 printLog를 제외한 나머지 추상 메소드들을 오버라이딩 하여 사용한다.

