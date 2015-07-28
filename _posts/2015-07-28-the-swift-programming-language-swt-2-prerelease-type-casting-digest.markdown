---
layout: post
title: "타입 변환"
date: 2015-07-28 21:32:31
categories: swift study
tags: swift
---
# 스위프트 스터디 5주차 (2015.08.01)

**이 문서는 Apple의 _The Swift Programming Language (Swift 2 Prerelease)_ 의 요약입니다.**

## 타입 변환(Type Casting)

- 타입 변환(*Type Casting*)은 인스턴스의 타입을 확인하는 방법이다. 그리고/또는, 그 인스턴스를 자신의 클래스 계층에 속한 슈퍼 클래스 또는 서브 클래스인 것처럼 속이는 것이다.
- 스위프트에서 타입 변환은 `is` 또는 `as` 연산자를 써서 구현된다.
- 타입이 프로토콜을 따르는 지 확인할 때도 타입 변환을 쓸 수 있다.

### 타입 변환을 위한 클래스 계층 선언

``` swift
class MediaItem {
    var name: String
    init(name: String) {
        self.name = name
    }
}
```

``` swift
class Movie: MediaItem {
    var director: String
    init(name: String, director: String) {
        self.director = director
        super.init(name: name)
    }
}

class Song: MediaItem {
    var artist: String
    init(name: String, artist: String) {
        self.artist = artist
        super.init(name: name)
    }
}
```

``` swift
let library = [
    Movie(name: "Casablanca", director: "Michael Curtiz"),
    Song(name: "Blue Suede Shoes", artist: "Elvis Presley"),
    Movie(name: "Citizen Kane", director: "Orson Welles"),
    Song(name: "The One And Only", artist: "Chesney Hawkes"),
    Song(name: "Never Gonna Give You Up", artist: "Rick Astley")
]
// the type of "library" is inferred to be [MediaItem]
```

- 스위프트의 타입 체커(type checker)는 `Movie` 와 `Song` 가 공통 슈퍼 클래스인 `MediaItem` 을 가지고 있다고 추정할 수 있고, `library` 를 `[MediaItem]` 으로 추론한다.
- `library` 에 있는 아이템들은 여전히 `Movie` 와 `Song` 인스턴스다.
- 하지만 이 배열의 내용을 순회한다면, 아이템들은 `Movie` 나 `Song` 이 아닌 `MediaItem` 으로 반환 받는다.
- 그것들을 자신의 타입으로 사용하기 위해 타입을 확인(*check*) 하거나, 다른 타입으로 다운캐스트(*downcast*) 해야 한다.

### 타입 확인

- 타입 확인 연산자 (`is`)를 사용해 인스턴스가 특정 서브클래스인지 확인한다.
- 타입 확인 연산자는 인스턴스가 특정 서브클래스면 `true`를 반환하고, 아니면 `false` 를 반환한다.

``` swift
var movieCount = 0
var songCount = 0

for item in library {
    if item is Movie {
        ++movieCount
    } else if item is Song {
        ++songCount
    }
}

print("Media library contains \(movieCount) movies and \(songCount) songs")
// prints "Media library contains 2 movies and 3 songs"
```

### 다운캐스팅(Downcasting)

- 특정 클래스 타입의 상수나 변수는 실제로 서브클래스의 인스턴스를 가리키고 있을 수 있다.
- 이런 경우 타입 변환 연산자(*type cast operator*) (`as?` 또는 `as!`)를 사용해 서브클래스 타입으로 다운캐스트(*downcast*)를 시도할 수 있다.
- 다운캐스트는 실패할 수 있기 때문에 두 가지 형식이다.
- 조건 형식(conditional form), `as?`는 다운캐스트 하려는 타입의 옵셔널 값을 반환한다.
- 강제 형식(forced form), `as!`는 다운캐스트를 시도하고 결과를 강제추출(force-unwraps)한다.
- 다운캐스트가 성공할 지 확실하지 않을 때는 조건 형식 `as?`를 사용하라. 다운캐스트가 불가능할 때 그 값은 `nil`이 될 것이다.
- 다운캐스트가 항상 성공할 때만 강제 형식 `as!`를 사용하라. 잘못된 타입으로 다운캐스트를 시도하면 런타임 에러가 발생한다.

``` swift
for item in library {
    if let movie = item as? Movie {
        print("Movie: '\(movie.name)', dir. \(movie.director)")
    } else if let song = item as? Song {
        print("Song: '\(song.name)', by \(song.artist)")
    }
}

// Movie: 'Casablanca', dir. Michael Curtiz
// Song: 'Blue Suede Shoes', by Elvis Presley
// Movie: 'Citizen Kane', dir. Orson Welles
// Song: 'The One And Only', by Chesney Hawkes
// Song: 'Never Gonna Give You Up', by Rick Astley
```

