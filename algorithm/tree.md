# 트리 (Tree)

트리란 ***계층적** 데이터를 표현한 자료구조이다. 트리는 다음과 같은 용도에서 사용된다.

- 계층적 데이터를 표현
    - HTML이나 XML의 문서 개체 모델 (DOM)을 표현
    - JSON이나 YAML처리 시 계층 관계를 표현
    - 프로그래밍 언어를 표현하는 추상 구문 트리
    - 인간 언어를 표현하는 파싱 트리

- 검색 트리를 통한 효율적인 검색 알고리즘 구현

## 트리 관련 용어

**노드 (node) :** 실제로 저장하는 데이터를 나타낸다. 

```mermaid
graph TD
B{부모}
A{부모} --> C{자식}
B --> D{자식}
B --> E{자식}
```
**부모 - 자식 :** 연결된 노드들 간의 상대적인 관계를 나타낸다
- 자식은 없을 수도 있고, 많이 있을 수도 있다
- 부모 노드는 무조건 **한개**이다
- 조부모, 형제자매 등의 개념도 있다

```mermaid
graph TD
A[Root] --> B{Node B}
A --> C{Node C}
B --> D{리프 노드}
B --> E{리프 노드}
C --> F{리프 노드}
```
**리프 (leaf) 노드 :** 가장 마지막에 위치한 데이터들을 나타낸다.
- 더 이상 자식 노드를 가지지 않음

**루트(root) 노드 :** 최상위에 위치한 데이터
- 맨 위에 있는 시작 노드를 의미한다
- 모든 노드와 직간접적으로 연결된다

**깊이(depth) :** 해당 노드에서 루트 노드로 가는 경로의 길이를 의미한다

**높이 (height) :** 해당 노드에서 리프 노드로 가는 최대 길이를 의미한다

**하위 트리 (subtree) :** 해당 노드 아래의 모든 것을 포함하는 트리

## 트리의 저장법 예시

범용적인 트리의 모습을 표현하는 알반적인 코드는 다음과 같다. 부모는 n개의 자식 노드를 가질 수 있으므로 부모 클래스에서 자식 클래스를 컬렉션과 함께 참조하는것이 직관적이다. 아래 예시를 확인해보자

- 자식이 여러개인 트리의 저장법
```java
public class Node {
    public int data;
    public ArrayList<Node> children;
}
```

- 자식이 최대 둘인 트리 (이진트리)의 저장법

```java
private class Node {
    public int data;
    public Node left;
    public Node right;
}
```

- 자식이 하나인 트리의 저장법. (연결 리스트와 비슷한 코드다)

```java
private class Node {
    public int data;
    public Node child;
}
```

### 이진 탐색 트리 (Binary Search Tree)
<img src="./img/bst.png" width="500" height="450"/>

이진 탐색 트리는 자식이 최대 2개를 가지는 조건의 이진 트리에서 아래와 같은 조건을 추가한 자료구조이다. 

- 왼쪽 자식은 언제나 부모보다 작다
- 오른쪽 자식은 언제나 부모보다 크다
- 부모 노드와 같은 값을 가지는 노드는 적당히 위 조건을 수정하여 저장한다.
    - ex) 왼쪽 자식은 언제나 부모보다 작거나 같다 or 오른쪽 자식은 언제나 부모보다 크거나 같다

위 조건으로 인해 이진 탐색 트리는 정렬된 자료구조의 특징을 가진다. 루트 노드를 기준으로 루트 노드보다 작은 값은 왼쪽에, 큰 값은 오른쪽에 포진되어 있다.


#### BST 검색
이진 탐색 트리의 검색 과정은 각 노드마다 두 하위트리로 이분뒤어 하위 트리로 내려갈때마다 검색 공간이 절반씩 줄어든다. 따라서 시간 복잡도는 O(logn)이다. 하지만 트리가 한쪽으로 편향된 구조를 가지는 경우 최악의 시간 복잡도는 O(n)이다.

