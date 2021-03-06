





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



# Mutable 컬렉션

- mutable 컬렉션은 수정 가능한 컬렉션입니다. 

```kotlin
val mutableList: MutableList<Int> = mutableListOf(1,2)
mutableList.add(3)
```



# ? 와 !!



### ?

- 변수 또는 타입에 선언되면 null 일 수 있는 것을 나타냅니다.

```kotlin
val name: String? = null	// name 은 변수명, String 타입
```



### !!

- nullable -> non-null 강제로 캐스팅합니다.
- 만약 str이 null 이면 NPE 가 떨어집니다.
- 될 수 있으면 !!  는 안쓰는 것이 좋습니다. 당장의 코드가 not-null 이라 !! 을 써도 나중에 코드를 변경했을 때, 체크하기 어렵기 때문입니다.

```kotlin
val length = str!!.length	// str 을 non-null 로 강제 캐스팅
```







# lateinit

- 늦은 초기화를 의미합니다.



### 특징

- 초기화 전에 값을 사용할려고 하면 예외가 발생하니 초기화가 필요한 부분에만 사용해야 합니다.
- 추후 nullable 로 변경 가능합니다.



### Source

```kotlin
class Lateinit {
  private lateinit var no: Int
  
  fun init() {
    no = 10
  }
}
```





# Delegates.notNull

- primitive type 초기화 할 때 사용합니다.
- Int, long 등



# Use

- 자바에는 try-catch-resource 라는 것을 제공하고 있습니다. 자원을 열었을 때, 구문이 닫히면 자원을 해제하는 것입니다.
- kotlin 에서는 use 를 사용하여 resource 를 해제할 수 있습니다.
  - Closeable/AutoCloseable 을 구현한 객체에만 적용됩니다.

```kotlin
class UseExample {
    fun readLine(filePath: String): String {
        BufferedReader(FileReader(filePath))
            .use { 
                return it.readLine()
        }
    }
}
```





# data class

- kotlin 의 data class 는 데이터를 보관하고 전달하기 위한 클래스입니다.



### 특징

equals(), hashCode(), toString(), copy() 등의 메소드를 제공해줘서 편리합니다.



### source

```kotlin
data class DataClassExample(val name: String)

fun main() {
    val dataClassExample = DataClassExample("test")
    val copy = DataClassExample("test").copy()
    println(dataClassExample == copy)
    println(dataClassExample.equals(copy))
}
```









# scope function

### 정의

- 범위 지정 함수는 수신 객체와 수신 객체 지정 람다 두가지 구성요소를 가집니다.



##### 예시 설명

```kotlin
// receiver 가 수신 객체, block 이 수신 객체 지정 람다 입니다.
inline fun <T, R> with(receiver: T, block: T.() -> R): R {
    return receiver.block()
}
```



### let

- 객체의 컨텍스트에서 블록내 코드를 실행하는 기능입니다. 주어진 객체를 람다 식의 인자로 받아서 블록 내에서 처리합니다.

- 예를 들면, null 이 아닌 경우에 코드를 실행할 때, 지역 변수의 범위를 제한 하는 경우에 사용합니다.



#####  source

```kotlin
data class People(var name: String)

fun main() {
    var person = People("test")
    // person 이 null 이 아닐 때 수행합니다.
    person?.let { it -> // person(=it) 는 내부에서만 사용 가능합니다.
        it.name = it.name + "1234"
    }
    println(person)
}
```



### With

- NotNull 객체를 사용하며, 블럭 return 값이 없습니다.
- 아래와 같은 코드와 같이 NotNull(Person) 객체이면서 return 없는 곳에서 활용 가능합니다.



##### source

```kotlin
data class Person(var name: String)

fun main() {
    var person = Person("test")
    with(person) {
        println(name)
    }
}
```



### apply

- 수신 객체 람다 내부에서 수신 객체의 함수를 사용하지 않고, 수신 객체 자신을 다시 반환하는 경우에 apply 를 사용합니다.
- 예를 들면, 객체 초기화가 있습니다.



##### source

```kotlin
class ApplyExample {
    lateinit var name: String

    override fun toString(): String {
        return name
    }
}

fun main() {
    var example = ApplyExample().apply {
        name = "init"
    }
    println(example)
}
```



### Also

- 수신 객체 람다가 전달된 객체를 사용 하지 않거나, 수신 객체의 속성을 변경하지 않을 때 also 를 사용합니다.
- apply 와 차이점은 apply 는 수신 객체를 받은 뒤, 속성을 변경시킬 수 있지만 Also 는 변경 안됩니다. 
- 아래와 같이 값 체크 같은 검증을 수행할 때 사용합니다.



##### source

```kotlin
data class AlsoExample(var name: String)

fun main() {
    // AlsoExample 객체를 변경하지 않고 (수신 객체) 그대로 사용
    var alsoExample = AlsoExample("test").also {
        requireNotNull(name)
    }
    println(alsoExample)
}
```





### run

- 연산하거나 여러 지역 변수의 범위를 제한하기 위해 run 을 사용합니다.

- 아래 예시를 보면 문자열 + 연산 수행하고, 객체들의 범위를 제한할 수 있습니다.



##### source

```kotlin
data class RunExample(var content: String)
data class RunExample2(var content: String)
data class RunExample3(var content: String)


fun main() {
    var allContent = run {
        RunExample("content1").content +
                RunExample2("content2").content +
                RunExample3("content3").content
    }
    println(allContent)
}
```







# Unit

