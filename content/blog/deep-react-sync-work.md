---
title: "Sync Work를 처리하는 방법에 대해 알아보자."
date: "2025-09-30"
category: "React"
series: "deep-react"
description: "Fiber 아키텍처를 도입하면서 WORK의 우선순위 조정에도 변동이 생겼다. 이때 등장한 개념들에 대해 알아보자."
---

<img width="1120" height="624" alt="image" src="https://github.com/user-attachments/assets/2e007ca1-c82c-4506-92cf-6b2574977de5" />


# Sync WORK 를 처리하는 방법

Fiber 아키텍처를 도입하기 전, 그러니까 React 16 이전 버전에서는 수행중인 Work의 순서를 조정할 수 없었다.

그러나 Fiber 아키텍처를 도입하면서 수행중인 Work의 우선순위를 조정할 수 있고 취소도 할 수 있게 되었다.

## Reconciler와 Scheduler

여기서 Reconciler와 Scheduler 개념이 다시 등장한다.

| **Reconciler** | 어떤 작업(업데이트)이 필요한지 결정하는 React의 핵심 로직. |
| --- | --- |
| **Scheduler** | 우선순위(priority)와 시점을 관리해서 작업을 브라우저에 맞게 실행시키는 도우미. |

중요한 점은 Reconciler와 Scheduler는 둘이 협력하고 있지만 우선순위를 정하는 값이 서로 다르다는 점이다.

Reconciler에서 ImmediatePriority의 값이 99라면 Scheduler에서 ImmediatePriority의 값은 1이다.

따라서 우선순위의 변환이 필요하다.

## Async Work

```jsx
function scheduleCallback(reactPriorityLevel, callback, options) {
  const priorityLevel = reactPriorityToSchedulerPriority(reactPriorityLevel)
  return Scheduler_scheduleCallback(priorityLevel, callback, options)
}
```

Reconciler는 Scheduler에 **작업(callback)** 을 "이 우선순위로 실행해 주세요"라고 맡긴다.

그러면 Scheduler는 알아서 브라우저의 idle 타임 등에 맞춰 실행한다.

이렇게 맡긴 후에  Reconciler 더 이상 신경 쓰지 않는다.

## Sync Work

비동기처럼 Scheduler에 전부 맡기지 않고 Reconciler가 직접 관리한다.

동기 작업은 지금 당장 실행해야 하는 일이기 때문이다.

따라서 순차적으로 실행되므로 sync 작업을 위한 내부 Queue에 따로 저장해두고 실행한다.

```jsx
function scheduleSyncCallback(callback: SchedulerCallback) {
  // Push this callback into an internal queue. We'll flush these either in
  // the next tick, or earlier if something calls `flushSyncCallbackQueue`.
  if (syncQueue === null) {
    syncQueue = [callback]
    // Flush the queue in the next tick, at the earliest.
    immediateQueueCallbackNode = Scheduler_scheduleCallback(
      Scheduler_ImmediatePriority,
      flushSyncCallbackQueueImpl
    )
  } else {
    // Push onto existing queue. Don't need to schedule a callback because
    // we already scheduled one when we created the queue.
    syncQueue.push(callback)
  }
  return fakeCallbackNode // Task는 없으니 가짜를 반환
}
```

[[React 까보기 시리즈] Fiber 구조로 가능해진 Concurrent mode 와 Sync WORK 를 처리하는 방법?](https://www.youtube.com/watch?v=A6KGVmvEV-s&list=PLpq56DBY9U2B6gAZIbiIami_cLBhpHYCA&index=15)
