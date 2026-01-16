---
title: "TypeORM 이론"
date: "2025-11-29"
category: "nestjs"
---

## Typeorm

> 쉽게 테이블의 구조를 생성하기 위해서 사용함.
> 

### 생성 방법

1. 먼저 entity.ts 파일에 엔티티와 칼럼을 넣어주고 (PK 있어야 함 `PrimaryGeneratedColumn`)
2. app.module.ts 파일에 entities 에 새로 만든 클래스를 넣어준다.

```tsx
// src/posts/entities/posts.entity.ts

import { Column, Entity } from "typeorm";

@Entity()
export class PostModel {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  author: string;

  @Column()
  title: string;

  @Column()
  content: string;

  @Column()
  likeCount: number;

  @Column()
  commentCount: number;
}

// src/app.module.ts
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { PostsModule } from './posts/posts.module';
import { TypeOrmModule } from '@nestjs/typeorm';

@Module({
  imports: [
    PostsModule,
    TypeOrmModule.forRoot({
      type: 'postgres',
      host: '127.0.0.1',
      port: 5432,
      username: 'postgres',
      password: 'postgres',
      database: 'postgres',
      entities: [PostsModel],
      synchronize: true,
    })
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

<img width="217" height="241" alt="image" src="https://github.com/user-attachments/assets/27d74243-a6e6-46b5-9cbd-07b3bde8b0b9" />


이렇게 생성된 것을 확인할 수 있다.

이제 연결을 해주어야한다.

위에서 생성한 DB 테이블 클래스인 PostsModel을 NestJS와 연결하는 과정이다.

1. 이때 `posts.module.ts` 파일에서 `TypeOrmModule.forFeature` 코드를 사용하는 이유는 다음과 같다.

> "Nest야, 이 모듈(PostsModule)에서는 `PostsModel`이라는 설계도를 사용해서 DB에 접근할 거야. 그러니 이 모델을 관리할 수 있는 도구(Repository)를 준비해줘!"
> 
1. `posts.service.ts` 파일에서 Repository 주입하기

```tsx
// posts.module.ts

@Module({
  imports: [
    TypeOrmModule.forFeature([
      PostsModel,
    ])
  ],
  controllers: [PostsController],
  providers: [PostsService],
})
export class PostsModule {}

