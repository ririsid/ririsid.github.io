---
layout: post
title:  "NSDictionary의 키에 값이 있는지 확인하기"
date:   2017-01-22 16:25:00 +0900
tags:   objective-c swift
---
오브젝티브-C에서 `NSDictionary`의 특정 키에 해당하는 값이 있는지 확인해야 할 때가 있다.
주로 서버에서 가져온 JSON 데이터에서 값을 추출할 때 필요하다.

전통적인 권장 방법은 `objectForKey` 메소드를 사용하는 것이다.

{% highlight objc %}
NSDictionary<NSString *, NSString *> *married = @{@"Sam" : @YES,
                                                  @"John" : @YES,
                                                  @"Kevin" : @YES,
                                                  @"William" : @NO};
NSString *key = @"John";
if ([married objectForKey:key]) {
    NSString *log = [key stringByAppendingString:@" is a member."];
    NSLog(@"%@", log);
} else {
    NSString *log = [key stringByAppendingString:@" is not a member."];
    NSLog(@"%@", log);
}
{% endhighlight %}

이 방법을 사용하면 크래시 없이 키에 값이 있는지 확인할 수 있다.

현대적인 오브젝티브-C에서는 서브크립팅(`[]`) 문법으로 조금 더 편하게 사용할 수 있다.

{% highlight objc %}
// 중복 코드 생략
if (married[key]) {
    NSString *log = [key stringByAppendingString:@" is a member."];
    NSLog(@"%@", log);
} else {
    NSString *log = [key stringByAppendingString:@" is not a member."];
    NSLog(@"%@", log);
}
{% endhighlight %}

첫 예제와 같은 동작을 하는 코드로 문법만 다르기 때문에 마찬가지로 크래시 없이 사용할 수 있다.

## JSON 데이터와 NSNull

다만, JSON 데이터를 받을 때 의도와 다른 경우가 발생할 수 있다.
`NSDictionary`는 `nil`을 값으로 가질 수 없기 때문에 키는 있고 값이 없는 경우, `[NSNull null]`이 값으로 할당되고는 한다.
**오브젝티브-C에서 모든 객체는 조건문에서 `true`로 평가** 되기 때문에 `[NSNull null]` 역시 `true`로 평가된다. `@0` 혹은 `@NO` 역시 마찬가지이다.
따라서 값이 `[NSNull null]`인 키는 조건문에서 값이 있다고 평가된다. 하지만 일반적으로는 값이 없다고 평가되어야 올바른 로직을 구현할 수 있다.
그래서 보통 다음과 같은 매크로를 사용한다.

{% highlight objc %}
#define isNull(value) value == nil || [value isKindOfClass:[NSNull class]]

NSDictionary<NSString *, NSString *> *married = @{@"Sam" : @YES,
                                                  @"John" : [NSNull null],
                                                  @"Kevin" : @YES,
                                                  @"William" : @NO};
NSString *key = @"John";                        
if (isNull(married[key])) {
    NSString *log = [key stringByAppendingString:@" is a member."];
    NSLog(@"%@", log);
} else {
    NSString *log = [key stringByAppendingString:@" is not a member."];
    NSLog(@"%@", log);
}
{% endhighlight %}

## 스위프트

스위프트에서는 어떻게 할까?
스위프트의 `Dictionary` 타입은 `nil`을 값으로 가질 수 있으므로 다음과 같은 코드를 만들었다.

{% highlight swift %}
let married = ["Sam": true,
               "John": nil,
               "Kevin": true,
               "William": false]
let key = "John"
if married[key] != nil {
    print("\(key) is a member.")
} else {
    print("\(key) is not a member.")
}
{% endhighlight %}

결과는 예상과 달리 _John is a member._ 다.
문제가 뭘까? `married`의 타입을 확인하면 `[String : Bool?]`이다. 값이 옵셔널 `Bool`이기 때문에 문제가 생겼다. 옵셔널을 언랩 해야만 원하는 결과를 얻을 수 있다.

{% highlight swift %}
// 중복 코드 생략
if let value = married[key], value != nil {
    print("\(key) is a member.")
} else {
    print("\(key) is not a member.")
}
{% endhighlight %}

이제 결과는 _John is not a member._ 가 되었다.

### 참고
- [http://stackoverflow.com/questions/2784648/how-to-check-if-an-nsdictionary-or-nsmutabledictionary-contains-a-key](http://stackoverflow.com/questions/2784648/how-to-check-if-an-nsdictionary-or-nsmutabledictionary-contains-a-key)
