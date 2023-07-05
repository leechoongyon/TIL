





# parameter default 값

- 아래와 같이 num parameter 가 Int = 1 로 설정된건 값이 없을 때, 1이 default. 입력받은 값이 존재하면 해당 값을 사용

```kotlin
fun test(num: Int = 1): List<String> {
 	println(num)
}


test(5) // print 5
test() // print 1

```



# Inline

- 인라인 함수를 사용하면 함수가 그대로 복사되어 사용되어집니다.
- 보통 jvm 기반 언어는 내부 컴파일러에서 코드를 해석해서 최적화를 하는데 인라인 함수는 말 그대로 아래 doSomething 을 실행시킵니다. 
  - 예를 들면, 보통 매개변수 전달, 스텍 생성, 복귀 주소 저장 등의 작업을 수행하는데 이런 작업이 생략된겁니다.
- 작은 함수에서 유용하며, 코드가 많고 큰 함수에서는 되도록이면 사용하지 않는 것이 좋습니다.

```kotlin
inline fun doSomething() {
    println("doSomething...")
}

fun main() {
    doSomething()
}
```





# 확장함수 



### String.readObject()

- kotlin 의 확장함수 입니다. String 클래스에 새로운 함수인 readObject 를 추가하는 것을 의미합니다.
- 아래 예시를 보면 String 에 readObject 라는 메소드인데, reified T 로 제너릭 타입이 return 값과 parameter 입니다.
- 제네릭 타입이 Int 일 때, string -> Int 로 변환해줍니다.

```kotlin
inline fun <reified T> String.readObject(): T {
    return when (T::class) {
        Int::class -> this.toInt() as T
        else -> throw IllegalArgumentException("Unsupported type")
    }
}

fun main() {
    val strInt = "100"
    val intValue: Int = strInt.readObject()
    println(intValue)  // 100
}

```



### fun Int.factorial(): Int

- Int 뒤에 . 을 붙여서 factorial() 확장함수를 만듭니다.



##### source

- 아래 소스는 Int 확장함수를 만들었습니다. factorial 를 통해 입력 값을 받고, product 확장함수를 통해 1 부터 this 까지 곱합니다.

```kotlin
fun Int.factorial(): Int = (1..this).product()
fun Iterable<Int>.product(): Int =
    fold(1) { acc, i -> acc * i }

fun main() {
    val number = 5
    println("result is ${number.factorial()}")
}
```









# In

```kotlin
(people in names) // names 에 people 이 있는지 확인하는 코드. java contains 랑 비슷
```



# List

- Kotlin 의 List interface 는 immuable list 를 의미합니다.

```kotlin
val list2: List<Int> = listOf(1,2,3) // 수정 X
var list2: List<Int> = listOf(1,2,3) // 수정 O
```



# 프로퍼티

- Kotlin 에서 프로퍼티는 클래스나 객체 내에 선언된 속성입니다.

```kotlin
class Person {
  var mutableProperty: Int = 10 // 수정 가능
}
```


