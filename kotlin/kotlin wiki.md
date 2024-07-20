





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







# stdlib

- 표준 라이브러리입니다. println, java.io 와 같은 공통적인 함수를 만들어놓은 라이브러리입니다.
- 반복적인 코드는 좋지 않으며, copy-paste 를 하고 있다면 잘못됐다는 것을 인지하고 있어야 합니다.
- 새로운 공통 함수를 만들어야 한다면 확장 함수로 만드는 것이 좋습니다. (오픈소스 보다 찾기가 쉽습니다)

```kotlin
MathUtils.abs(xxx) // abs 를 찾기 어려움.
Int.abs(xxx)   // Int 에서 abs 찾기 쉬움
```





# 프로퍼티 위임

- 프로퍼티가 코드나 내부 동작이 동일하다면 프로퍼티 위임을 통해 재사용할 수 있습니다.

- 아래 예시를 보면 getValue 와  setValue 에 대해서 로깅하는 것이 추가됐습니다.
- LogProp 을 위임한다면 프로퍼티를 get/set 할 때마다 로그가 출력됩니다.

```kotlin
fun main() {
    var propTest: String? by LogProp(null)	// 프로퍼티 위임 (LogProp)
    println(propTest)   // getValue...null
    propTest = "test" // setValue... test
}

private class LogProp<T>(var value: T) {
    operator fun getValue(
        ref: Any?,
        prop: KProperty<*>
    ): T {
        println("getValue...$value")
        return value
    }

    operator fun setValue(
        ref: Any?,
        prop: KProperty<*>,
        newValue: T
    ) {
        println("setValue... $newValue")
        value = newValue
    }
}
```







# Generic

- 자바의 제너릭과 동일합니다. 타입 안정성을 위해 타입을 강제합니다. 



##### source

- 아래 소스를 보면 Array<Parent> 로 타입을 규정한 첫번째 케이스는 에러가 안나고, Child 로 타입을 규정한 두번째 케이스는 컴파일 에러가 발생합니다. 이렇게 되는 이유는 메소드 내부에서 Child 가 변경이 될 수도 있기 때문에 규제를 걸어놓은 것입니다.

```kotlin
open class Parent
class Child : Parent()
fun calculateCalories(array: Array<Parent>) {
    println("size : ${array.size}")
}

fun main() {
    val array: Array<Parent> = arrayOf(Parent(), Child())
    calculateCalories(array)    // size 2

    val errorCase: Array<Child> = arrayOf(Child(), Child())
    calculateCalories(errorCase)    // compile error. 코틀린 제네릭 타입 불변성때문에 에러 발생
}
```



##### source 2

- 아래 예시를 보면 두번째 케이스는 위와 동일하게 컴파일 에러가 나야할 것 같지만 안나고 정상 동작합니다.
- 그 이유는 kotlin 의 공변성 때문입니다.
- Array<T> 는 class Array<T> 이고, List<T> 는 interface List<out E> 로 정의 돼있습니다.

```kot
fun calculateCalories(list: List<Parent>) {
    println("size : ${list.size}")
}

fun main() {
    val list: List<Parent> = listOf(Parent(), Child())
    calculateCalories(list)    // size 2

    val list: List<Child> = listOf(Child(), Child())
    calculateCalories(list)    // size 2
}
```



# 공변성 (out, covariant)

- 쉽게 얘기하면 제약을 푸는 것입니다.
- 자기 자신과 자식에 대한 객체를 허용합니다.



### source

- 아래 예시를 보면 case1, case2 가 있습니다. case 1는 컴파일 에러 발생, case2는 공변성으로 인해 정상 처리됩니다.
- 이 때, 알아둬야할 점은 copyAppliedOut 에서 out 을 적용함으로써 해당 파라미터의 값이 추가되거나 변경되는 것은 하면 안되는 것입니다. 만약 아래 case 1과 같이 Child() 를 source 파라미터로 입력받아서 source 파라미터에 parent 를 상속받은 다른 객체로 변경한다면 제대로 동작하지 않을 것 입니다. 
  - Ex) source[0] = Sub[0] // Sub 는 Parent 를 상속받은 또 다른 객체 
