---
layout: post
title:  "문자열과 문자 (Strings and Characters)"
date:   2015-07-07 23:59:45 +0900
tags:   swift
---
# 스위프트 스터디 1주차 (2015.07.04)

**이 문서는 Apple의 _The Swift Programming Language (Swift 2 Prerelease)_ 의 요약입니다.**

## 문자열과 문자 (Strings and Characters)

- 스위프트의 문자열은 `String` 타입으로 표현된다.
- `String` 타입은 `Character` 타입 값의 묶음이다.
- 스위프트의 `String` 과 `Character` 타입은 빠르고, 유니코드를 준수한다.
- 모든 문자열은 인코딩 독립적인 유니코드로 구성되고, 다양한 유니코드 표현법으로 접근할 수 있다.

> 스위프트의 `String` 타입은 파운데이션 프레임워크의 `NSString` 클래스와 호환(bridged)된다. 파운데이션 프레임워크를 사용한다면 모든 `NSString` API를 `NSString` 으로 타입 캐스트한 `String` 값에서 호출할 수 있다. 또한, 모든 `NSString` 인스턴스가 필요한 API에 `String` 값을 사용할 수 있다.

### 문자열 리터럴

- 문자열 리터럴을 통해 `String` 값을 사용할 수 있다.
- 문자열 리터럴은 쌍따옴표로 둘러쌓인 연속된 문자이다.

{% highlight swift %}
let someString = "Some string literal value"
{% endhighlight %}

### 빈 문자열 만들기

- 빈 문자열 리터럴 또는 `String` 초기화 문법을 통해 빈 문자열을 만들 수 있다.

{% highlight swift %}
var emptyString = ""
var anotherEmptyString = String()
{% endhighlight %}

- 두 방법의 결과는 동일하다.
- `isEmpty` 속성을 통해 `String` 값이 빈 문자열인지 확인할 수 있다.

{% highlight swift %}
if emptyString.isEmpty {
	print("Nothing to see here")
}
{% endhighlight %}

### 문자열 변수

- `String` 값을 변수 또는 상수로 할당할 수 있다.

> 두 클래스를 사용하는 오브젝티브-C를 쓰는 코코아에서와 다른 접근법이다.

### 문자열은 값 타입이다

- 스위프트의 `String` 타입은 값 타입이다.
- 새 `String` 값을 생성하면 함수 또는 메소드에 넘기거나 상수나 변수에 할당할 때 복사된 값을 넘긴다.

> 코코아에서는 동일한 `NSString` 인스턴스를 참조한다.

- 직접 변경하지 않은 이상 `String` 값이 변경되지 않았다고 확신할 수 있다.
- 스위프트의 컴파일러는 반드시 복사가 필요할 때만 복사하도록 최적화한다. 이는 뛰어난 퍼포먼스를 의미한다.

### 문자 사용하기

- `for-in` 루프에 `String` 값의 `characters` 프로퍼티를 넣으면 각각의 `Character` 값에 접근할 수 있다.

{% highlight swift %}
for character in "Dog!🐶".characters {
	print(character)
}
{% endhighlight %}

- 단일 문자 리터럴에 `Character` 타입 주석을 붙여서 독립적인 `Character` 상수나 변수를 만들 수 있다.
- `Character` 값의 배열을 `String` 값의 초기화 메소드에 넘겨서 `String` 값을 만들 수 있다.

### 문자열과 문자 결합하기

- \+ 연산자를 사용해서 문자열을 더한 새로운 `String` 값을 만들 수 있다.

{% highlight swift %}
let string1 = "hello"
let string2 = " there"
var welcome = string1 + string2
{% endhighlight %}

- \+= 연산자도 사용할 수 있다.
- `String` 타입의 `append()` 메소드를 사용해서 `Character` 값을 덧붙일 수 있다.

> `String` 또는 `Character` 값을 `Character` 값에 덧불일 수 없다. `Character` 값은 하나의 문자만을 포함해야하기 때문이다.

