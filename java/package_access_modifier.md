# Package & Access Modifier

## Package
자바의 패키지란 폴더와 같은 개념으로 클래스들을 용도에 맞게 구분 짓는 방법이다. 패키지는 관련 있는 클래스를 그룹화하여 유지 보수를 용이하게 하고 클래스간의 충돌을 방지한다. 예를 들어 같은 이름을 가진 클래스들을 다른 패키지로 구분하면 정상적으로 자바 어플리케이션이 컴파일 된다. 마지막으로 패키지는 접근 제어를 제공하므로 특정 패키지에 속한 클래스들에 대한 접근을 다른 패키지에서 제어할 수 있다.

### 패키지 선언을 할 떄 꼭 지켜야 하는 제약사항

소스 코드 내부에서 패키지를 선언할때 주의사항은 다음과 같다.
- 소스의 가장 첫 줄에 있어야 한다
- 한 소스파일에 하나의 패키지 선언문만 있어야 한다
- 패키지 이름과 위치한 폴더 이름이 같아야 한다. 이름이 다를 경우 파일을 찾지 못해 컴파일이 되지 않는다

### 패키지 명명 규칙
패키지의 이름을 정할 떄 보통 도메인의 역순으로 패키지명을 명명하는 컨벤션을 따른다. 예를 들어 담성(damsung)이라는 회사에서 패키지를 만든 경우는 `com.damsung.<패키지명>`으로 명명을 한다. 기본으로 정해져 있는 규칙은 다음과 같다. 
| 패키지 시작 이름 | 내용 |
| --- | --- |
| java | 자바 기본 패키지 (Java 벤더에서 개발) |
| javax | 자바 확장 패키지 (Java 벤더에서 개발) |
| org | 비영리단체(오픈소스)의 패키지 |
| com | 영리단체(회사)의 패키지 |

패키지명은 대부분 소문자로 지정해준다. 또한 int, static과 같은 자바의 예약어를 사용하지 않는 규칙이 있다.


### import를 사용하여 다른 패키지에 접근하기

import 키워드를 사용하면 다른 패키지에서 사용하고 있는 클래스를 현재 파일에 가져올 수 있다.

먼저 com.study.sj.packages라는 패키지 경로에 Sub이라는 이름의 클래스를 작성해보자
```java
package com.study.sj.packages;

public class Sub {
}
```

Sub 클래스를 다른 패키지에 사용하려면 다음과 같이 import 구문을 사용하여 클래스를 사용한다는 것을 컴파일러에게 알려줘야 한다.
```java
package com.study.sj;

import com.study.sj.packages.Sub;

public class Java {
    public static void main(String[] args) {
        Sub sub = new Sub();

    }
}
```

위 예시 외에도 만약 com.study.sj.packages 패키지에서 import 하고 싶은 클래스의 수가 너무 많아 하나씩 지정해주기 어려운 경우에는 다음 처럼 패키지 내부의 전체 클래스를 import 해주는 구문을 사용할 수 있다.
```java
import com.study.sj.packages.*
```

### import static
다른 패키지에 있는 클래스 변수를 import static 구문을 사용해서 바로 가져올 수 있다. 예시로 Sub 클래스에서 static 변수와 메소드를 하나씩 구현해보자.

```java
package com.study.sj.packages;

public class Sub {
    public final static String CLASS_NAME = "Sub";

    public static void callMethodName() {
        System.out.println("callMethodName");
    }
}
```

두 static 변수와 메소드를 다른 패키지에서 바로 사용하고 싶을 때는 다음과 같이 해주면 된다.
```java
package com.study.sj;

import static com.study.sj.packages.Sub.CLASS_NAME;
import static com.study.sj.packages.Sub.callMethodName;

public class Java {
    public static void main(String[] args) {
        System.out.println(CLASS_NAME);
        callMethodName();
    }
}
```

main 메소드를 실행해보면 결과는 다음과 같다
```
> Task :Java.main()
Sub
callMethodName
```

## 접근 제어자 (Access Modifier)
자바가 제공하는 접근 제어자는 총 4개가 있으며 모두 변수, 메소드, 인스턴스 및 클래스에 사용이 가능하다. 종류는 다음과 같다.

**public :**  어디든 접근 가능 <br>
**protected :** 같은 패키지나 상속 관계의 자식 객체만 접근 가능 <br>
**default :** 동일 패키지에서 접근 가능. 패키지 접근 제어자 (package private) 라고 한다 <br>
**private :** 같은 클래스 내부에서만 접근 가능 <br>

접근 제어자를 사용하는 이유는 다음과 같다.

- 접근 제어자를 사용하면 외부에서 객체의 중요한 데이터나 외부에 굳이 노출할 필요가 없는 데이터에 접근하는 것을 방지할 수 있다 -> 캡슐화
- 클래스 내부 데이터를 감춤으로써 세부적인 구현을 숨기고 필요한 부분만 외부에 노출시킬 수 있다 -> 추상화

### 접근 제어자의 예시
**setter & getter**: 일반적으로 멤버 변수는 private으로 선언. 멤버 변수에 대한 접근은 public 메소드를 통해 접근한다. 대표적인 예시로 멤버 변수를 조회하는 getter와 멤버 변수에 값을 할당하는 setter가 있다. <br>
**private method** : 주로 클래스 내부에서 중복되는 코드를 관리하기 위해 사용한다 <br>
**private 생성자** : 외부에서 객체를 생성하는 것을 제한하기 위해 사용한다. ex) 싱글턴 객체, 빌더 패턴 적용 시 <br>