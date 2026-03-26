---
title: "Constraint"
date: "2026-03-26"
category: "Flutter"
---

# Constraint (제약)

- Single Pass : 한 번만 계산한다
- Constraints Go Down : 부모에서 자식으로 전가된다
- Sizes Go Up : 크기는 위로 올라간다
- Parent Sets Position : 부모가 위치를 정한다

크기의 제약을 의미하는데, 네 개의 값으로 정해져있다

1. Max Height
2. Min Height
3. Max Width
4. Min Width

위젯의 위치는 부모 위젯이 지정하기 때문에 자식 위젯은 자신이 어디에 위치될지 알 수 없다. 따라서 플러터에서 x, y 좌표로 위젯을 배치하지 않는 것이다.

자식 위젯이 어디에 정렬되어야 하는지 정확히 알 수 없는 경우에는 자식 위젯의 크기가 무시될 수 있다.
⇒ 따라서 대부분의 플러터 정렬은 `Align(alignment: Alignment)` 로 이루어진다.