- 즉, out 식별자를 사용하는 것은 read 만 가능하다는 것을 의미합니다.

```kotlin
fun copy(source: Array<Parent>, target: Array<Parent>) {
    for (i in source.indices) {
        target[i] = source[i]
    }
}

fun copyAppliedOut(source: Array<out Parent>, target: Array<Parent>) {
    for (i in source.indices - 1) {
        target[i] = source[i]
    }
}

fun main() {

    // case 1.compile error type mismatch
    val source: Array<Child> = arrayOf(Child(), Child())
    val target: Array<Parent> = arrayOf(Parent())
    copy(source, target) // compile error type mismatch

    // case 2. out 적용
    val source: Array<Child> = arrayOf(Child(), Child())
    val target: Array<Parent> = arrayOf(Parent(), Parent())
    copyAppliedOut(source, target) // 정상 처리
}
```





# 반공변성 (contravariant, in)

- out 이 읽기 전용이였다면 반공변성 in 은 쓰기 전용입니다.



### source

- 아래 소스를 통해 target 에는 Parent 를 상속받은 Child 가 들어갈 수 있고, 값을 변경하거나 추가할 수 있게 됩니다.

```kotlin
fun copyAppliedOutIn(source: Array<out Parent>, target: Array<in Parent>) {
    for (i in source.indices) {
        target[i] = source[i]
    }
}

fun main() {
    val source: Array<Child> = arrayOf(Child(), Child())
    val target: Array<Parent> = arrayOf(Child(), Child())
    copyAppliedOutIn(source, target) // 정상 처리

    println(target.size)    // target 에는 source 의 child 객체들이 들어감
}
```





# 파라미터 제약조건

- 제네릭 파라미터에 제약조건을 걸고 싶을 때가 있습니다. 



### source (제약조건 1개)

```kotlin
// <T: AutoCloseable> 을 제약조건으로 설정함으로써 AutoCloseable 을 구현한 객체만 입력 파라미터 가능
fun <T: AutoCloseable> release(source: T) {
    source.close()  
}
```



### source (제약조건 2개 이상)

```kotlin
interface Sport {
    fun play()
}

interface Calories {
    fun calculate()
}

// 여러개 제약조건을 설정하기 위해 where 사용 
fun <T> playAndCalculate(source: T) where T : Sport, T : Calories {
    source.play()
    source.calculate()
}
```





# `<reified T>`

- 코틀린 제네릭 타입 매개변수에 사용됩니다.

- 자바의 Generic 과 유사합니다. 타입을 강제화시키는 것입니다.

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





# 스타 프로젝션 (star projection)

- 쓰기 X, 읽기만 가능합니다.



### source

- 아래와 같이 List<*> 를 선언해서 어떤 타입이든 받을 수 있습니다. 다만, parameter list 의 값은 변경할 수 없습니다.

```kotlin
fun print(list: List<*>) {
    list.forEach { value ->
        print(value)
    }
}

fun main() {
    print(listOf(1, 2, 3))
    println()
    print(listOf("a", "b", "c"))
}
```





# 새도잉

- 프로퍼티와 파라미터가 같은 이름을 가질 수 있으며, 가독성이 떨어질 수 있습니다.

- 아래와 같은 예시는 쉽게 찾을 수 있습니다. 값을 찍어보면 되니까

```kotlin
class Tree(val name: String) {
  fun addTree(name: String) {
    
  }
}
```



- 아래와 같이 클래스 타입 파라미터, 메소드 타입 파라미터가 T 로 동일합니다. 둘은 다르며, 구분지어줘야 합니다.
- 아래 해결방법중 2번 방법이 가독성이 좋다고 생각됩니다.

