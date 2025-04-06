# Test Double

테스트 더블(Test Double)은 테스트 대상 객체와 연관된 객체를 대신하여 테스트를 용이하게 해주는 객체를 의미한다.
테스트의 **명확성, 독립성, 유지보수성**을 높여주며, 연관 객체의 상태나 동작을 외부 환경의 영향 없이 직접 제어할 수 있다.

> 영화 촬영 시 배우의 위험한 역할을 대신하는 스턴드 더블에서 유래되었다.

---

## Dummy
- 사용은 되지 않지만 함수의 파라미터로 전달되어야 할 때 사용한다.
- 보통 아무 로직도 수행하지 않는 껍데기 객체로 활용된다.

```kotlin
interface Repository {
    fun save(data: String)
}

class DummyRepository : Repository {
    override fun save(data: String) {
        // 아무 것도 하지 않음
    }
}

class Service(private val repository: Repository) {
    fun execute() {
        // 내부적으로 repository를 사용하지 않음
    }
}

// 더미 객체를 Service 생성자의 파라미터로 활용
val service = Service(DummyRepository())
service.execute()
```

---

## Fake
- 실제 동작을 간단히 흉내 낸 구현체로, **테스트에서만** 사용하는 간단한 객체이다.
- 예: 실제 DB 대신 메모리에 저장하는 Fake 저장소.

```kotlin
class FakeMemoryRepository : Repository {
    // 가짜 인메모리 데이터베이스를 생성하여 실제 데이터베이스의 역할을 대체한다
    private val storage = mutableListOf<String>()

    override fun save(data: String) {
        storage.add(data)
    }
    // fake storage에서 반환
    fun getAll(): List<String> = storage
}

val fakeRepo = FakeMemoryRepository()
val service = Service(fakeRepo)
service.repository.save("test")
println(fakeRepo.getAll()) // ["test"]
```

---

## Stub
- 특정 입력에 대해 **미리 정해둔 값**을 반환하는 객체이다.
- 테스트를 예측 가능하게 만들기 위해 사용된다.

```kotlin
interface UserService {
    fun getUserName(id: Long): String
}

class StubUserService : UserService {
    override fun getUserName(id: Long): String = "StubUser"
}

val userService = StubUserService()
println(userService.getUserName(123)) // 항상 "StubUser"를 반환함
```

---

## Spy
- 실제 객체처럼 동작하지만, **자기 자신이 호출된 내역**을 추적할 수 있는 객체이다.

```kotlin
class SpyRepository : Repository {
    // 메소드가 호출되었는지에 대한 여부
    var called = false
    // 가장 최근에 들어온 인자 값을 저장
    var lastSavedData: String? = null

    override fun save(data: String) {
        called = true
        lastSavedData = data
    }
}

val spy = SpyRepository()
val service = Service(spy)
spy.save("hello")

println(spy.called) // true
println(spy.lastSavedData) // "hello"
```

---

## Mock
- 지정된 응답을 반환할 수 있고, 자기 자신의 호출을 검증할 수 있는 객체이다.
- 일반적으로 Mocking 프레임워크(MockK, Mockito 등)를 통해 생성한다.

---

### MockK (Kotlin 전용)

- 공식: https://mockk.io
- Gradle:

```kotlin
testImplementation("io.mockk:mockk:1.13.8")
```

### MockK 예시

```kotlin
import io.mockk.*

interface Repository {
    fun save(data: String)
}

class Service(private val repository: Repository) {
    fun saveSomething(data: String) {
        repository.save(data)
    }
}

val mockRepository = mockk<Repository>()
// mocking 응답 지정
every { mockRepository.save("Hello") } just Runs

val service = Service(mockRepository)
service.saveSomething("Hello")

// save 메소드 호출 검증
verify { mockRepository.save("Hello") }
```

---
## 요약

| 유형   | 목적                         | 설명                                                        | 특징                                         |
|--------|------------------------------|-------------------------------------------------------------|----------------------------------------------|
| Dummy  | 인자 채우기 용               | 사용되지 않지만 인자로 필요한 객체                          | 실제로 사용되지 않음                         |
| Fake   | 단순 구현체                  | 실제 동작 가능한 단순한 구현체                              | 실제 기능을 간단하게 구현 (메모리 DB 등)     |
| Stub   | 반환값 고정                  | 고정된 값을 반환하도록 설정된 객체                          | 입력에 따라 정해진 값 반환                   |
| Spy    | 호출 기록 확인               | 호출 여부나 인자를 기록할 수 있는 객체                      | 실제 구현 + 호출 감시 가능                   |
| Mock   | 지정된 응답 반환 + 호출 검증 | 테스트 중 특정 동작을 설정할 수 있으며, 호출 여부 및 인자 검증도 가능 | 주로 Mocking 프레임워크 사용 (Stub + Spy 기능 포함) |

---

## 번외 MockK의 인자 캡처: `slot`

MockK는 단순한 Stub/Mock 외에도, 함수 호출 시 전달된 **실제 인자를 가로채서 확인**할 수 있는 기능인 `slot`을 제공한다.

### slot이란?
`slot`은 테스트 대상 메서드가 호출될 때 전달되는 인자를 **저장(capture)** 하기 위한 기능이다. 즉, "이 메서드가 불렸을 때 실제 어떤 값이 인자로 넘어갔는지 알고 싶다"라는 상황에 사용한다.

사용 방법
- `slot<String>()`처럼 타입을 명시해야 한다.
- `.captured`는 마지막 호출 값만 저장한다.
- 여러 번 호출되는 경우는 mutableListOf<>()를 사용해야 전체 추적이 가능하다.

### 예제

```kotlin
import io.mockk.*

interface UserRepository {
    fun save(name: String)
}

val mockRepository = mockk<UserRepository>()
val nameSlot = slot<String>()

every { mockRepository.save(capture(nameSlot)) } just Runs

mockRepository.save("Alice")

println("Captured name: ${nameSlot.captured}") // "Alice"

// 여러개의 인자도 캡처 가능
val nameList = mutableListOf<String>()
every { mockRepository.save(capture(nameList)) } just Runs

mockRepository.save("Alice")
mockRepository.save("Bob")

println(nameList) // ["Alice", "Bob"]
```

### 나중에 참고할 자료
- [MockK의 흑마술을 파헤치자!](https://sukyology.medium.com/mockk%EC%9D%98-%ED%9D%91%EB%A7%88%EC%88%A0%EC%9D%84-%ED%8C%8C%ED%97%A4%EC%B9%98%EC%9E%90-6fe907129c19)

- [Kotlin MockK 사용법 (공식 문서 번역)](https://www.devkuma.com/docs/kotlin/mockk/)