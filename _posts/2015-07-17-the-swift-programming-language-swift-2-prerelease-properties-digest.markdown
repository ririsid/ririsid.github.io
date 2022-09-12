---
layout: post
title: 프로퍼티 (Properties)
date: 2015-07-17 23:47:48 +0900
tags: Swift
---

# 스위프트 스터디 3주차 (2015.07.18)

**이 문서는 Apple의 _The Swift Programming Language (Swift 2 Prerelease)_ 의 요약입니다.**

## 프로퍼티 (Properties)

- 프로퍼티는 값들을 각각의 클래스, 구조체, 열거형과 결합시킨다.
- 저장 프로퍼티(stored properties)는 상수와 변수를 인스턴스의 일부로 저장한다.
- 계산 프로퍼티(computed properties)는 값을 저장하는 대신 계산한다.
- 계산 프로퍼티는 클래스, 구조체, 열거형에서 쓸 수 있다.
- 저장 프로퍼티는 클래스와 구조체에서만 쓸 수 있다.

### 저장 프로퍼티

- 저장 프로퍼티는 변수와 상수 모두 쓸 수 있다.
- `var` 키워드를 사용하면 _저장 변수 프로퍼티(variable stored properties)_ 가 된다.
- `let` 키워드를 사용하면 _저장 상수 프로퍼티(constant stored properties)_ 가 된다.
- 저장 프로퍼티에 기본 값을 지정할 수 있다. _(다음 챕터 참조)_

#### 상수 구조체 인스턴스의 저장 프로퍼티

- 구조체 인스턴스를 상수로 할당했다면, 인스턴스의 **변수 프로퍼티** 까지 수정할 수 없다.
- 이는 구조체가 값 타입이기 때문이다.
- 클래스는 참조 타입이기 때문에 상수에 할당해도 변수 프로퍼티를 바꿀 수 있다.

#### 지연 저장 프로퍼티

- 지연 저장 프로퍼티는 한 번 사용되기 전에는 계산되지 않는다.
- `lazy` 식별자를 프로퍼티 선언 앞에 표시하면 사용할 수 있다.
- 지연 프로퍼티는 항상 변수로 선언해야 한다. 상수는 항상 초기화가 완료되기 전에 값을 가지기 때문이다.
- 지연 프로퍼티는 인스턴스의 초기화가 완료된 뒤까지 알지 못하는 외부 요인에 의존해 프로퍼티를 초기화할 때 유용하다.
- 또한, 사용하지 않거나 필요 없을 지도 모르면서 비싼 댓가를 치뤄야하는 프로퍼티를 초기화할 때도 유용하다.
- 다음 예제에서 `DataImporter` 는 초기화되지 않는다.

```swift
class DataImporter {
    /*
    DataImporter is a class to import data from an external file.
    The class is assumed to take a non-trivial amount of time to initialize.
    */
    var fileName = "data.txt"
    // the DataImporter class would provide data importing functionality here
}

class DataManager {
    lazy var importer = DataImporter()
    var data = [String]()
    // the DataManager class would provide data management functionality here
}

let manager = DataManager()
manager.data.append("Some data")
manager.data.append("Some more data")
// the DataImporter instance for the importer property has not yet been created
```

- 다음과 같이 접근하게 되면 초기화된다.

```swift
print(manager.importer.fileName)
// the DataImporter instance for the importer property has now been created
// prints "data.txt"
```

> 지연 프로퍼티를 여러 스레드에서 동시에 접근할 경우 한 번만 초기화된다고 보장하지 않는다.

#### 저장 프로퍼티와 인스턴스 변수

- 오브젝티브-C에서는 인스턴스 변수와 프로퍼티를 사용해서 값을 저장할 수 있었다.
- 스위프트는 혼란을 줄이기 위해 프로퍼티만 사용한다.

### 계산 프로퍼티

- 계산 프로퍼티는 값을 진짜로 저장하지 않는다. 대신, 간접적으로 다른 프로퍼티와 값을 사용할 수 있도록 게터와 세터를 제공한다.

```swift
struct Point {
    var x = 0.0, y = 0.0
}
struct Size {
    var width = 0.0, height = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        }
    }
}
var square = Rect(origin: Point(x: 0.0, y: 0.0),
    size: Size(width: 10.0, height: 10.0))
let initialSquareCenter = square.center
square.center = Point(x: 15.0, y: 15.0)
print("square.origin is now at (\(square.origin.x), \(square.origin.y))")
// prints "square.origin is now at (10.0, 10.0)"
```

#### 간편 세터 선언

- 세터는 설정하기 위한 값의 이름을 지정하지 않으면, 기본 값으로 `newValue` 를 사용한다.

```swift
struct AlternativeRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set {
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}
```

#### 읽기전용 계산 프로퍼티

