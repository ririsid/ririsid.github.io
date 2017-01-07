---
layout: post
title: "오류 처리 (Error Handling)"
tags: swift
date: 2015-07-26 23:31:01
---

# 스위프트 스터디 5주차 (2015.08.01)

**이 문서는 Apple의 _The Swift Programming Language (Swift 2 Prerelease)_ 의 요약입니다.**

## 오류 처리 (Error Handling)

- 스위프트는 실행중(runtime)에 회복 가능한 오류(recoverable errors)를 던지고(throwing), 받고(catching), 전파하고(propagating), 조작하도록(manipulating) 돕는 퍼스트 클래스를 제공한다.
- 어떤 함수와 메소드는 항상 실행이 완료되거나 유용한 결과를 내놓는다고 보장하지 않는다.
- 옵셔널은 값이 없음을 표현한다. 하지만 때로는 함수가 실패했을 때, 어떻게 실패했는지 알고 거기에 대응하는 게 더 유용하다.
- 예를 들어, 파일을 읽고 처리한다고 하자. 이 작업이 실패하는 경우는 여러가지이다. 지정한 위치(path)에 파일이 없거나, 읽기 권한이 없거나, 적절한 포멧으로 인코딩되지 않았거나. 이러한 구분은 프로그램이 오류를 해결하고, 회복하고, 필요하면 사용자에게 알리는 것을 가능하게 한다.

> 스위프트의 오류 처리 패턴은 코코아와 오브젝티브-C의 NSError와 함께 쓸 수 있다.

### 오류 표현

- 스위프트에서 오류는 `ErrorType` 프로토콜을 따르는 타입의 값으로 표현된다.
- 스위프트 열거형은 오류에 대한 추가정보를 가진 관련 값을 포함한 관계된 오류 상태 그룹을 모델링하는데 적합하다.
- 이는 스위프트 열거형이 `ErrorType` 을 채용하면, 컴파일러에 의해 합성된 구현을 자동으로 가지기 때문이다.
- 다음은 자판기의 오류 상태의 표현의 예제다.

```swift
enum VendingMachineError: ErrorType {
    case InvalidSelection
    case InsufficientFunds(required: Double)
    case OutOfStock
}
```

### 오류 던지기(Throwing Errors)

- 함수나 메소드의 정의에서 파라미터 다음에 `throws` 키워드를 써서 그 함수나 메소드가 오류를 던질 수 있다는 것을 표시한다.
- 반환형이 있으면 `throws` 키워드를 반환 화살표 (->) 전에 적어야 한다.
- 함수, 메소드, 클로저는 명시적으로 표시하지 않으면 오류를 던질 수 없다.

```swift
func canThrowErrors() throws -> String

func cannotThrowErrors() -> String
```

- 던지는 함수(throwing function)의 본문 어디서나 `throw` 구문을 써서 오류를 던질 수 있다.

```swift
struct Item {
    var price: Double
    var count: Int
}

var inventory = [
    "Candy Bar": Item(price: 1.25, count: 7),
    "Chips": Item(price: 1.00, count: 4),
    "Pretzels": Item(price: 0.75, count: 11)
]
var amountDeposited = 1.00

func vend(itemNamed name: String) throws {
    guard var item = inventory[name] else {
        throw VendingMachineError.InvalidSelection
    }

    guard item.count > 0 else {
        throw VendingMachineError.OutOfStock
    }

    if amountDeposited >= item.price {
        // Dispense the snack
        amountDeposited -= item.price
        --item.count
        inventory[name] = item
    } else {
        let amountRequired = item.price - amountDeposited
        throw VendingMachineError.InsufficientFunds(required: amountRequired)
    }
}
```

```swift
let favoriteSnacks = [
    "Alice": "Chips",
    "Bob": "Licorice",
    "Eve": "Pretzels",
]
func buyFavoriteSnack(person: String) throws {
    let snackName = favoriteSnacks[person] ?? "Candy Bar"
    try vend(itemNamed: snackName)
}
```

