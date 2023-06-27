





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



# `<reified T>`

- 코틀린 제네릭 타입 매개변수에 사용됩니다.



```kotlin
class ReifiedExample {

    inline fun <reified T> printGenericType() {
        print(T::class.simpleName)
    }
}


fun main() {
    ReifiedExample().printGenericType<String>()
}
```