```kotlin
interface Tree
class TreeA : Tree
class TreeB : Tree

class Forest<T : Tree> {

    fun addTree(tree: T) {

    }
}

fun main() {
    val forest = Forest<TreeA>()
    forest.addTree(TreeA())
    forest.addTree(TreeB())	// type mismatch
}


// solution 메소드 파라미터 타입을 정의합니다. 
class Forest<T : Tree> {

    fun <T: Tree> addTree(tree: T) {

    }
}

// solution2 타입파라미터 이름을 구분지어줍니다.
class Forest<T : Tree> {

    fun <ST: Tree> addTree(tree: ST) {

    }
}

```









# variance 한정자

- variance 한정자는 위에서 설명한 out, in 을 의미합니다. 즉, 타입을 유연하게 해주는 것입니다.

- Cake<T> 는 한정자가 없음으로 불공변성이며, 불공변성이 의미하는 바는 제네릭 타입에 입력되어지는 것들이 서로 연관이 없다는 것입니다. 아래와 같이 T 에 어떤 타입이 오던 관련이 없습니다.

```kotlin
class Cake<T>

fun main() {
  val a: Cake<Any> = Cake<String>	// type mismatch
}
```



### source (out)

- 아래 M2Mac 은 서브이고, Notebook 이 상위개념입니다. out 을 통해 서브 인스턴스를 받도록 설정한 예제입니다.  

```kotlin
class Computer<out T>
open class Notebook
class M2Mac() : Notebook()

fun main() {
    val a: Computer<Notebook> = Computer<M2Mac>() // 정상
    val b: Computer<M2Mac> = Computer<Notebook> // type mismatch

}
```



### source (in)

- out 과 반대입니다.

```kotlin
class Computer<in T>
open class Notebook
class M2Mac() : Notebook()

fun main() {
    val a: Computer<Notebook> = Computer<M2Mac>() // mismatch
    val b: Computer<M2Mac> = Computer<Notebook>() // 정상
}
```







# Variance(in, out) 안정성

- 자바는 Array 에 대해 Covariant (공변성 = 자신 + 자식 허용) 입니다.
- 근데 아래와 같은 문제가 발생했습니다. 

```java
Integer[] nums = {1,2};
Object[] objs = nums;
objs[0] = "a";	// RuntimeException 발생
```



- 코틀린에서는 위 문제를 해결하기 위해 Array 를 InVariant 로 설정했습니다.





# 생성자 함수 대신 팩토리 메소드를 사용

- 자바에서와 마찬가지로 코틀린에서도 생성자 함수 대신 팩토리 메소드를 사용하는 것이 좋습니다.

- 이름을 붙일수 있습니다. 즉, 가독성이 좋아집니다. 
- 생성자로 만들기 어려운 인스턴스를 만들수 있으니 유연합니다.



### source

```kotlin
class SimpleList<T>(var id: T) {
    override fun toString(): String {
        return "SimpleList(id=$id)"
    }
}

fun <T> createSimpleList(id: T): SimpleList<T>? {
    return SimpleList(id)
}

fun main() {
    // 생성자 사용
    val simpleList = SimpleList<String>("test")
    println(simpleList)

    // factory Method 사용
    val factoryExample = createSimpleList("FactoryMethodExample...")
    println(factoryExample)
}
```





# companion object

- 자바의 static 과 비슷하지만 일부 기능을 더 지원합니다.



### 클래스 내에서 companion object 사용

```kotlin
class SimpleList<T>(var id: T) {
    companion object {
        fun <T> of (id: T): SimpleList<T>? {
            return SimpleList(id)
        }
    }
}

fun main() {
    // companion object 사용
    val example = SimpleList.of("HelloWorld...")
    println(example)
}
```





### interface 에서 companion object 사용

- 아래와 같이 interface 에서 companion object 를 사용할 수 있기에 MyList<T> 를 구현한 다른 클래스에서 of 메소드를 재활용 가능합니다. 단, override 는 안됩니다.