검색 코드는 다음과 같이 재귀적으로 작성할 수 있다.

```java
public static Node getNodeOrNull(Node startNode, int data) {
    if(startNode == null) {
        return null;
    }
    
    // 검색하려는 값이 현재 노드보다 크면 오른쪽 노드 검색
    if(data > startNode.data) {
        return getNodeOrNull(startNode.right, data);
    }
    
    // 검색하려는 값이 현재 노드보다 작으면 왼쪽 노드 검색
    if(data < startNode.data) {
        return getNodeOrNull(startNode.left, data);
    }

    if(data == startNode.data) {
        return startNode;
    }
}
```

#### BST 삽입
이진 탐색 트리의 삽입 과정은 다음과 같다.
1. 새로운 노드를 받아줄 수 있는 부모 노드를 찾는다
    - 새로운 노드가 부모 노드보다 큰 값을 가질 때, 오른쪽 자식 노드가 비어있으면 삽입 가능
    - 새로운 노드가 부모 노드보다 작은 값을 가질 때, 왼쪽 자식 노드가 비어있으면 삽입 가능

2. 받아줄 수 없는 부모 노드가 없다면 자식 노드로 계속 내려가 받아줄 수 있는 노드를 찾는다.

코드로 표현하자면 다음과 같다.

```java
public static void add(Node startNode, int data) {
    if (data >= startNode.data) {
        if (startNode.right == null) {
            startNode.right = new Node(data);;
        } else {
            add(startNode.right, data);
        }
    } else {
        if (startNode.left == null) {
            startNode.left = new Node(data);;
        } else {
            add(startNode.left, data);
        }
    }
}
```

#### BST 삭제
이진 탐색 트리의 삭제 과정은 다음과 같다.
1. 삭제 대상 노드를 찾는다
2. 대상 노드와 바로 전 값을 가진 노드를 찾는다
    - 왼쪽 하위 노드의 가장 오른쪽 리프 노드
3. 두 노드를 교환한다
4. 리프 노드로 교환된 삭제 대상 노드를 null로 바꾼다

## 트리의 순회 (DFS)
트리는 각 노드의 방문 순서를 정하는 대표적인 3가지 순회법이 있다. **부모 노드를 어느 순서에 먼저 방문할것인지에 대해 다음과 같이 나뉜다.**

### 전위(pre-order) 순회
```java
public void traversePreOrder(Node node) {

    if(node == null) {
        return;
    }
    System.out.println(node.data);
    traversePreOrder(node.left);
    traversePreOrder(node.right);
}
```
1. 현재 노드
2. 왼쪽 하위 트리
3. 오른쪽 하위 트리

### 중위(in-order) 순회
```java
public void traverseInOrder(Node node) {

    if(node == null) {
        return;
    }

    traverseInOrder(node.left);
    System.out.println(node.data);
    traverseInOrder(node.right);
}
```
1. 왼쪽 하위 트리
2. 현재 노드
3. 오른쪽 하위 트리

### 후위(post-order) 순회
```java
public void traversePostOrder(Node node) {

    if(node == null) {
        return;
    }

    traversePostOrder(node.left);
    traversePostOrder(node.right);
    System.out.println(node.data);
}
```
1. 왼쪽 하위 트리
2. 오른쪽 하위 트리
3. 현재 노드

## 레드 블랙 트리 (Red Black Tree)
각 노드가 레드 혹은 블랙이 된다. 이 특성을 사용해 삽입과 삭제 연산 발생 시 스스로 균형을 잡는 (self balancing) 트리 구조를 유지할 수 있다는 장점이 있다.  

### 레드 블랙 트리의 특성
- 노드는 레드 혹은 블랙이다
- 루트 노드는 블랙이다
- 모든 리프 노드(NIL)은 블랙이다. Null Pointer가 모두 리프 노드이다
- 레드 노드의 자식은 언제나 블랙이다
- 어떤 노드와 리프 사이에 있는 블랙 노드 수는 동일하다