### 문자열 삽입(interpolation)

- 문자열 삽입은 상수, 변수, 리터럴, 표현식이 섞여있는 문자열 리터럴에서 새로운 `String` 값을 만들어내는 방법이다.
- 문자열 리터럴에 백슬래시가 앞에 붙은 괄호로 둘러쌓인 값을 삽입한다.

{% highlight swift %}
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
{% endhighlight %}

> 괄호안에 이스케이프되지 않은 쌍따옴표 또는 백슬래시 그리고 캐리지 리턴과 라인 피드는 포함하지 못한다.

### 유니코드

- 유니코드는 다양한 쓰기 시스템에서 인코딩, 표시하기, 텍스트 처리를 하기 위한 국제 표준이다.
- 스위프트의 `String` 과 `Character` 타입은 완전히 유니코드를 따른다.

#### 유니코드 스칼라

- 스위프트의 `String` 타입은 유니코드 스칼라 값으로 만들어졌다.
- 유니코드 스칼라는 문자 또는 변경자를 위한 21비트 숫자이다.

- 모든 21비트 유니코드 스칼라에 문자가 할당되어 있지 않다. 일부는 나중을 위해 예약되어 있다.
- 스칼라에는 문자가 할당되어 있고 보통 *LATIN SMALL LETTER A* 같은 이름도 가지고 있다.

#### 스트링 리터럴에서의 특별한 문자

- 스트링 리터럴은 다음의 특별한 문자를 포함할 수 있다.
- \0 (널 문자), \\ (백슬래시), \t (가로 탭), \n (라인 피드), \r (캐리지 리턴), \" (쌍따옴표), \’ (홑따옴표)
- \u{n}로 쓰인 임의의 유니코드 스칼라. n은 유효한 유니코드 포인트와 같은 1-8자의 16진수.

{% highlight swift %}
let wiseWords = "\"Imagination is more important than knowledge\" - Einstein"
// "Imagination is more important than knowledge" - Einstein
let dollarSign = "\u{24}"        // $,  Unicode scalar U+0024
let blackHeart = "\u{2665}"      // ♥,  Unicode scalar U+2665
let sparklingHeart = "\u{1F496}" // 💖, Unicode scalar U+1F496
{% endhighlight %}

#### 확장 자소 클러스터(Extended Grapheme Clusters)

- 모든 스위프트의 `Character` 타입 인스턴스는 하나의 확장 자소 클러스터로 표현된다.
- 확장 자소 클러스터는 일련의 사람이 읽을 수 있는 문자(조합되었을 때)를 제공하는 하나 이상의 유니코드 스칼라이다.
- 예를 들어, *é* 는 하나의 유니코드 스칼라로 표현된다(*LATIN SMALL LETTER E WITH ACUTE* 또는 *U+00E9*). 하지만 같은 문자를 *e* (*LATIN SMALL LETTER E* 또는 *U+0065*)와 뒤따르는 *COMBINING ACUTE ACCENT* (*U+0301*) 쌍으로 표현할 수 있다.
- 두 가지 경우 모두 하나의 스위프트 `Character` 값으로 표현된다.

{% highlight swift %}
let eAcute: Character = "\u{E9}"                         // é
let combinedEAcute: Character = "\u{65}\u{301}"          // e followed by ́
// eAcute is é, combinedEAcute is é
{% endhighlight %}

- 확장 자소 클러스터는 조합 문자를 하나의 `Character` 값으로 표현한다. 예를 들어, 미리 조합된 문자와 분해된 문자 모두 하나의 스위프트 `Character` 값으로 표현된다.

{% highlight swift %}
let precomposed: Character = "\u{D55C}"                  // 한
let decomposed: Character = "\u{1112}\u{1161}\u{11AB}"   // ᄒ, ᅡ, ᆫ
// precomposed is 한, decomposed is 한
{% endhighlight %}

- 확장 자소 클러스터는 스칼라를 감쌀 수도 있다.