```kotlin
class MyLinkedList<T>(
    val head: T,
    val tail: MyLinkedList<T>?
) : MyList<T> {

}

interface MyList<T> {
    companion object {
        fun <T> of(vararg elements: T): MyList<T>? {
            return null
        }
    }
}
```







# DSL

- Domain Specific Language 
- 확장 함수, 람다 함수, 수신 객체 등을 사용하여 표현력을 높일 수 있습니다. 즉, 가독성이 좋아집니다.



```kotlin
package org.example.dsl

class QueryBuilder {
    // columns 자체는 readOnly
    private val columns: MutableList<String> = mutableListOf()
    private var tableName: String = ""
    private var condition: String = ""

    fun select(vararg cols: String) {
        columns.addAll(cols)
    }

    fun from(table: String) {
        tableName = table
    }

    fun where(cond: String) {
        condition = cond
    }

    fun buildQuery(): String {
        val selectClause = "SELECT ${columns.joinToString(", ")}"
        val fromClause = "FROM $tableName"
        val whereClause =
            if (condition.isNotEmpty()) {
                "WHERE $condition"
            } else {
                ""
            }
        return "$selectClause $fromClause $whereClause"
    }
}

/**
 * @param QueryBuilder.() 확장함수를 의미. init 이 확장함수를 의미. 
 */
fun initQuery(init: QueryBuilder.() -> Unit): String {
    val builder = QueryBuilder()
    // 입력파라미터로 select, from, where 을 받아서 세팅 수행
    builder.init()
    // 쿼리 생성
    return builder.buildQuery()
}

fun main() {
    val query = initQuery {
        select("id", "name")
        from("test")
        where("name = 'test'")
    }
    println(query)
}

```





# Custom DSL

- DSL 은 복잡한 자료구조, 계층적인 구조를 표현할 때 가독성이 좋습니다.
- 아래 예제는 TableBuilder 에 DSL 을 만든 예제입니다.

```kotlin
class TableBuilder {
    var text = ""

    // String.unaryPlus 라는 기존의 함수가 있는데 이것을 override 한 것
    operator fun String.unaryPlus() {
        text += this
    }

    override fun toString(): String {
        return "TableBuilder(text='$text')"
    }
}

// TableBuilder.() 확장함수를 입력받고 리턴이 없습니다.TableBuilder 에 init 함수를 적용합니다.
fun table(init: TableBuilder.() -> Unit) = TableBuilder().apply(init)

fun main() {
    val table = table {
        +"abc"  // unaryPlus 를 override 했기에 단항연산자가 적용됩니다.
        +"cde"
    }
    println(table)
    
    val tableBuilder = TableBuilder().apply {
        +"abc"
        +"cde"
    }
    println(tableBuilder)
}
```







# 함수 타입 정리

- () -> Unit : 아규먼트가 없으며, Unit 을 리턴하는 함수입니다. Unit 은 자바의 void (기본형), Void (참조형) 을 대신하는 용어입니다.
- (Int) -> Unit : Int 를 아규먼트로 받고, Unit 을 리턴하는 함수입니다.
- (Int) -> () -> Unit : Int 를 아규먼트로 받고, 함수를 리턴하는 함수입니다. 함수는 아규먼트가 없고, Unit 을 리턴합니다.





# 함수 파라미터 받기

- 아래 예시는 함수를 파라미터로 받아 함수를 수행하는 예제입니다.



```kotlin
val plus: (Int, Int) -> Int = { a, b -> a + b }
val multi: (Int, Int) -> Int = { a, b -> a * b }

fun operate(a: Int, b: Int, operation: (Int, Int) -> Int): Int {
    return operation(a, b)
}

fun main() {
    println("plus: ${operate(5, 3, plus)}")
    println("multi: ${operate(5, 3, multi)}")
}
```