### 오류 받고 처리하기(Catching and Handling Errors)

- `do-catch` 문을 사용해서 오류를 받고 처리할 수 있다.

```swift
do {
    try function that throws
    statements
} catch pattern {
    statements
}
```

- 오류가 던져지면 오류는 `catch` 절을 만날 때까지 바깥 스코프로 전파된다.
- `switch` 문처럼 컴파일러는 `catch` 절이 완전한지 추론한다. 그렇게 판단하면 오류가 관리되고 있다고 간주한다.
- 그렇지 않으면, 포함하고 있는 영역에서 오류를 처리해야 한다. 또는, 포함하고 있는 함수가 `throws` 와 함께 정의되어야 한다.
- 오류가 처리되는 것을 보장하기 위해, 모든 오류에 대응하는 패턴으로 `catch` 절을 사용한다.
- 만약 `catch` 절이 패턴을 특징 짓지 않는다면, 모든 오류를 `error` 라는 이름의 지역 변수로 연결한다.

```swift
do {
    try vend(itemNamed: "Candy Bar")
    // Enjoy delicious snack
} catch VendingMachineError.InvalidSelection {
    print("Invalid Selection.")
} catch VendingMachineError.OutOfStock {
    print("Out of Stock.")
} catch VendingMachineError.InsufficientFunds(let amountRequired) {
    print("Insufficient funds. Please insert an additional $\(amountRequired).")
}
```

- 위 예제에서 `vend(itemNamed:)` 는 오류를 던질 수 있기 때문에 `try` 표현식으로 호출된다.
- 오류가 던져지면, 실행(execution)은 즉시 `catch` 절로 전달되고, 전파를 계속할 지 결정한다.
- 오류가 던져지지 않으면, `snack` 에 `vend(itemNamed:)` 반환 값이 할당되고, `do` 문에 있는 남은 구문이 실행된다.

#### 오류 전파(Propagation) 막기

- 어떤 경우는 던지기 함수나 메소드가 실행중에 오류를 던지지 않을 것을 알 수 있다. 이런 경우, `try!`를 쓰는 강제 시도(*forced-try*) 표현식을 통해 던지기 함수나 메소드를 호출할 수 있다.
- `try!`를 써서 던지기 함수나 메소드를 호출하면, 오류 전파를 막고, 실행 중에 오류가 던져지지 않는다고 포장한다.
- 실제로 오류가 던져지면 런타임 오류가 발생한다.

```swift
func willOnlyThrowIfTrue(value: Bool) throws {
    if value { throw someError }
}

do {
    try willOnlyThrowIfTrue(false)
} catch {
    // Handle Error
}

try! willOnlyThrowIfTrue(false)
```

#### 정리(Clean-Up) 행동 지정

- `defer` 문을 사용해 일부 구문을 실행(execution)이 현재 코드 블럭을 떠나기 직전에 실행하도록 할 수 있다.
- 이것을 이용해 오류가 발생하든 안하든 실행해야 하는 정리 작업을 할 수 있다.
- 예를 들면, 열린 파일 서술자를 닫고, 직접 할당한 메모리를 해제하는 것이다.
- `defer` 문은 현재 스코프가 끝날 때까지 실행을 지연한다.
- 지연된 구문은 `break` 나 `return` 문 또는 오류를 던지는 것과 같이 문장 밖으로 제어를 넘기는 코드를 포함하지 않는다.
- 지연된 행동은 기술된 순서의 반대로 실행된다. 첫 번째 `defer` 문이 두 번째 `defer` 문 다음에 실행된다.

```swift
func processFile(filename: String) throws {
    if exists(filename) {
        let file = open(filename)
        defer {
            close(file)
        }
        while let line = try file.readline() {
            // Work with the file.
        }
        // close(file) is called here, at the end of the scope.
    }
}
```

- 위 예제에서 `defer` 문을 써서 `open(_:)` 문이 대응하는 `close(_:)` 문이 호출되는 것을 보장한다.
- 이 호출은 오류가 던져지든 않든 상관 없다.
