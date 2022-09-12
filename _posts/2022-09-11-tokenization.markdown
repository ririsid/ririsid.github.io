---
layout: post
title: 토큰화
date: 2022-09-11 15:19:46 +0900
tags: Swift NaturalLanguage
---

## 토큰화

자연어 처리를 위해서는 문장을 의미있는 단위로 쪼개서 분석해야 한다. 이것을 토큰화(Tokenization)라고 한다.

`NaturalLanguage` 프레임워크를 사용하면 토큰화를 할 수 있다.

```swift
let text = """
iOS 16 enhances iPhone with all-new personalization features,
deeper intelligence, and more seamless ways to communicate
and share.
"""

let tokenizer = NLTokenizer(unit: .word)
tokenizer.string = text

tokenizer.enumerateTokens(in: text.startIndex..<text.endIndex) { tokenRange, _ in
    print(text[tokenRange])
    return true
}
```

출력 결과는 다음과 같다.

```plaintext
iOS
16
enhances
iPhone
with
all
new
personalization
features
deeper
intelligence
and
more
seamless
ways
to
communicate
and
share
```

### 한국어

영어를 사용할 때는 문제가 없지만 한국어를 사용하면 아쉬워진다.
영어는 단어(word)와 구두점(punctuation), 띄어쓰기(whitespace)로 나누어 토큰화가 가능하다.

한국어는 단어에 조사, 어미 등이 붙는 **교착어**로서 다른 형태소와 결합하여 사용되는 의존 형태소가 있어 **형태소(morpheme)**를 기반으로 토큰화 해야 자연어 처리가 쉬워진다.

위 코드에서 `text`를 한국어로 바꾸어 보았다.

```swift
let text = """
iPhone의 능력을 한 단계 더 끌어올려주는 iOS 16. 완전히 새로운
각종 사용자화 기능, 더 깊어진 지능 그리고 더욱더 매끄러운 소통 및
공유 방식을 선사합니다.
"""
```

출력 결과는 다음과 같다.

```plaintext
iPhone의
능력을
한
단계
더
끌어올려주는
iOS
16
완전히
새로운
각종
사용자화
기능
더
깊어진
지능
그리고
더욱더
매끄러운
소통
및
공유
방식을
선사합니다
```

결합된 형태소가 분리되지 않는다.

"능력"이라는 명사를 찾지 못하고 "능력은", "능력을"을 다른 단어로 인식한다는 것이다.

"iPhone의"는 내가 잘못 본 줄 알았다.

### 일본어

일본어도 한국어와 같은 교착어지만 형태소가 결합하는 경우가 적어서 그런지 상대적으로 토큰화가 잘 되는 것으로 보인다.

`text`를 일본어로 바꾸어 보았다.

```swift
let text = """
iOS 16、登場。まったく新しいカスタマイズ機能、より賢い知能、
コミュニケーションと共有を一段とシームレスにする方法が満載です。
あなたのiPhoneに、さらなる進化を。
"""
```

출력 결과는 다음과 같다.

```plaintext
iOS
16
登場
まったく
新しい
カスタマイズ
機能
より
賢い
知能
コミュニケーション
と
共有
を
一段
と
シームレス
に
する
方法
が
満載
です
あなた
の
iPhone
に
さらなる
進化
を
```

물론 "まったく", "まったい" 같이 어미가 다른 말이 구분되지 않는 한국어와 같은 문제가 있다.

### 품사 식별

품사 식별을 해보면 더 쉽게 알 수 있다.

```swift
let text = "A magical connection to your devices."
let tagger = NLTagger(tagSchemes: [.lexicalClass])
tagger.string = text
let options: NLTagger.Options = [.omitPunctuation, .omitWhitespace]
tagger.enumerateTags(in: text.startIndex..<text.endIndex, unit: .word, scheme: .lexicalClass, options: options) { tag, tokenRange in
    if let tag = tag {
        print("\(text[tokenRange]): \(tag.rawValue)")
    }
    return true
}
```

출력 결과는 다음과 같다.

```plaintext
A: Determiner
magical: Adjective
connection: Noun
to: Preposition
your: Determiner
devices: Noun
```

한국어로 `text`를 바꾼 결과는 다음과 같다.

```swift
let text = "기기에 바로 연결되는 매혹적인 경험."
```

```plaintext
기기에: OtherWord
바로: OtherWord
연결되는: OtherWord
매혹적인: OtherWord
경험: OtherWord
```

일본어로 `text`를 바꾼 결과는 다음과 같다.

```swift
let text = "あなたのデバイスとつながる。魔法のように。"
```

```plaintext
あなた: OtherWord
の: OtherWord
デバイス: OtherWord
と: OtherWord
つながる: OtherWord
魔法: OtherWord
の: OtherWord
よう: OtherWord
に: OtherWord
```

토큰화는 일본어가 한국어보다 조금 낫지만 품사 인식은 둘 다 안된다.

---

## 참고

- [Tokenizing natural language text](https://developer.apple.com/documentation/naturallanguage/tokenizing_natural_language_text)
- [Identifying parts of speech](https://developer.apple.com/documentation/naturallanguage/identifying_parts_of_speech)