> 변환(casting)은 인스턴스나 그 값을 실제로 변경하지 않는다. 기저의 인스턴스는 그대로 있고, 단순히 타입의 인스턴스로 속이고 접근한다.

### Any 와 AnyObject 로 타입 변환

- 스위프트는 불특정 타입으로 작업하기 위한 두 가지 특별한 타입 별칭(alias)을 제공한다.
  - `AnyObject` 는 어떤 클래스 타입의 인스턴스도 표현할 수 있다.
  - `Any` 는 함수 타입을 포함한 모든 타입의 인스턴스를 표현할 수 있다.

> `AnyObject` 와 `Any` 는 그것이 제공하는 행동이나 능력이 꼭 필요할 때만 사용하라. 코드에서 기대하는 타입에 대해 명확히 하는 것은 항상 좋다.

#### AnyObject

- 코코아 API로 작업을 할 때, `[AnyObject]` 타입의 배열을 받는 일은 흔하다. 이는 오브젝티브-C가 명시 타입 배열을 가지고 있지 않기 때문이다.
- 하지만 때로는 제공된 API에 관한 정보를 통해 각 배열에 포함된 객체의 타입을 확신할 수 있다.
- 이런 경우, 강제 타입 변환 연산자 (`as`) 를 사용해서 옵셔널 언래핑(optional unwrapping)을 사용하지 않고, 배열 안의 각 아이템을 `AnyObject` 보다 더 정확한 클래스 타입으로 다운캐스트 할 수 있다.

``` swift
let someObjects: [AnyObject] = [
    Movie(name: "2001: A Space Odyssey", director: "Stanley Kubrick"),
    Movie(name: "Moon", director: "Duncan Jones"),
    Movie(name: "Alien", director: "Ridley Scott")
]
```

``` swift
for object in someObjects {
    let movie = object as! Movie
    print("Movie: '\(movie.name)', dir. \(movie.director)")
}
// Movie: '2001: A Space Odyssey', dir. Stanley Kubrick
// Movie: 'Moon', dir. Duncan Jones
// Movie: 'Alien', dir. Ridley Scott
```

``` swift
for movie in someObjects as! [Movie] {
    print("Movie: '\(movie.name)', dir. \(movie.director)")
}
// Movie: '2001: A Space Odyssey', dir. Stanley Kubrick
// Movie: 'Moon', dir. Duncan Jones
// Movie: 'Alien', dir. Ridley Scott
```

#### Any

- `Any` 를 사용해서 함수 타입과 클래스가 아닌 타입을 포함한 복합 타입으로 작업할 수 있다.

``` swift
var things = [Any]()

things.append(0)
things.append(0.0)
things.append(42)
things.append(3.14159)
things.append("hello")
things.append((3.0, 5.0))
things.append(Movie(name: "Ghostbusters", director: "Ivan Reitman"))
things.append({ (name: String) -> String in "Hello, \(name)" })
```

- `switch` 문의 케이스(case)에서 `is` 와 `as` 연산자를 사용해서 `Any` 혹은 `AnyObject` 타입으로만 알고 있는 상수나 변수의 지정 타입을 알 수 있다.

``` swift

for thing in things {
    switch thing {
    case 0 as Int:
        print("zero as an Int")
    case 0 as Double:
        print("zero as a Double")
    case let someInt as Int:
        print("an integer value of \(someInt)")
    case let someDouble as Double where someDouble > 0:
        print("a positive double value of \(someDouble)")
    case is Double:
        print("some other double value that I don't want to print")
    case let someString as String:
        print("a string value of \"\(someString)\"")
    case let (x, y) as (Double, Double):
        print("an (x, y) point at \(x), \(y)")
    case let movie as Movie:
        print("a movie called '\(movie.name)', dir. \(movie.director)")
    case let stringConverter as String -> String:
        print(stringConverter("Michael"))
    default:
        print("something else")
    }
}

// zero as an Int
// zero as a Double
// an integer value of 42
// a positive double value of 3.14159
// a string value of "hello"
// an (x, y) point at 3.0, 5.0
// a movie called 'Ghostbusters', dir. Ivan Reitman
// Hello, Michael
```

> `switch` 문의 케이스는 타입을 확인하고 변환하기 위해 강제 타입 변환 연산자 (`as`, `as?` 아님) 를 사용합니다. 이 방법은 `switch` 케이스 문의 문맥 안에서 항상 안전합니다.