# 익명 함수

- 익명 함수는 일반 함수처럼 만들고 메소드 이름만 빼면 됩니다.

```kotlin
//  Int 의 확장함수 이며, Int 를 파라미터로 받습니다.
fun Int.plus(other: Int) = this + other

// myPlus 의 타입은 확장함수를 나타내며, 리시버를 가진 함수 타입이라고 합니다.
val myPlus = fun Int.(other: Int) = this + other

// myPlus2 의 타입은 Int 를 받아서 Int 를 리턴하는 함수입니다.
val myPlus2: Int.(Int) -> Int = fun Int.(other: Int) = this + other

fun main() {
    println("result : ${myPlus.invoke(3, 4)}")
    println("result : ${myPlus(3, 4)}")
    println("result : ${3.myPlus(4)}")
}
```





# 컴포지션

- 객체 지향 프로그래밍에서 한 객체가 다른 객체들을 포함하고, 그 포함된 객체의 기능을 활용하여 복잡한 기능을 수행하는 방법입니다. 객체 간의 관계를 형성하고 코드 재사용성과 모듈화를 촉진하는 개념입니다.



### source

- 아래 예시와 같이 PdfLoader, ImageLoader 에 FileStream 이라는 객체를 선언하여 모듈을 사용하는 예제입니다. FileStream 뿐만 아니라 여러 객체들을 선언하여 사용할 수 있습니다.

```kotlin
class FileStream {
    fun open() {}
    fun close() {}
}

class PdfLoader {
    val fileStream = FileStream()
    
    fun init() {
        fileStream.open()
    }
}

class ImageLoader {
    val fileStream = FileStream()

    fun init() {
        fileStream.open()
    }
}
```





# 연산 또는 액션을 전달할 때는 interface 대신 함수 타입 사용

- 위에서도 설명을 했지만 함수 메소드로 interface 대신 함수 타입으로 넘기는 것이 더 장점이 많다고 생각됩니다. 



### source

- 아래 코드에서 interface 를 사용한다고 가정하면 Plus, Multi 에 대한 구현체를 각각 만들어야 하니 불편합니다.

```kotlin
// 함수 타입 사용
val plus: (Int, Int) -> Int = { a, b -> a + b }
val multi: (Int, Int) -> Int = { a, b -> a * b }

fun operate(a: Int, b: Int, operation: (Int, Int) -> Int): Int {
    return operation(a, b)
}

fun main() {
    println("plus: ${operate(5, 3, plus)}")
    println("multi: ${operate(5, 3, multi)}")
}


// interface 사용
fun operate(a : Int, b: Int, operator: Operator) {
  return operator.operate(a,b)
}

interface Operator {
  fun operate(a: Int, b: Int) {}
}
```





# sealed

- 클래스 계층 구조를 제한하는 데 사용되는 클래스 한정자입니다. 



### 특징



##### 상속 제한

- 클래스는 직접 상속할 수 있는 클래스를 제한합니다. sealed 클래스를 상속하는 클래스는 동일한 파일 내에 선언되어야 합니다. 다른 파일에서 `sealed` 클래스를 상속하는 것은 허용되지 않습니다.



##### 제한된 하위 클래스

- `sealed` 클래스는 자체적으로 하위 클래스를 가질 수 있습니다. 하위 클래스는 동일한 파일 내에 선언되어야 하며, `sealed` 클래스를 직접 상속해야 합니다. 외부 파일에서 `sealed` 클래스의 하위 클래스를 선언하는 것은 허용되지 않습니다.





# 동등성



### 구조적 동등성

- equals 와 '==' 를 의미합니다. 
- a == b 에서 a 가 nullable 이 아니면 a.equals(b) 로 변환됩니다.
- a 가 nullable 이면 a?.equals(b) ? :  (b == null) 로 변환됩니다.



### 레퍼런스 동등성

- 두 연산자가 같은 객체 주소를 가리키면 return true 합니다.



