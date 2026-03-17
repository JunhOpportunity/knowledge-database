---
title: "Web view 프로젝트"
date: "2026-03-17"
category: "Flutter"
---

# 블로그 웹뷰 프로젝트

<aside>
✋

PC의 웹 자체를 그냥 핸드폰 버전으로 띄워주는 것을 의미한다.


</aside>

## Semantic Versioning

> 소프트웨어 버전 관리에 표준화된 규칙
> 

[Major].[Minor].[Patch]

만약 맨 앞에 ^(캐럿) 을 붙이면 패치 버전과 마이너 버전이 업데이트 될 경우 자동으로 최신 버전을 다운받아 사용하게 된다.

## 라이브러리 사용 방법

- [pub.dev](http://pub.dev) 사이트에서 검색 (NPM과 유사)
- pubspec.yaml 파일의 `dependencies: flutter:` 아래에 붙여넣기
- Pub get 버튼을 누르면 의존성 설치가 완료된다.

## AppBar(맨 위 네비게이션 바)

<img width="278" height="118" alt="image" src="https://github.com/user-attachments/assets/6341febd-f787-474b-ac47-184a6f8b6ae5" />


```dart
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Colors.orange,
        title: Text("AppBar"),
        centerTitle: true,
      ),
    );
  }
```

안드로이드는 왼쪽이 기본 정렬이고 IOS는 가운데가 기본 정렬이다.

따라서 맨 아래에 `centerTitle: true` 를 작성해준 것이다.

+) `final homeUrl = Uri.parse(’https://blog.codefactory.ai’)` Uri.parse를 사용하면 URL 주소를 URI 타입으로 변환해준다.

+) `WebViewController()..loadRequest(homeUrl);`  이렇게 .. 을 사용하면 맨 마지막에 사용된 함수의 결과값을 반환하라는 것이 아니라 함수의 실행 대상을 반환하는 것을 의미한다. 즉, `loadRequest`의 결과값을 반환하는 게 아니라 `WebViewController`를 반환한다는 의미이다.

```dart
// 원래 코드
final controller = WebViewController();
controller.loadRequest()

// .. 코드
final controller = WebViewController()..loadRequest();
```

## 홈 버튼 아이콘 생성 및 이동

홈 아이콘은 Icons.home 으로 제공해주고 있다.

이때 onPressed 함수는 콜백 함수이다. 즉, 버튼을 눌렀을 때만 동작하는 함수라는 것이다.

```dart
Widget build(BuildContext context) {
  return Scaffold(
    appBar: AppBar(
      backgroundColor: Colors.orange,
      title: Text("AppBar"),
      centerTitle: true,
      
      // 오른쪽 위에 홈 버튼을 만들고, 홈으로 이동하는 기능
      actions: [
        IconButton(onPressed: (){
          controller.loadRequest(homeUrl);
        }, icon: Icon(
          Icons.home
        ))
      ],
      
      
    ),
    body: WebViewWidget(controller: controller)
  );
}
```

## 안드로이드 자바스크립트 에러 방지

안드로이드에서는 유튜브 같은 영상을 재생하려고 할 때 자바스크립트 재생이 막혀있을 수 있다.

따라서 JavascriptMode 코드를 추가적으로 작성해야 한다.

```dart
  WebViewController controller = WebViewController()
  ..setJavaScriptMode(JavaScriptMode.unrestricted)
  ..loadRequest(homeUrl);
```
