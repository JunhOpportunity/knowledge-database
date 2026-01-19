---
title: "TypeORM 이론"
date: "2025-12-25"
category: "nestjs"
---

# Relationship

레퍼런스 하는 id 칼럼은 항상 many 입장의 테이블에 들어가게 된다.

둘 중 하나의 테이블은 상대방의 id 칼럼을 갖고 있어야 한다. (`@JoinColumn 사용`)

## One to One Relationship

<img width="514" height="94" alt="image" src="https://github.com/user-attachments/assets/1f033daf-7061-4ec6-b86f-d931997f3d4c" />


`@OneToOne` 키워드를 작성하고 해당 키워드의 프로퍼티에 연결할 칼럼을 명시해주면 된다.

여기서 주의할 점은 한 테이블은 상대방 태이블의 id를 갖고 있어야 한다는 점이다.

이때 이 역할을 명시해 주는 것이 `@JoinColumn()` 키워드이다.

```tsx
// Profile.entity.ts
@Entity()
export class ProfileModel{
  @OneToOne(() => UserModel, (user) => user.profile)
  @JoinColumn()
  user: UserModel;
}

// User.entity.ts
@Entity()
export class UserModel{
  @OneToOne(() => ProfileModel, (profile) => profile.user)
  profile: ProfileModel;
}
```

+) ✋ OneToOne 관계를 생성해서 연결한 뒤에 app.module.ts 의 forFeature 부분에 ProfileModel 도 추가해주어야함.

## One to Many & Many to One Relationship

<img width="518" height="83" alt="image" src="https://github.com/user-attachments/assets/6907a649-3724-4039-92d9-ed1cc97696fe" />


Post를 예시로 들었는데, Post의 경우 여러 Post가 한 사람에게 종속되기 때문에 Many는 Post 라는 것을 알 수 있다.

이 관계에서는 `@JoinColumn()` 키워드를 작성할 필요가 없다.

왜냐하면 이 관계에서는 무조건 Many 에 해당하는 테이블에서 id를 들고있게 되기 때문이다. (테이블은 배열 형태, 즉 한 셀에 여러 개의 데이터를 갖고 있을 수 없기 때문)

```tsx
// post.entity.ts
@Entity()
export class PostModel{
  @ManyToOne(() => UserModel, (user) => user.posts)
  author: UserModel;
}

// user.entity.ts
@Entity()
export class PostModel{
  @OneToMany(() => PostModel, (post) => post.author)
  posts: PostModel[];
}
```

여기서 주의할 점이 있다.

Relationship을 수행할 때는 연결된 테이블의 값들이 보여지는 것에 대한 여부가 기본값으로 false 값이기 때문에 보여주고 싶은 데이터가 있다면 추가해주어야한다.

```tsx
  @Get('users')
  getUsers() {
    return this.userRepository.find({
      relations: {
        profile: true,
        posts: true,
      },
    });
  }
```

## Many to Many Relationship

<img width="515" height="93" alt="image" src="https://github.com/user-attachments/assets/6568d1aa-4e83-4280-ac78-36fc92af1a2d" />

서로 여러 개를 Reference 하고 있다.

이 관계도 한 쪽에 Join 테이블을 생성해주어야 한다.

다만 이때는 어디에 작성해주든 상관이 없다.

```tsx
export class PostModel{
  @ManyToMany(() => TagModel, (tag) => tag.posts)
  @JoinTable()
  tags: TagModel[];
}

export class TagModel{
  @ManyToMany(() => PostModel, (post) => post.tags)
  posts: PostModel[];
}
```

<img width="236" height="64" alt="image" src="https://github.com/user-attachments/assets/3cf65879-07d7-4568-af4d-e4ae58b3cc91" />


이때 신기한 점은, 테이블에 추가적으로 칼럼이 하나 생긴 것이 아니라 새 테이블이 하나 생성되었다는 점이다.

각각의 테이블을 reference 하는 테이블을 새로 생성된 것을 확인할 수 있다.
