





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



### 타입 아규먼트에 reified 한정자를 붙일 수 있습니다.

```kotlin
// 자바에는 제네릭이 없어서 reified 로 제네릭을 사용해도 컴파일 시점에 제네릭 관련 내용이 사라짐.
// 즉, 못쓴다는 것
fun <reified T> printTypeName() {
    print(T::class.simpleName)
}

// 아래와 같이 inline 을 사용해서 컴파일 시점에 아래 함수를 직접 호출함으로써 제네릭을 그대로 가져다 쓸 수 있음
inline fun <reified T> printTypeName() {
    print(T::class.simpleName)
}

fun main() {
    printTypeName<String>()
}
```



### 함수 타입 파라미터를 가진 함수가 더 빠르게 동작합니다.

- 아래와 같이 예제가 있을 때, JVM 에서는 함수형을 파라미터로 받는 (action: (Int) -> Unit) 을 컴파일 시점에 변환시킵니다. 그렇기에 Inline 이 안붙은 printNoInline 은 호출했을 때, 파라미터 변환이 일어나고 변환된 것을 호출되도록 수행되기 때문에 미세한 차이가 발생합니다.
- Inline 의 경우 그런게 없기 때문에 오버헤드가 없습니다.

```kotlin
inline fun printInline(arg: Int, action: (Int) -> Unit) {
 	print(arg, arg2)
}

fun printNoInline(arg: Int, action: (Int) -> Unit) {
 	print(arg, arg2)
}
```







### 비지역 리턴을 사용할 수 있습니다.





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



### 주의사항

- 클래스 내부에 확장함수를 선언하는건 좋지 않습니다. 가시성을 제한합니다.

```kotlin
package org.example.extension

class ExtensionFunctionExample3 {
    fun String.isAddr(): Boolean = length == 13
}

private fun String.isAddr(): Boolean = length == 13

fun main() {
    // 클래스 내부에 확장함수를 쓰는건 될 수 있으면 피하는게 좋습니다. 가시성을 제한합니다. 또한, 레퍼런스도 쓸 수 없습니다.
    val isAddr = ExtensionFunctionExample3().apply { "12345".isAddr() }
    println(isAddr)

    // 이런 방식으로 쓰는게 좋습니다.
    val str = "abcde12345"
    val isAddrBoolean = "abc".isAddr()
    val isAddrBooleanReference = str::isAddr // 오류 안남
    println(isAddrBoolean)
}
```









