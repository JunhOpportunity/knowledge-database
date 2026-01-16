---
title: "Service와 의존성 주입"
date: "2025-11-24"
category: "nestjs"
---

# Service

지금까지는 모든 코드를 controller.ts 파일에 작성했다.

하지만 현재는 프로젝트가 커지고 코드가 많아지게 되면 굉장히 관리하기 힘든 구조이기 때문에 각 엔드포인트에 해당하는 코드들을 service.ts에 함수로 만들어 저장하고 이를 호출해서 사용하는 방식으로 코드를 변경해야 한다.

이때 중요한 것이 의존성 주입과 제어의 역전이다.

직접 PostsService 라는 클래스를 생성하지 않았는데 `constructor(private readonly postsService: PostsService) {}` 이 코드만으로 컨트롤러와 서비스를 연결하였다.

# 의존성

## 의존성 주입

아래와 같은 경우 A는 B가 존재할 때만 생성할 수 있으므로 의존하고 있다고 볼 수 있다.

즉, B는 A가 생성될 때 주입된다.

```tsx
class A {
	constructor(instance: B)
}

class B {
}
```

주입을 시켜야 하는 클래스들을 모두 module.ts 파일의 providers 안에 넣어주면 된다.

이때 클래스 자체를 넣는다. 괄호를 붙이면 인스턴스가 된다는 것에 주의해야 한다.

코드를 보면서 이해를 해보자.

`posts.controller.ts` 의 @Controller(’posts’) 아래에 보면 다음과 같은 코드가 자동으로 작성되어져있다.

> constructor(private readonly postsService: PostsService) {}
> 

이 코드가 바로 Nest에서 자동으로 의존성을 주입해주는 부분이다.

이제 `posts.moudle.ts`에 가보면 controllers와 porivders가 존재한다.

```tsx
// posts.moudle.ts

@Module({
  controllers: [PostsController],
  providers: [PostsService],
})
export class PostsModule {}
```

여기서 Nest가 자동으로 의존성을 주입해주는 것들을 providers 에 넣어주면 자동으로 연결이 되는 것이다.

이제 이 개념이 posts 폴더 뿐만 아니라 전체에 적용된다고 보면 된다.

최상단에 존재하는 `app.module.ts` 파일에서는 `imports` 키워드도 확인할 수 있는데, 이때 imports 부분에서 posts 모듈을 연결한다고 보면 된다.

```tsx
// app.module.ts

@Module({
	imports: [PostsModule],
  controllers: [PostsController],
  providers: [PostsService],
})
export class PostsModule {}
```

그리고 최종적으로 main.ts 파일에서 앱을 생성할 때 AppModule을 실행하게 된다.

```tsx
// main.ts

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(process.env.PORT ?? 3000);
}
```
