---
layout: post
title: "중첩 타입 (Nested Types)"
date: 2015-07-28 23:13:14
categories: swift
tags: swift
---
# 스위프트 스터디 5주차 (2015.08.01)

**이 문서는 Apple의 _The Swift Programming Language (Swift 2 Prerelease)_ 의 요약입니다.**

## 중첩 타입 (Nested Types)

- 열거형은 종종 특정 클래스나 구조체의 기능을 돕기 위해 만들어진다. 마찬가지로, 더 복잡한 타입에서 사용하기 위한 유틸리티 클래스와 구조체의 정의를 편하게 한다.
- 이를 만족시키기 위해 스위프트에서는 중첩 타입(*nested types*)를 정의할 수 있다.
- 다른 타입 안에 또 다른 타입을 중첩하기 위해, 지원하는 타입의 바깥 중괄호 안에 정의를 작성한다.
- 타입은 필요한만큼 여러 단계로 중첩할 수 있다.

### 중첩 타입 사용 (Nested Types in Action)

```swift
struct BlackjackCard {

    // nested Suit enumeration
    enum Suit: Character {
        case Spades = "♠", Hearts = "♡", Diamonds = "♢", Clubs = "♣"
    }

    // nested Rank enumeration
    enum Rank: Int {
        case Two = 2, Three, Four, Five, Six, Seven, Eight, Nine, Ten
        case Jack, Queen, King, Ace
        struct Values {
            let first: Int, second: Int?
        }
        var values: Values {
            switch self {
            case .Ace:
                return Values(first: 1, second: 11)
            case .Jack, .Queen, .King:
                return Values(first: 10, second: nil)
            default:
                return Values(first: self.rawValue, second: nil)
            }
        }
    }

    // BlackjackCard properties and methods
    let rank: Rank, suit: Suit
    var description: String {
        var output = "suit is \(suit.rawValue),"
        output += " value is \(rank.values.first)"
        if let second = rank.values.second {
            output += " or \(second)"
        }
        return output
    }
}
```

```swift
let theAceOfSpades = BlackjackCard(rank: .Ace, suit: .Spades)
print("theAceOfSpades: \(theAceOfSpades.description)")
// prints "theAceOfSpades: suit is ♠, value is 1 or 11"
```

### 중첩 타입 참조 (Referring to Nested Types)

- 정의된 문맥 밖에서 중첩 타입을 사용하려면, 이름 앞에 그것을 포함하고 있는 타입의 이름을 놓는다.

```swift
let heartsSymbol = BlackjackCard.Suit.Hearts.rawValue
// heartsSymbol is "♡"
```
