---
title: "FireEvent VS UserEvent"
date: "2024-02-02"
category: "tdd"
---
# FireEvent VS UserEvent

## FireEvent

DOM 이벤트를 시뮬레이션 하기 위해 제공하는 API

testing-library 자체에서 제공하고 있어서 별도의 설치 없이 사용 가능하다.

fireEvent는 userEvent와 비슷하지만 단순히 동작에 대한 이벤트만 디스패치 한다.

fireEvent는 단순하게 해당 이벤트만 발생시킬 뿐 연쇄적으로 발생하는 이벤트에 대해서는 고려되지 않는다.(예를 들어 클릭 한 번에 pointerdown, mousedown, pointerup, mouseup, click, focuse 등의 이벤트가 연쇄적으로 발생)

따라서 현업에서는 fireEvent 보다 userEvent 사용을 선호하고 권장한다.

스크롤 이벤트처럼 userEvent에서 제공되지 않은 것을 테스트하거나 단순 컴포넌트를 테스트 하는 경우에는 fireEvent를 사용한다.

## UserEvent

disabled, display 상태도 고려하여 실제와 더 유사하게 테스트가 가능하다

실제 사용자가 사용하는 것처럼 이벤트가 연쇄적으로 발생하기 때문에 신뢰성이 향상된다
