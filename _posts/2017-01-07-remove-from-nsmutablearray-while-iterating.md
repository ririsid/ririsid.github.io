---
layout: post
title: "NSMutableArray를 반복하며 객체 제거하기"
tags: objective-c swift
date: 2017-01-07 19:54:51
---

오브젝티브-C에서 배열을 반복하면서 조건에 따라 일부 객체를 제거해야할 때가 있다.
우선 배열을 `NSMutableArray`로 만들고, `for-in`문을 통해 바로 조건에 맞지 않는 객체를 제거하려고 했다.

```objective-c
NSArray<NSString *> *originalItems = @[@"Sam", @"John", @"Kevin", @"William"];
NSMutableArray<NSString *> *copiedItems = originalItems.mutableCopy;
for (NSString *item in copiedItems) {
    if (item.length > 4) {
        // 문자열의 길이가 4보다 크면 배열에서 제거한다.
        [copiedItems removeObject:item];
    }
}
```

이 코드를 실행하면 크래시가 발생한다. **콜렉션 타입이 반복중에 변경** 되었기 때문이다.
그래서 반복중에 변경하지 않는 방법으로 다시 만들었다.
조건에 맞지 않는 객체를 제거하지 않고, 반대로 조건에 맞는 객체만 새 배열에 저장해서 사용했다.

```objective-c
NSArray<NSString *> *originalItems = @[@"Sam", @"John", @"Kevin", @"William"];
NSMutableArray<NSString *> *newItems = [NSMutableArray new];
for (NSString *item in originalItems) {
    if (item.length <= 4) {
        // 문자열의 길이가 4보다 작거나 같으면 새 배열에 추가한다.
        [newItems addObject:item];
    }
}
originalItems = [NSArray arrayWithArray:newItems];
```

이 방법은 잘 동작하며, 현재 사용중인 방법이다.
하지만 글을 작성하면서 _이 방법이 최선일까?_ 하는 생각이 들어 검색해봤다.

```objective-c
NSArray<NSString *> *originalItems = @[@"Sam", @"John", @"Kevin", @"William"];
NSMutableArray<NSString *> *discardedItems = [NSMutableArray new];
for (NSString *item in originalItems) {
    if (item.length > 4) {
        // 문자열의 길이가 4보다 크면 삭제 아이템 배열에 추가한다.
        [discardedItems addObject:item];
    }
}
NSMutableArray<NSString *> *copiedItems = originalItems.mutableCopy;
[copiedItems removeObjectsInArray:discardedItems];
originalItems = [NSArray arrayWithArray:copiedItems];
```

새 방법은 제거할 아이템 배열을 만들고, `removeObjectsInArray:` 메소드를 사용해서 한꺼번에 제거한다.
코드는 조금 길어졌지만 의도가 좀 더 잘 반영된 코드다.
원본 배열의 타입이 `NSArray`가 아니라 `NSMutableArray`라면 코드도 길어지지 않는다.

## 스위프트

스위프트에서는 어떻게 할까?

```swift
var originalItems = ["Sam", "John", "Kevin", "William"]
originalItems = originalItems.filter { $0.characters.count <= 4 }
```

`filter` 메소드를 사용하면 직관적인 코드를 작성할 수 있다.

## 다시 오브젝티브-C

스위프트의 필터 방식을 오브젝티브-C에서 사용할 수 있을까?

```objective-c
NSArray<NSString *> *originalItems = @[@"Sam", @"John", @"Kevin", @"William"];
NSPredicate *predicate = [NSPredicate predicateWithFormat:@"SELF.length <= 4"];
originalItems = [originalItems filteredArrayUsingPredicate:predicate];
```

가능하다!

### 참고
- [http://stackoverflow.com/questions/111866/best-way-to-remove-from-nsmutablearray-while-iterating](http://stackoverflow.com/questions/111866/best-way-to-remove-from-nsmutablearray-while-iterating)
- [http://stackoverflow.com/questions/1728475/nsarray-remove-item-from-array](http://stackoverflow.com/questions/1728475/nsarray-remove-item-from-array)
- [http://stackoverflow.com/questions/19676371/how-to-filter-nsarray-using-predicate-on-an-object-property](http://stackoverflow.com/questions/19676371/how-to-filter-nsarray-using-predicate-on-an-object-property)