- unit 을 알기 전에 void, Void 개념을 알고 가야합니다.
- void 는 반환 값이 없다는 것입니다. Void 도 비슷한 의미이지만 void 와 차이점은 void 는 기본형이고, Void 는 참조형입니다.
- 예를 들면, public void test() {},Class`<Void>`  예시가 있습니다.
- 다시 돌아와서 Unit 은 자바의 void, Void 를 대신할 수 있는 참조형입니다. 굳이 구분해서 쓸 필요 없이 Unit 하나만 사용해도 됩니다.



##### Example Source

```kotlin
fun print(message: String): Unit {
  println(message)
}
```



##### 주의사항

- Unit 을 Boolean 과 같이 동작하도록 사용하면 안됩니다.
- Unit 은 값이 없는 것이기에 Unit? 은 의미가 없습니다. 가독성만 복잡하게 할뿐입니다. Boolean 을 사용하는게 낫습니다.

```kotlin
class UnitExample {
    fun isEqualsUsingUnit(str: String): Unit? {
        print("isEquals : ${str.equals("str")}")
        return null
    }
}

fun main() {
    val unit = UnitExample().isEqualsUsingUnit("str")
    println(unit)
}
```





# 리시버

- 리시버란? 객체 외부의 람다 코드 블록을 객체 내부에서 사용하는 것 처럼 작성할 수 있게 해줍니다.
- 리시버를 사용할 떄는 명시적으로 적어주는게 가독성이 좋습니다.



##### source example

```kotlin
// T 가 Receiver. R 를 반환
// 아래와 같이 리시버를 사용하는 람다를 lambda with receiver 라고 함. 	 
block : T.() -> R

// 아래는 람다파라미터를 받음
block : (T) -> R
```



##### source example 2

```kotlin
data class ReceiverExample2(var name: String)

fun main() {
  // also 는 it 으로 접근 가능
    ReceiverExample2("test12345").also {
        println("name : ${it.name}")
    }

  // apply 는 this 로 접근 가능
    ReceiverExample2("test12345678").apply {
        println("name : ${this.name}")
    }
}


// also 는 람다를 입력으로 받음 -> it 을 사용해서 객체 접근 -> 느낌이 it 이라는 것을 통해 접근하므로 외부에서 접근하는 느낌
public inline fun <T> T.also(block: (T) -> Unit): T {
    block(this)
    return this
}
// apply 는 receiver 를 받음 -> this 를 사용해서 객체 접근 -> this 를 통해 접근하니 내부에서 접근하는 느낌
public inline fun <T> T.apply(block: T.() -> Unit): T {
    block()
    return this
}

```







# 프로퍼티

- kotlin 의 프로퍼티는 자바의 필드와 유사하지만 더 많은 기능을 제공합니다.

```kotlin
var name: String = "test" // kotlin 프로퍼티

private String name = "test";	// java 필드
```



##### 특징 1. field

- 추가로 제공하는 기능은 사용자 정의 getter, setter 입니다.

- 아래 예시와 같이 var 을 이용해서 프로퍼티를 만들면 setter, getter 가 디폴트로 생기고, field 도 기본적으로 제공됩니다.

- val 은 읽기 전용이기에 field 를 제공하지 않습니다.

  

##### source (field 사용)

```kotlin
class PropertyExample {
    var name: String? = ""
        // 대문자로 치환. field name 을 가리키는 reference 입니다.
        get() = field?.uppercase()
        // 받는 값 그대로 세팅
        set(value) {
            field = value
        }
}

fun main() {
    var propertyExample = PropertyExample()
    propertyExample.name = "HelloWorld..."
    println(propertyExample.name)
}
```





##### 특징 2. 확장프로퍼티

- 프로퍼티는 함수여서 확장 프로퍼티를 만들 수 있습니다.



##### source (확장 프로퍼티)

```kotlin
// string 에 count 라는 확장 프로퍼티를 만들었습니다.
val String.count: Int
    get() = this.length	// val 의 경우 읽기 전용이며, get 을 default 로 생성해줍니다. 여기서는 get 을 재정의했습니다. 


fun main() {
    var text = "HelloWorld..."
    println(text.count)
}
```



##### 주의사항

- 확장 프로퍼티는 함수와 비슷한 용도입니다. 확장 프로퍼티와 함수를 어떻게 구분해야하는가? 
- 프로퍼티는 상태를 설정하거나 나타낼 때 설정하는 것이 좋습니다.
- 프로퍼티는 상태를 get/set 할 때 사용하는 것이 좋으며, 나머지 경우에 함수를 사용하는게 좋을 것이라 생각됩니다.
- 자바의 경우와 비슷하게 상태는 peroperty, 행위는 메소드로 표현하는 것이 좋다 생각됩니다.





# Open

- kotlin 은 기본적으로 클래스와 메서드에서 final 로 선언됩니다. 즉, 상속이 안됩니다.
- 상속이 필요하다면 open 을 사용해야 합니다.



##### abstract 차이점

- abstract 는 인스턴스화 할 수 없으나 open 은 가능합니다.
- abstract 는 하위 클래스에서 필수적으로 override 해야하며, open 클래스는 선별적으로 override 가 가능합니다.



##### source

```kotlin
// 상속 가능한 OpenExample class
open class OpenExample {
    // 상속 가능한 openMethod
    open fun openMethod(): String {
        return "HelloWorld..."
    }

    // 상속 불가능한 Method
    fun notOpenMethod(): String {
        return "NotOpenMethod..."
    }
}

class Sub() : OpenExample() {
    override fun openMethod(): String {
        return super.openMethod()
    }
}

fun main() {
    var str = Sub().openMethod()
    println(str)

    str = OpenExample().openMethod()
    println(str)
}
```