### 규약

- 반사적 동작 : x 가 null 아니면 x.equals(x) -> true 리턴해야 합니다
- 대칭적 동작 : x, y null 이 아니면 x.equals(y) , y.equals(x) 같은 결과 출력해야 합니다.
- 연속적 동작 : x,y,z 가 null 이 아니면 x.equals(y), y.equals(z) 가 같은 결과이면 z.equals(x) 도 같은 결과여야 합니다.
- 일관성이 있어야 하며, 빠른시간내에 리턴해야 합니다. 



# data class 를 동등성 비교

-  객체 주소는 다르지만 값을 비교하고 싶은 요건이 있을 수 있습니다.



### source

- 아래 예시를 보면 A 로 객체를 생성해서 비교했을 때는 false 이지만, data class 로 생성해서 비교했을 때는 데이터가 같으므로 true 를 리턴합니다.
- 데이터 비교가 필요할 때는 data class 를 사용하는 것이 좋습니다.

```kotl
class A(var name: String)
data class AA(var name: String)

fun main() {
    val a = A("test")
    val a1 = A("test")
    val a2 = A("test")

    println(a == a1)    // false
    println(a == a2)    // false

    val aa = AA("test")
    val aa1 = AA("test")
    val aa2 = AA("test")

    println(aa == aa1)  // true
    println(aa == aa2) // true
}
```





# HashCode

- 컬렉션에 데이터를 빠르게 추가하고, 빠르게 조회해야하는 요건이 있습니다.
- 이 때, 사용될 수 있는게 map, set 이고, 중복을 허용하지 않습니다. 
- 빠르게 추가하기 위해 중복이 있는 것을 체크해야하고 이를 위해 해시 테이블을 사용합니다. 해시 테이블에 어떤 위치에 데이터를 배치할지는 해시 함수를 통해 계산을 합니다.
- 즉, 해시 코드를 통해 값을 추가하거나 조회할 때 사용합니다. (빠르고 충돌이 적음)



### equals , hashcode

- equals 는 값을 비교하며, equals 를 재정의한다면 hashcode 또한 재정의를 해야합니다. 왜냐하면 equals 를 재정의를 해서 비교를 했을 때, 값이 같다면 hashcode 를 통해서도 값이 같아야 하기 때문입니다.
- equals 와 마찬가지로 같은 값에 대하여 일관적으로 똑같은 값을 리턴해줘야 한다는게 hashcode 의 규약입니다.







# 비교 구문 (Compare)

- 비교 구문 예시입니다.



### source

- 1개만 비교할 때는 sortedBy, 2개 이상 비교할 때는 sortedWith 를 사용합니다.

```kotlin
data class Member(val name: String, val age: Int)

fun main() {
    val members = listOf<Member>(
        Member("t2", 12),
        Member("t1", 10)
    )

    val membersSortByNameAge = members.sortedWith(compareBy({ it.name }, { it.age }))
    println(membersSortByNameAge)  // Member(name=t1, age=10), Member(name=t2, age=12)

    val membersSortByName = members.sortedBy { it.name }
    println(membersSortByName) // Member(name=t1, age=10), Member(name=t2, age=12)
}
```





# 확장 함수 vs 멤버 함수

- 함수를 작성할 때, 클래스 내에 작성한 것인지 확장 함수를 통해 작성할 것인지 하는 상황이 있을 수 있습니다.



### 정리

- 확장 함수는 객체내 프로퍼티가 없습니다. 즉, 상태를 가지고 있지 않으면 행위만 있습니다. 멤버 함수는 객체 내에 위치해 있기에 상태를 가질 수 있습니다.
- 확장 함수는 상속, 어노테이션 처리를 하지 않고, 클래스 내부에 없으니 찾기 어려울 수 있습니다. (확장 함수는 package 를 어떻게 가져갈지에 대해서 고민이 필요해 보입니다.)





