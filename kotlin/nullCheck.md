# Null Check

- 코틀린은 null check 를 지원해주는 여러 방법이 있습니다.
- 아래 예시를 보면 null check 방법이 있습니다. 

```kotlin
fun main() {
  // Computer 객체 값이 null 될 수 있다는 것입니다.
    val computer: Computer? = getComputer()
    computer?.calculate() // computer 가 null 이면 실행하지 않습니다.
    
    if (computer != null) {	// 위 코드와 동일합니다.
        computer.calculate()
    }
}

fun getComputer(): Computer {
    return Computer()
}

class Computer {
    fun calculate() {
        println("calculate...")
    }
}
```



- 아래와 같은 예시도 있습니다.

```kotlin
fun main() {
  // computer null 이면 "Unnamed", computer.name 이 null 이면 "Unnamed"
  	val computer: Computer? = null
    val computerName = computer?.name ?: "Unnamed"
    println("computeName : " + computerName)
}

class Computer {
    val name: String? = null

    fun calculate() {
        println("calculate...")
    }
}
```




