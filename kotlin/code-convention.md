# 객체 선언



### 객체 생성 비용

- 객체를 wrap 하면 3가지 비용이 발생합니다. 
- 객체는 더 많은 비용을 발생시킵니다. Int 는 4바이트, Integer 는 16 바이트에 레퍼런스까지 해서 더 많은 용량이 필요합니다.
- 요소가 캡슐화 되어있다면 접근에 추가적인 함수 호출이 필요합니다. 이러한 것들이 쌓이면 비용입니다.
- 객체는 생성되고 메모리를 할당해야 합니다. 메모리에 대한 레퍼런스도 만들어야 합니다. 비용입니다.



### 예제 소스

- 아래와 같이 기존 소스에서는 Empty() 객체를 계속해서 생성했다면 개선된 소스에서 Empty 를 선언함으로써 재사용 하고 있습니다.
- Empty 를 재사용하기 위해 LinkedList 불변성에 out covariant 를 선언해 서브 타입을 받을수 있게 해줬습니다.

```kotlin
//// 기존 소스
sealed class LinkedList<T>

class Node<T>(
    val head: T,
    val tail: LinkedList<T>
) : LinkedList<T>()

class Empty<T> : LinkedList<T>()

// 불필요한 Empty 를 만들어줘야 함 - 객체를 생성한다는건 불필요한 비용
val list1: LinkedList<Int> = Node(1, Node(2, Node(3, Empty())))


// 개선 소스
sealed class LinkedList<out T>  // 리스트는 불변성이며, covariant 로 받아 서브 타입을 허용해줍니다. 

class Node<T>(
    val head: T,
    val tail: LinkedList<T>
) : LinkedList<T>()

object Empty : LinkedList<Nothing>()	// Nothing 의 경우 모든 서브타입이 됩니다.

fun main() {
    val list1: LinkedList<Int> = Node(1, Node(2, Empty))
}
```