- 계산 프로퍼티에 세터가 없으면 읽기전용 계산 프로퍼티가 된다.

> 계산 프로퍼티는 값이 고정이 아니기 때문에 변수로 만들어야만 한다.

- 읽기전용 계산 프로퍼티는 `get` 키워드를 생략할 수 있다.

```swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0
    var volume: Double {
        return width * height * depth
    }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
print("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
// prints "the volume of fourByFiveByTwo is 40.0"
```

### 프로퍼티 옵저버

- 프로퍼티 옵저버는 프로퍼티 값의 변화를 관찰한다.
- 프로퍼티 옵저버는 프로퍼티 값이 변할 때마다 호출된다. 새로운 값이 이전 값과 같아도 호출된다.
- 지연 저장 프로퍼티를 뺀 저장 프로퍼티에 프로퍼티 옵저버를 추가할 수 있다.
- 재정의(override)한 상속받은 프로퍼티(저장이든 계산이든)에도 프로퍼티 옵저버를 추가할 수 있다.

> 직접 구현한 계산 프로퍼티를 위해 프로퍼티 옵저버를 정의할 필요는 없다. 계산 프로퍼티의 세터에서 바로 관찰할 수 있기 때문이다.

- `willSet` 과 `didSet` 둘 중 하나 혹은 둘 다 포함할 수 있다.
- `willSet` 은 값이 저장되기 전에 호출된다.
- `didSet` 은 새 값이 저장된 직후에 호출된다.
- `willSet` 에는 새 값이 상수로 전달된다. 기본 값은 `newValue` 다.
- `didSet` 에는 이전 값이 상수로 전달된다. 기본 값은 `oldValue` 다.

> `willSet` 과 `didSet` 옵저버는 위임되지 않은 생성자(initializer)에서는 호출되지 않는다.

```swift
class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            print("About to set totalSteps to \(newTotalSteps)")
        }
        didSet {
            if totalSteps > oldValue  {
                print("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}
let stepCounter = StepCounter()
stepCounter.totalSteps = 200
// About to set totalSteps to 200
// Added 200 steps
stepCounter.totalSteps = 360
// About to set totalSteps to 360
// Added 160 steps
stepCounter.totalSteps = 896
// About to set totalSteps to 896
// Added 536 steps
```

### 전역과 지역 변수

- 계산 프로퍼티와 프로퍼티 옵저버의 기능은 전역 변수와 지역 변수에서도 쓸 수 있다.

> 전역 상수와 변수는 항상 _지연 저장 프로퍼티_ 처럼 지연 처리된다. 지연 저장 프로퍼티와는 달리 `lazy` 식별자를 붙이지 않는다.
> 지역 상수와 변수는 절대 지연 처리되지 않는다.

### 타입 프로퍼티

- 인스턴스 프로퍼티는 각 타입의 인스턴스에 속한다.
- 타입 자체에 프로퍼티를 지정할 수 있다. 이것을 타입 프로퍼티(_type properties_)라고 부른다.
- 값 타입(구조체, 열거형)에서는 저장 타입 프로퍼티와 계산 타입 프로퍼티를 쓸 수 있다.
- 클래스에서는 계산 타입 프로퍼티만 쓸 수 있다.
- 타입 자체는 생성자가 없기 때문에, 저장 인스턴스 프로퍼티와 달리 저장 타입 프로퍼티는 항상 기본 값을 제공해야 한다.

#### 타입 프로퍼티 문법

- `static` 키워드를 사용해서 타입 프로퍼티를 정의한다.
- 클래스의 계산 타입 프로퍼티는 하위 클래스에서 재정의할 수 있도록 `static` 대신 `class` 키워드를 사용할 수 있다.

```swift
struct SomeStructure {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 1
    }
}
enum SomeEnumeration {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 6
    }
}
class SomeClass {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 27
    }
    class var overrideableComputedTypeProperty: Int {
        return 107
    }
}
```

> 위 예제는 읽기전용 계산 타입 프로퍼티지만, 계산 인스턴스 프로퍼티와 같은 문법으로 읽기 쓰기 계산 타입 프로퍼티를 정의할 수 있다.

#### 타입 프로퍼티 읽고 쓰기

- 타입 프로퍼티는 인스턴스 프로퍼티와 마찬가지로 닷(.) 문법을 통해 읽고 쓸 수 있다.
- 하지만 타입 프로퍼티는 인스턴스가 아닌 타입에 읽고 쓴다.

```swift
print(SomeStructure.storedTypeProperty)
// prints "Some value."
SomeStructure.storedTypeProperty = "Another value."
print(SomeStructure.storedTypeProperty)
// prints "Another value."
print(SomeEnumeration.computedTypeProperty)
// prints "6"
print(SomeClass.computedTypeProperty)
// prints "27"
```
