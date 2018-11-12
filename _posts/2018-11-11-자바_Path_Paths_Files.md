---
title: 자바 Path & Paths & Files
categories: JAVA
tags: [JAVA, JAVA7]
---

자바 7에서 파일을 다루는 다른 방법으로 `java.nio.file.Path`, `java.nio.file.Paths`, `java.nio.file.Files` 의 인터페이스/클래스가 추가됨.
`Path` 는 파일의 경로를 가리키는 인터페이스
`Paths` 는 `Path` 인터페이스를 인스턴스화 하기 위한 유틸리티 클래스
`Files` 는 `Path` 를 가지고 이런저런 처리를 하기 위한 유틸리티 클래스
```java
Path root = Paths.get("/"); // 루트디렉토리
Path host = Paths.get("c:\\windows\\system32\\drivers\\etc\\hosts");
Path skinINfo = Paths.get("/home", "site", "id2", "skins", "skin_00", "skin_info.json"); // /home/site/id2/skins/skin_00/skin_info.json

// 경로 합치기
Path path = Paths.get("c:/windows/system32");
Path filePath = path.resolve("drivers/etc/hosts"); // == "c:\windows\system32\drivers\etc\hosts"

// 두 경로의 상대 경로
Path path1 = Paths.get("/home/site/id2/skins/skin_00");
Path path2 = Paths.get("/home/site/id2/skins/skin_00/css/main.css");
path1.relativize(path2); // == css/main.css
path2.relativize(path1); // == ../..

// 경로 정규화
Path crazyPath = Paths.get("c:/windows/system32/en/../drivers/./etc/hosts");
crazyPath.normalize(); // == c:\windows\system32\drivers\etc\hosts
```

`Path` 는 `File` 로 또는 그 반대로 간단히 전환 가능
```java
Path path = Paths.get("/home/site/id2/skins/skin_00/css/main.css");
File mainCssFile = path.toFile();

File commonJs = new File("/home/site/id2/skins/skin_00/js/common.js");
Path commonJsPath = commonJs.toPath();
```

`Files` 클래스에는 자바 7 이전버전에서 직접 코딩하거나 다른 라이브러리를 사용하여 처리하던 일들을 간단히 할 수 있는 메소드 들이 있음
```java
// 파일 카피/이동
Path srcPath = Paths.get("/home/site/id2/skins/skin_00/skin_info.json");
Path targetPath = Paths.get("/home/site/id2/skins/skin_01/skin_info.json");
Files.copy(srcPath, targetPath);
Files.move(srcPath, targetPath);

// 파일 읽기
List<String> lines = Files.readAllLines(srcPath);
for(String line : lines) {
  System.out.println(line);
}

String contents = new String(Files.readlAllBytes(srcPath), "UTF-8");
System.out.println(contents);

// 파일 쓰기
String str = "Java Files.Writer() example.\n1234567890";
Files.write(targetPath, str.toByte());
```
