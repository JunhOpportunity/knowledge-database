---
title: "Entity Annotation"
date: "2025-12-22"
category: "nestjs"
---

## Entity Annotation

- `@PrimaryGeneratedColumn()` : PK, 자동으로 ID를 생성
- `@PrimaryGeneratedColumn('uuid')` : 128비트 짜리 문자열을 생성
- `@PrimaryColumn()` : generated 가 아니기 때문에 무조건 직접 값을 넣어주어야 한다.
- `@Column()` : 칼럼 생성
- `@CreateDateColumn()` : 데이터 생성 날짜와 시간 자동으로 생성
- `@UpdateDateColumn()` : 데이터가 업데이트되는 날짜와 시간 자동으로 생성
- `@VersionColumn()` : 데이터가 업데이트 될 때마다 1씩 올라감 (처음 생성시 값은 1) 정확하게는 save() 함수가 몇 번 불렸는지 기억함
- `@Column()`
`@Generated(’increment’)` : 자동으로 1씩 증가하는 데이터

여기서 신경써야할 점이 있다. uuid 를 설정할 때는 number 타입이 아닌 string 타입으로 선언해주어야 한다는 것이다.

이렇게 하지 않으면 에러가 발생한다.

### `@Column({})` property

```tsx
@Column({ update: false })
  createdAt: Date;
```

- length : 입력할 수 있는 글자의 길이
- nullable : null 가능 여부
- update : 업데이트 가능 여부
- select : 데이터를 조회할 때 기본적으로 이 칼럼을 가져올 것인지 결정
- default : 기본값
- unique : PK처럼 중복되는 값이 없도록 하는 것

### enum

```tsx
enum RolesEnum {
	USER = 'user',
	ADMIN = 'admin',
}

@Column({
	type: 'enum',
	enum: Object.values(RolesEnum),
	default: Role.USER,
})
role: Role;
```

### Entity Embedding

> 상속과 비슷한 개념. 중복되는 내부 칼럼을 한 번에 작성해서 재사용한다.
이 방법보다는 상속을 많이 사용한다.
> 

이때 재사용 되는 것들을 선언하는 테이블의 경우에는 Entity 라는 키워드를 붙여주지 않는다.

```tsx
// 재사용되는 것들 선언
export class Name{
  @Column()
  first: string;

  @Column()
  last: string;
}

// 사용
  @Column(() => Name)
  name: Name;
```

### 테이블 상속

일반적인 상속과 똑같은 개념과 유사한 로직으로 이루어져있다.

Embedding 과 마찬가지로 @Entity 라는 키워드를 적어주지 않는다.

```tsx
export class BaseModel {
  @PrimaryGeneratedColumn()
  id: number;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;
}

@Entity()
export class BookModel extends BaseModel{
  @Column()
  name: string;
}
```
