---
layout: post
title: xcconfig 파일에서 URL 사용하기
date: 2022-09-14 22:45:41 +0900
tags: xcode xcconfig
---

_xcconfig_ 파일 안에서 `//`는 문맥과 상관 없이 주석으로 처리된다.

따라서 `https://example.com` 같은 URL은 `//example.com`이 주석으로 처리되어 설정에서 URL을 가져오면 `https:`만 가져오게 된다.

### 해결 방법 1

```other
URL = https:/$()/example.com
```

주석은 슬래시(/)가 연속으로 붙어있어야 하므로 빈 값(`$()`)을 중간에 끼어 넣으면 빌드 설정을 평가할 때 빈 값이 제거되면서 원하는 값을 얻을 수 있게 된다.

### 해결 방법 2

```other
URL = example.com
```

`https://`를 _xcconfig_ 파일에서 사용하지 않는다. 대신 설정에서 참조할 때 붙인다.

```other
https://${URL}
```

물론 이렇게 처리하면 개발 환경에 따라 [http\|https]가 나뉠 때 곤란해진다.

---

## 참고

[How do I configure full URLs in xcconfig files](https://stackoverflow.com/a/36297483/15639964)

[Adding a Build Configuration File to Your Project](https://developer.apple.com/documentation/xcode/adding-a-build-configuration-file-to-your-project)
