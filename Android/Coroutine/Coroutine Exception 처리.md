
코루틴은 exception 발생시 exception이 발생한 코루틴을 cancel시키고 해당 exception을 부모 코루틴으로 전달한다.  
그럼 부모 코루틴도 마찬가지로 본인 코루틴을 cancel하고, 하위의 자식 코루틴 또한 cancel을 수행한다. 그 후, 자신의 부모 코루틴으로 exception을 전파한다.  
위 과정이 최상위 코루틴까지 퍼지도록 반복된다.

이 때, try-catch로 코루틴을 래핑하여 감싸는 방법은 코루틴 블럭 내에서 처리하는 경우에는 유효할 수 있다.
```kotlin
launch {
    try {
        // 예외가 발생한 경우 
        error("cancels coroutine")
    } catch (e: Exception) {
        // 코드가 여기로 진행된다. 
        print("launch failed")
    }
}
```
하지만, 코루틴 블럭 자체를 예외처리로 감싼다면 위와 같은 효과를 기대할 수 없다.  
왜냐하면 코루틴은 예외가 발생하면 해당 코루틴을 실행한 CoroutineScope에도 예외를 전파하기 때문이다.  
```kotlin
// 코루틴 종료까지 프로세스를 유지해야 해서 runblocking을 사용합니다. 
fun main(args: Array<String>): Unit = runBlocking {
    try { // launch를 try-catch로 감싼다. 
        launch {
            error("cancels coroutine")
        }
    } catch (e: Exception) {
        println("catched $e")
    }
    print("main done")
}
```
위 코드에서는 try-catch에서 exception을 잡고 "main done"을 출력해야 할 것 같지만, 그렇지 못하고 비정상 종료가 이루어진다.  
위에서 얘기한대로 `runBlocking`(부모 코루틴)에 에러가 전파되어 프로세스 자체가 종료되기 때문이다.  
이처럼 예외가 발생할 때 이를 받아 처리하는 방법에는 3가지가 있다.  

1. 부모 - 자식 코루틴 구조가 아닌 새로운 코루틴 스코프를 생성하여 거기서 로직을 수행
2. 에러를 한 방향으로 처리하는 SupervisorScope 사용
3. CoroutineScope에서 처리되지 않은 예외를 받는 기능 추가

### 1. 부모 - 자식 코루틴 구조가 아닌 새로운 코루틴 스코프를 생성하여 로직을 수행
방금 전 예시 코드의 `launch`는 사실 `runBlocking`의 `this`를 사용하는 `this.launch`와 같다.
```kotlin
 fun main(args: Array<String>): Unit = runBlocking {
    try {
        this.launch { // 해당 함수 블록의 this(coroutineScope 에서 호출)
            error("cancels coroutine")
        }
    } catch (e: Exception) {
        println("catched $e")
    }
    print("main done")
}
```
코루틴은 예외를 부모 코루틴으로 전달하므로 `this.launch`를 사용하지 않고, 새로운 `ScopeBuilder`를 사용하면 된다.
**`builder`로는 `coroutineScope`, `GlobalScope` 등이 있다.**
```kotlin
fun main(args: Array<String>): Unit = runBlocking {
    try {
        coroutineScope { // 여기부터는 다른 scope 
           launch {
                error("cancels coroutine")
            }
        }
    } catch (e: Exception) {
        println("catched $e")
    }
    delay(1000)
    print("main done")
}
```

### 2. `SupervisiorScope` 사용하기
코루틴에서 예외가 발생했다고 코루틴 전체를 cancel 시킬 필요는 없다.  
이러한 상황을 위해 예외를 child로만 퍼트리는 특별한 Scope가 있는데 이것이 바로 `SupervisorScope`이다.
```kotlin
fun main(args: Array<String>): Unit = runBlocking {
    supervisorScope {
        launch {
            delay(500)
            println("first scope done")
        }

        // 예외가 발생해도, 해당 launch만 실패하게 됩니다. (예외를 위로 전달하지 않음)
        launch {
            error("cancels coroutine")
            println("second scope done")
        }
        delay(1000)
        println("main scope done")
    }
    print("main function completed")
}
```
위 코드에서 두번째 launch에서만 예외가 발생하고, 이를 실행한 supervisorScope에는 예외가 퍼지지 않고 해당 코루틴만 실패한다.

### 3. `CoroutineExceptionHandler` 사용하기
`CoroutineExceptionHandler`를 사용하여 예외처리를 해결할 수 있다.  
안드로이드 viewModel의 경우 viewModelScope의 context에 coroutineExceptionHandler를 추가하여 사용시 Scope에서 발생하는 예외처리가 가능하다.
```kotlin
// SomeViewModel 
val coroutineExceptionHandler = CoroutineExceptionHandler { coroutineContext, throwable -> 
    // handle error
}

val scope = viewModelScope + coroutineExceptionHandler
scope.launch {
    // scope를 사용한 launch 에서 발생한 예외는 coroutineExceptionHandler로
}
```
