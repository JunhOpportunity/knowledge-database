---
title: "옵저버 패턴"
date: "2024-02-17"
category: "디자인패턴"
series: "디자인패턴"
---

<img width="1032" height="576" alt="image" src="https://github.com/user-attachments/assets/bdccdade-a594-4477-8268-7195e2e255ae" />


# 옵저버 패턴

> 옵저버(관찰자)들이 관찰하고 있는 대상자의 상태가 변화가 있을 때마다 대상자는 직접 목록의 각 관찰자들에게 통지하고, 관찰자들은 알림을 받아 조치를 취하는 행동 패턴
> 

Observer 패턴에서 특정 객체를 구독할 수 있는데. 구독하는 주체를 Observer라 하고. 구독 가능한 객체를 Observable이라 한다. 이벤트가 발생할 때 마다 Observable은 모든 Observer에게 이벤트를 전파한다.

- `this.observers` : 이벤트가 발생할때마다 전파할 Observer들의 배열
- `subscribe()` : Observer를 Observer 배열에 추가
- `unsubscribe()` : Observer 배열에서 Observer를 제거
- `notify()` : 등록된 모든 Observer들에게 이벤트를 전파

```jsx
class Observable {
  constructor() {
    this.observers = []
  }

  subscribe(func) {
    this.observers.push(func)
  }

  unsubscribe(func) {
    this.observers = this.observers.filter(observer => observer !== func)
  }

  notify(data) {
    this.observers.forEach(observer => observer(data))
  }
}
```

만약 어떤 컴포넌트가 특정 데이터의 다운로드 완료 알림을 받기 원하거나, 사용자가 메시지 보드에 새로운 메시지를 게시했을 때 모든 멤버가 알림을 받거나 하는 등의 상황에 사용하면 유용하다.
