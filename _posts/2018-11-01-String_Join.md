---
layout: post
title:  "문자열 합치기
categories: 자바
---

자바에서 무언가 문자열을 합칠때,
일반적인 문자열(`String[]` 나, `List<String>`)은


```java
List<String> stringList = new ArrayList<>();
String result = "";
for(String str : stringList) {
  result += str;
}
```

등의 코드로 직접 합치거나
Apache Commons Lang 의 `StringUtil.join()` 메소드를 이용해 간단히 처리할 수 있음

자바 8 이후,
`String.join()` 메소드로도 간단히 처리 가능

```
String.join(",", "Apple", "Banana", "Corn");
String.join(",", new String[]{"A", "B", "C"});
String.join(",", stringList);
```


합쳐야할 데이터가 문자가 아닌 모델객체인 경우는 자바 8의 스트림을 이용해서 간단히 가능

```java
List<People> peopleList = new ArrayList<>();
String result = peopleList.stream()
    .map(people -> "His/her name is " + people.getName())
    .collect(joining(","));
```

중간에 무언가 구분자를 넣어줄 경우는 `StringUtil.join(",")` 와 같이 유틸리티 라이브러리를 사용하거나 Stream을 사용하면 편함.

끝!!
