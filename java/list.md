# List
List는 순서가 유지되는 컬렉션을 구현하는데 사용한다. 배열과 마찬가지로 index를 사용해 데이터에 접근을 한다.

자바에서 List 인터페이스를 구현한 대표적인 구현체의 특징은 다음과 같다
- ArrayList : 데이터간 공백을 허용하지 않고, 동적으로 배열의 크기가 변한다
- LinkedList : 노드끼리 서로 연결하여 리스트처럼 만든 컬렉션이다
- Vector : Thread safe하다
- Stack : 나중에 들어온 값을 먼저 처리하는 LIFO (후입선출)의 구조를 가진다. Thread safe하다

## ArrayList
ArrayList는 배열의 상위호환 버전으로써 자동으로 확장이 가능한 배열이다. 자바 컬렉션의 List 인터페이스의 구현체중 하나다. ArrayList는 내부적으로 Object 타입의 배열을 사용한다. 내부 코드를 살펴보면 사용하는 배열은 `elementData`라는 변수명으로 다음과 같이 선언되어 있다.
    
```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    // 나머지 생략

    /**
        * The array buffer into which the elements of the ArrayList are stored.
        * The capacity of the ArrayList is the length of this array buffer. Any
        * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
        * will be expanded to DEFAULT_CAPACITY when the first element is added.
        */
    transient Object[] elementData; // non-private to simplify nested class access
```

- ArrayList는 배열과는 반대로 중간에 빈 데이터가 존재하지 않는 연속적인 데이터의 리스트 구조를 가진다. 따라서 삽입/삭제 연산이 일어날때마다 빈 배열을 채워주기 위해 데이터들을 이동하는 작업을 수행하므로 삽입/삭제 동작이 상대적으로 느리다는 단점이 있다. 

- 데이터의 적재량의 따라서 배열의 크기를 가변적으로 늘리거나 줄일 수 있다.

- 내부 배열이 꽉 찰때 마다 더 큰 용량의 배열을 생성하여 기존 배열을 복사한다. 이 과정에서 지연이 발생하게 된다.
    - 상세한 내용은 별도로 작성


## ArrayList의 생성자와 주요 메소드
### 생성자
ArrayList가 제공하는 생성자는 총 3개로써 종류는 다음과 같다.

| 생성자 | 설명 |
| --- | --- |
| ArrayList() | 내부 배열의 용량 (capacity)이 10인 ArrayList 객체를 생성한다. 정확히 말하면 생성된 직후 내부 배열의 용량은 0이었다가 데이터가 추가되면 내부 배열의 사이즈를 10으로 할당한다 |
| ArrayList(Collection<? extends E> c) | 인자로 들어온 컬렉션을 내부에 저장한 ArrayList 객체를 생성한다 |
| ArrayList(int initialCapacity) | 내부 배열이 initialCapacity만큼의 저장공간을 가진 ArrayList 객체를 생성한다 |

### ArrayList에 데이터를 담을 수 있는 다양한 메소드
ArrayList에 데이터를 추가하려면 다음과 같은 메소드를 사용할 수 있다.
| 메소드 | 설명 |
| --- | --- |
| boolean add(E e) | 배열의 가장 마지막 인덱스에 요소를 추가한다. 추가가 성공하면 true를 반환한다 |
| void add(int index, E element) | 매개변수의 index 위치에 있는 공간에 요소를 추가한다 |
| boolean addAll(Collection<? extends E> c) | 배열의 가장 마지막 인덱스에 매개변수로 넘어온 컬렉션의 요소들을 배열에 추가한다 |
| addAll(int index, Collection<? extends E> c) | 매개 변수의 index 위치부터 매개 변수로 넘어온 컬렉션의 요소들을 배열에 추가한다 |

index를 지정해주는 삽입 메소드는 매개 변수로 넘어온 index가 현재 배열의 사이즈보다 크면 IndexOutOfBoundsException이 발생한다. 또한 index 기준으로 뒤에 있는 데이터들을 한칸씩 뒤로 옮긴 다음 해당 index에 새로운 요소를 삽입한다. 따라서 ArrayList의 사이즈가 크면 클수록 연산이 많아지기 때문에 비효율적이 된다.

### 삭제
ArrayList에 데이터를 삭제하려면 다음과 같은 메소드를 사용할 수 있다.
| 메소드 | 설명 |
| --- | --- |
| void clear() | 모든 데이터를 삭제한다 |
| E remove(int index) | 매개 변수로 넘어온 index에 해당하는 데이터를 삭제하고 반환한다 |
| boolean remove(Object o) | 매개 변수로 넘어온 객체 o와 동일한 첫번째 데이터를 삭제한다 |
| boolean removeAll(Collection<?> c) | 매개 변수로 넘어온 컬렉션 객체와 동일한 모든 데이터를 삭제한다 |


### 조회 관련 메소드
ArrayList 내부 데이터를 조회와 기타 ArrayList의 정보를 반환하는 메소드이다.
| 메소드 | 설명 |
| --- | --- |
| E get(int index) | 매개 변수로 넘어온 index에 해당하는 데이터를 반환한다 |
| int size() | ArrayList에 들어있는 객체의 수를 반환한다 |
| int indexOf(Object o) | 매개 변수로 넘어온 객체 o의 배열 인덱스를 반환한다 |
| int lastIndexOf(Object o) | 매개 변수로 넘어온 객체 o와 동일한 데이터의 배열 인덱스를 반환한다 |


