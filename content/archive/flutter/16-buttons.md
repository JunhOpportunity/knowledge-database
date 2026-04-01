---
title: "버튼에 대한 모든 것"
date: "2026-04-01"
category: "Flutter"
---

# 버튼

기본적으로 플러터에서 제공되는 버튼들은 모두 똑같다.

하지만 기본 스타일만 약간 다른 것 뿐이다.

- Elevated Button
- OutlineButton
- TextButton

```dart
ElevatedButton(
  // 아래에 콜백 함수 말고 null을 주면 disabled 된다
  onPressed: () {},
  style: ElevatedButton.styleFrom(
    // 배경 색깔
    backgroundColor: Colors.red,
    // 비활성화 시 색
    disabledBackgroundColor: Colors.green,
    // 배경 위의 색깔 (클릭 시, 글자 색상)
    foregroundColor: Colors.white,
    shadowColor: Colors.black,
    // 위로 좀 더 띄우기
    elevation: 10.0,
    textStyle: TextStyle(
      fontWeight: FontWeight.w700,
      fontSize: 20.0,
    ),
    padding: EdgeInsets.all(32.0),
    // 테두리 스타일
    side: BorderSide(color: Colors.black, width: 12.0),
  ),
  child: Text('Elevated Button'),
),
```

## Material State(= Widget State)

- hovered : 마우스 커서를 올려놓은 상태
- focused : 포커스 되었을 때
- pressed : 눌렀을 때
- dragged : 드래그 되었을 때
- selected : 선택 되었을 때
- scrollUnder : 다른 컴포넌트 밑으로 스크롤링 되었을 때
- disabled : 비활성화 되었을 때
- error : 에러 상태일 때

### MaterialStateProperty.all

위 Material State 중 어떤 상태가 오더라도 아래의 스타일을 취하겠다.

```dart
style: ButtonStyle(
  backgroundColor: MaterialStateProperty.all(Colors.red),
),
```

### MaterialStateProperty.resolveWith

버튼의 상태에 따라서 스타일을 따로 지정할 수 있다.

```dart
style: ButtonStyle(
  backgroundColor: WidgetStateProperty.resolveWith(
      (Set<WidgetState> states) {
        // 눌렀을 때 빨간색
        if(states.contains(WidgetState.pressed)) {
          return Colors.red;
        }
        // 기본 상태는 검은색
        return Colors.black;
      }
  ),
),
```

## Shape

- StadiumBorder() : 양쪽이 둥근 모양
- RoundedRectangleBorder() : 직사각형

```dart
RoundedRectangleBorder(
	borderRadius: BorderRadius.circular(16.0) 
)
```

- BeveledRectangleBorder() : 양쪽이 뾰족함
- ContinuousRectangleBorder() : 모서리가 둥근 직사각형
- CircleBorder() : 원형

## 아이콘 버튼

```dart
ElevatedButton.icon(
	icon: Icon(
		Icons.keyboard_alt_outlined,
	)
)
```
