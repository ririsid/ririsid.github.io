---
layout: post
title: "언어 인식"
date: 2022-09-10 23:14:10 +0900
tags: swift nlp
---

## 언어 인식

`NaturalLanguage` 프레임워크를 통해 언어를 인식할 수 있다.

다음 코드는 가장 가능성이 높은 언어 식별자 하나를 알 수 있다.

```swift
import NaturalLanguage

// Create a language recognizer.
let recognizer = NLLanguageRecognizer()
recognizer.processString("This is a test, mein Freund.")

// Identify the dominant language.
if let language = recognizer.dominantLanguage {
    print(language.rawValue)
} else {
    print("Language not recognized")
}
```

출력 결과는 다음과 같다.

> en

### 가능한 언어 목록

가능한 언어를 더 많이 알 수도 있다.
다음은 최대 2개의 언어 식별자와 확률을 알 수 있다.

```swift
// Generate up to two language hypotheses.
let hypotheses = recognizer.languageHypotheses(withMaximum: 2)
print(hypotheses)
```

출력 결과는 다음과 같다.

> [__C.NLLanguage(_rawValue: en): 0.7789781093597412, __C.NLLanguage(_rawValue: de): 0.13277797400951385]

### 제약 추가

제약을 만들 수도 있다.
언어 목록을 제한할 수도 있고, 언어별 확률을 추가할 수도 있다.

```swift
// Specify constraints for language identification.
recognizer.languageConstraints = [.french, .english, .german,
                                  .italian, .spanish, .portuguese]

recognizer.languageHints = [.french: 0.5,
                            .english: 0.9,
                            .german: 0.8,
                            .italian: 0.6,
                            .spanish: 0.3,
                            .portuguese: 0.2]

let constrainedHypotheses = recognizer.languageHypotheses(withMaximum: 2)
print(constrainedHypotheses)
```

출력 결과는 다음과 같다.

> [__C.NLLanguage(_rawValue: de): 0.13059720396995544, __C.NLLanguage(_rawValue: en): 0.8619569540023804]

### 초기화

다른 문자열을 처리하기 전에는 초기화해야 한다.

```swift
// Reset the recognizer to its initial state.
recognizer.reset()
```

---

## 참고

[Apple Developer Documentation](https://developer.apple.com/documentation/naturallanguage/identifying_the_language_in_text)
