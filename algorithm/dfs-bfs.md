# 트리 속에 있는 데이터 찾기

저장에 특별한 규칙이 없는 트리가 있다고 가정했을때, 어떤 순서로 각 노드를 방문할 것인지에 대한 대표적인 방식은 **DFS(깊이 우선 탐색)과 BFS(너비 우선 탐색)이 있다**.


## DFS (깊이 우선 탐색)
한 우물부터 깊이 판다. 루트 노드부터 시작해서 트리의 최하단 노드부터 먼저 방문하는 방식이다. 

깊이 우선 탐색은 **스택을** 활용해 구현할 수 있다.

```java
public void searchDeptFirst(Node node) {
    Stack<Node> stack = new Stack<>();
    
    // 첫번째 노드 push
    stack.push(node);

    while (!stack.empty()) {
        Node next = stack.pop();

        System.out.println(next.data);

        for (Node child : next.children) {
            stack.push(child);
        }
    }
}
```

스택을 활용하기 때문에 아래와 같이 재귀함수를 호출하는 구조로 구현이 가능하다.

```java
public void searchDeptFirstRecursive(Node node) {
    if (node == null) {
        return;
    }

    System.out.println(node.data);

    for (Node child : node.children) {
        searchDeptFirstRecursive(child);
    }
}
```


## BFS (너비 우선 탐색)
똑같은 깊이의 이웃 노드를 우선 방문하는 방식이다. **최단 경로 찾기에 적합하다.**

너비 우선 탐색은 **큐를** 활용해 구현할 수 있다.

```java
public void searchBreadthFirst(Node node) {
    Queue<Node> queue = new LinkedList<>();

    // 첫번째 노드 enqueue
    queue.add(node);

    while (!queue.isEmpty()) {
        Node next = queue.remove();

        System.out.println(next.data);

        queue.addAll(next.children);
    }
}
```

## BFS vs DFS
|  | 깊이 우선 탐색(DFS) | 너비 우선 탐색(BFS) |
| --- | --- | --- |
| 탐색 순서 | 자식 노드부터 탐색 | 이웃한 노드 먼저 탐색 |
| 관련 자료구조  | 스택 | 큐 |