{% highlight swift %}
let enclosedEAcute: Character = "\u{E9}\u{20DD}"
// enclosedEAcute is é⃝
{% endhighlight %}

- 지역 표시 문자(regional indicator symbols)를 위한 유니코드 스칼라 한 쌍은 조합되어 하나의 `Character` 값을 만든다. *REGIONAL INDICATOR SYMBOL LETTER U* (*U+1F1FA*)와 *REGIONAL INDICATOR SYMBOL LETTER S* (*U+1F1F8*)

{% highlight swift %}
let regionalIndicatorForUS: Character = "\u{1F1FA}\u{1F1F8}"
// regionalIndicatorForUS is 🇺🇸
{% endhighlight %}

### 문자 세기

- 문자열의 `Character` 값의 수를 얻기 위해 문자열의 `characters` 프로퍼티의 `count` 프로퍼티를 사용한다.

{% highlight swift %}
let unusualMenagerie = "Koala 🐨, Snail 🐌, Penguin 🐧, Dromedary 🐪"
println("unusualMenagerie has \(unusualMenagerie.characters.count) characters")
// prints "unusualMenagerie has 40 characters"
{% endhighlight %}

- 스위프트의 확장 자소 클러스터를 사용한 `Character` 값은 문자열 덧붙이기나 변경이 항상 문자열의 길이에 영향을 주지는 않는다는 것을 의미한다.
- 예를 들어, 4문자 단어 *cafe* 에 *COMBINING ACUTE ACCECT* (*U+0301*)을 끝에 덧붙이면 문자 길이는 여전히 4이다.

{% highlight swift %}
var word = "cafe"
println("the number of characters in \(word) is \(word.characters.count)")
// prints "the number of characters in cafe is 4"

word += "\u{301}"    // COMBINING ACUTE ACCENT, U+0301

println("the number of characters in \(word) is \(word.characters.count)")
// prints "the number of characters in café is 4"
{% endhighlight %}

> 확장 자소 클러스터는 하나 이상의 유니코드 스칼라로 조합된다. 이는 같은 문자에 대한 다른 표현을 저장하기 위한 메모리 양이 다르다는 것을 의미한다.
> `characters` 프로퍼티와 `NSString` 의 `length` 와 항상 같지 않다.

### 문자열 접근과 수정

- 문자열의 메소드와 프로퍼티 또는 서브스크립트 문법을 통해 문자열에 접근하고 수정한다.

#### 문자열 인덱스

- 각각의 `String` 값은 포함하고 있는 각 `Character` 의 위치를 가리키는 `String.Index` 타입을 가지고 있다.
- 앞서 언급한 것처럼 각 문자는 서로 다른 양의 메모리가 필요하다. 그래서 `Character` 는 각자의 위치를 결정하기 위해 `String` 의 처음부터 끝까지 반복해야 한다. 이런 이유로 스위프트의 문자열은 정수로 색인되지 않는다.
- `startIndex` 프로퍼티는 `String` 의 첫 번째 `Character` 에 접근한다.
- `endIndex` 프로퍼티는 마지막 문자 다음을 반환한다.
- `String.Index` 값은 `predecessor()` 메소드를 호출해 직전 인덱스에 접근할 수 있고, `successor()` 메소드를 호출해 직후 인덱스에 접근할 수 있다.
- `String` 의 모든 인덱스는 이들 메소드를 연결하거나 전역 함수 `advance(start:n:)` 를 사용해서 접근할 수 있다.
- 문자열의 범위를 벗어난 인덱스에 접근하려고 시도하면 런타임 에러가 발생한다.
- `String` 의 각 인덱스에 위치한 `Character` 에 접근하기 위해 서브스크립트 문법을 사용할 수 있다.

