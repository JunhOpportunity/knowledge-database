---
title: "유용한 메서드"
date: "2025-01-02"
category: "nestjs"
---
# 유용한 메서드

- create: 모델에 해당되는 객체 생성. 데이터베이스에 저장은 하지 않음
저장을 하고 싶다면 기존처럼 save 메서드 사용하면 됨.

```tsx
@Post('sample')
async sample() {
  // 저장은 하지 않음
	const user1 = this.userRepository.create({
		email: 'sample@abc.ai'
	});
	
	// 저장 함
	const user2 = this.userRepository.save({
		email: 'sample@abc.ai'
	});
}
```

- findAndCount : 페이지네이션 할 때 유용. 필터링에 해당하는 데이터들을 가져와서 보여주고, 전체 데이터가 몇 개인지도 보여줌.

```tsx
// 일단 20개의 데이터를 보여주고, 마지막에 전체 데이터의 개수를 보여줌
await this.userRepository.findAndCount('count', {
		take: 20,
})
```

- preload : 입력된 값을 기반으로 데이터베이스에 있는 데이터를 불러오고 추가 입력된 값으로 데이터베이스에서 가져온 값들을 대체함.
많이 사용하지는 않음.

```tsx
async sample() {
	const user3 = this.userRepository.preload({
		id: 101,
		email: 'sample@abc.ai'
	});
}
```

- delete

```tsx
async sample() {
	await this.userRepository.delete(
		101
	)
}
```

- increment : 해당 값을 특정 값 만큼 증가시킨다.

```tsx
// id가 1인 데이터에서 count 값을 2 증가시킨다
async sample() {
	await this.userRepository.increment({
			id: 1
		}, 'count', 2);
	)
}
```

- decrement: 해당 값을 특정 값 만큼 감소시킨다.

```tsx
// id가 1인 데이터에서 count 값을 3 감소시킨다
async sample() {
	await this.userRepository.decrement({
			id: 1
		}, 'count', 3);
	)
}
```

- count : 특정 값이 들어가있는 데이터의 갯수 카운팅하기

```tsx
async sample() {
	await this.userRepository.count({
		where: {
			email: ILike('%google%')
		}
	})
}
```

- sum : 다 더하는 것

```tsx
// where 조건들에 해당하는 데이터들의 count 값의 총합
await this.userRepository.sum('count', {
	where: {
		email: ILike('%google%')
	}
})
```

- average : 평균
- minimum : 최솟값
- maximum : 최댓값

```tsx
await this.userRepository.average('count', {
		where: {
			email: ILike('%google%')
		}
})
```