// posts.service.ts
@Injectable()
export class PostsService {
  constructor(
    @InjectRepository(PostsModel)
    private readonly postsRepository: Repository<PostModel>) {}
```

## GET

이제 getAllPosts 함수를 변경해보자.

```tsx
  async getAllPosts() {
    this.postsRepository.find();
  }
```

다음과 같이 Repository 를 사용하는데, 이때 find의 파라미터를 공백으로 넣으면 필터링을 거치지 않기 때문에 모든 데이터를 가져오게 된다.

다음은 getPostById 함수를 변경해보자.

MySQL 쿼리문처럼 조건을 찾기 위해서는 `where` 라는 키워드를 사용해야 한다.

`데이터 존재 값 : 비교 값` 형태로 코드를 작성해주면 된다.

(둘 다 변수명이 같을 경우에는 그냥 하나로 통합해서 써도 된다. id : id ⇒ id)

+) await 로 반환되기 때문에 Promise로 반환된다. 따라서 null이 아니기 때문에 에러가 잡히지 않는다. 이 부분을 주의해야한다.

```tsx
// 변경 전
  getPostById(id: number) {
    const post = posts.find((post: PostModel) => post.id === +id);

    if (!post) {
      throw new NotFoundException();
    }

    return post;
  }

// 변경 후
  async getPostById(id: number) {
    const post = await this.postsRepository.findOne({
      where: {
        id: id,
      },
    });

    if (!post) {
      throw new NotFoundException();
    }

    return post;
  }
```

## CREATE

- create 메서드 : 저장할 객체를 생성한다.
- save 메서드 : 객체를 저장한다. (create 메서드에서 생성한 객체로)

위에서 PostsModel 타입으로 데이터가 들어올 것이라고 선언해두었기 때문에 여기서는 바로 타입을 활용할 수 있게된다.

```tsx
  async createPost(author: string, title: string, content: string) {
    const post = this.postsRepository.create({
      author,
      title,
      content,
      likeCount: 0,
      commentCount: 0,
    });

    const newPost = await this.postsRepository.save(post);

    return newPost;
  }
```

이때 중요한 점은, 바로 id를 따로 입력하지 않았다는 점이다.

post 값으로 id를 입력하지 않은 채로 save를 했는데 postman을 확인해보면 id가 들어가있다.

자동으로 id가 생성이 된다는 것을 확인할 수 있었다.

<img width="542" height="466" alt="image" src="https://github.com/user-attachments/assets/b3395a8d-ac94-459b-a6ee-83a932a7a1e5" />


이게 어떻게 가능한걸까?

바로 PK를 entity 선언 부분에서 따로 정해주었기 때문이다.

<aside>
✋

### 그렇다면 만약 1, 2, 3 id를 할당한 뒤에 id 2에 해당하는 데이터를 지우면 다음에 생성되는 id는 빈 곳을 채우는 2가 될까 아니면 4가 될까?

⇒ 결론부터 말하자면 다음에 새로 생성되는 데이터의 id는 4가 된다.

왜냐하면 데이터베이스는 기본적으로 한 번 사용된 번호는 다시 쓰지 않는 것을 원칙으로 하기 때문이다!

좀 더 자세히 알아보자면, Entity 부분에서 id를 설정할 때 `@PrimaryGeneratedColumn()` 데코레이터를 사용했을 것이다. 이때 데이터베이스 내부적으로는 카운터가 돌아가게 된다.

1번 id를 생성했으면 카운터는 2가 되고 다음 2번 id를 생성하면 카운터는 3이되는 방식으로 동작한다.

만약 2번 id를 가진 데이터를 삭제해도 id는 3번까지 사용 되었으므로 카운터는 여전히 4일것이다.

따라서 다음에 생성될 데이터는 현재 카운터 값인 4를 할당받게 된다.

### 그렇다면 중간중간 빈 자리가 있기 때문에 낭비되는 부분이 생길수도 있고 id가 계속해서 삭제되고 쌓이다보면 오버플로우 같은 현상이 일어나지는 않을까?

⇒ 보통 4바이트 정수형을 쓰면 21억 번까지 생성할 수 있기 때문에 ID가 커지는 것에 대해서는 걱정하지 않아도 된다.

그리고 비어있는 ID를 채우고 싶다면 DB의 시퀀스를 강제로 초기화 해야 하는데, 실무에서는 거의 하지 않는 굉장히 위험한 작업이라고 한다.

게다가 예전에 2번 사용자가 쓴 댓글이나 결제 내역이 DB 어딘가에 남아있다면, 새로 가입한 2번 사용자가 그 내역을 보게 될 위험이 있다는 데이터 무결성 문제도 존재한다.

또한, 새로운 ID를 생성할 때 매번 중간에 빈 번호가 있는지 확인하는 것은 굉장히 비효율적이기 때문에 그냥 마지막 번호에 +1을 하는 것이 훨씬 빠르다.

</aside>

## UPDATE

- SAVE
    - 만약 데이터가 존재하지 않는다면 새로 생성한다.
    - 만약 같은 id 값을 가진 데이터가 존재한다면 해당 데이터를 업데이트한다.

```tsx
  async updatePost(postId: number, author: string, title: string, content: string) {
    const post = await this.postsRepository.findOne({
      where: {
        id: postId,
      },
    });

    if (!post) {
      throw new NotFoundException();
    }

    if (author) {
      post.author = author;
    }

    if (title) {
      post.title = title;
    }

    if (content) {
      post.content = content;
    }

    const newPost = await this.postsRepository.save(post);

    return newPost;
  }
```

<img width="544" height="524" alt="image" src="https://github.com/user-attachments/assets/29875fa1-e0fd-4e24-899c-f0371ee7d694" />


## DELETE

```tsx
  async deletePost(id: number) {
    const post = this.postsRepository.findOne({
      where: {
        id,
      },
    });

    if (!post) {
      throw new NotFoundException();
    }

    await this.postsRepository.delete(id);

    return id;
  }
```