{% highlight swift %}
let greeting = "Guten Tag"
greeting[greeting.startIndex]
// G
greeting[greeting.endIndex.predecessor()]
// g
greeting[greeting.startIndex.successor()]
// u
let index = advance(greeting.startIndex, 7)
greeting[index]
// a
greeting[greeting.endIndex] // error
greeting.endIndex.successor() // error
{% endhighlight %}

- 문자열의 각각의 문자에 접근하기 위한 모든 인덱스의 'Range' 를 만들기 위해 전역 함수 `indices(_:)` 를 사용한다.

{% highlight swift %}
for index in indices(greeting) {
    print("\(greeting[index])")
}
print("\n")
// prints "Guten Tag"
{% endhighlight %}

#### 삽입과 제거

- 문자열의 특정 인덱스에 문자를 삽입하기 위해, `insert(_:atIndex:)` 메소드를 사용한다.

{% highlight swift %}
var welcome = "hello"
welcome.insert("!", atIndex: welcome.endIndex)
// welcome now equals "hello!"
{% endhighlight %}

- 다른 문자열을 특정 인덱스에 삽입하기 위해, `splice(_:atIndex:)` 메소드를 사용한다.

{% highlight swift %}
welcome.splice(" there".characters, atIndex: welcome.endIndex.predecessor())
// welcome now equals "hello there!"
{% endhighlight %}

- 문자열에서 특정 인덱스의 문자를 제거하기 위해, `removeAtIndex(_:)` 메소드를 사용한다.

{% highlight swift %}
welcome.removeAtIndex(welcome.endIndex.predecessor())
// welcome now equals "hello there"
{% endhighlight %}

- 특정 범위의 문자열을 제거하기 위해, `removeRange(_:)` 메소드를 사용한다.

{% highlight swift %}
let range = advance(welcome.endIndex, -6)..<welcome.endIndex
welcome.removeRange(range)
// welcome now equals "hello"
{% endhighlight %}

### 문자열 비교

- 스위프트는 문자열과 문자 비교, 전위 비교, 후위 비교를 제공한다.

#### 문자와 문자 동일성

- == 연산자와 != 연산자로 동일성을 확인한다.

- 만약 두 `String` 값의 확장 문자 클러스터가 언어학적으로 같은 의미와 형태라면  같다고 간주한다.
- 반대로 영어에서 사용하는 *LATIN CAPITAL LETTER A* (*U+0041* 또는 *"A"*)와 러시아어에서 사용하는 *CYRILLIC CAPITAL LETTER A* (*U+0410* 또는 *"A"*)는 같지 않다.

> 스위프트의 문자열과 문자 비교는 로케일 무관하다.

#### 전위 후위 동일성

- 문자열의 `hasPrefix(_:)` 와 `hasSuffix(_:)` 메소드를 호출해서 비교한다.

### 문자열의 유니코드 표현

- 유니코드 문자열이 텍스트 파일이나 다른 저장소에 쓰여질 때, 문자열의 유니코드 스칼라는 유니코드 정의 인코딩 양식 중 하나로 부호화된다.
- 각 양식은 *코드 유닛* 으로 알려진 작은 단위로 문자열을 부호화한다.
- 이 양식은 UTF-8, UTF-16, UTF-32를 포함한다.

- 스위프트는 문자열의 유니코드 표현에 접근하는 몇 가지 방법을 제공한다.
- `for-in` 문에서 반복을 통해 각 확장 자소 클러스터 `Character` 값에 접근할 수 있다.
- 다른 방법으로는 `String` 값의 세 프로퍼티를 사용한다. *utf8*, *utf16*, *utf32*

#### 유니코드 스칼라 표현

- `String` 값의 *unicodeScalars* 프로퍼티를 반복해서, 유니코드 스칼라 표현에 접근할 수 있다.
- 이 프로퍼티의 타입인 *UnicodeScalarView* 는 *UnicodeScalar* 의 값의 묶음이다.
- 각 *UnicodeScalar* 는 *UInt32* 값으로 표현되는 스칼라의 21비트 값을 반환하는 *value* 프로퍼티를 가지고 있다.
