---
title: "Class Validator"
date: "2026-01-14"
category: "nestjs"
---
# Class Validator

```tsx
$ npm i class-validator
$ npm i class-transformer
```

## DTO

> Body의 값들을 하나의 클래스로 묶어서 관리하는 방법.
> 

```tsx
// 기존 코드
@Post()
postPosts(
	@User('id') userId: number,
	@Body('title') title: string,
	@Body('content') content: string
) {}
```

```tsx
// DTO 작성
// src/posts/dto/create-post.dto.ts
export class CreatePostDto {
	@IsString({
		message : "유효성 검사에 걸렸을 때 보여질 메세지"
	})
  title: string;
  
  @IsString()
  content: string;
}

// 또는 더 간단하게 PickType 사용해서 구현 가능
export class CreatePostDto extends PickType
(PostModel, ['title', 'content']) {}
```

```tsx
// 적용 코드
@Post()
postPosts(
	@User('id') userId: number,
	@Body() body: CreatePostDto,
) {}
```

```tsx
// main.ts 에 작성해서 모든 코드에 Validation 적용
async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.useGlobalPipes(new ValidationPipe())

  await app.listen(process.env.PORT ?? 3000);
}
```

## **Exclude Annotation**

> 특정 프로퍼티를 응답으로 보내고 싶지 않을 때 사용
> 

보통 특정 프로퍼티를 선택적으로 보내고 싶다면 릴레이션을 작성해서 값을 가져오도록 구현한다.

하지만 매번 일일이 이것들을 구현하지 않고 @Exclude Anootation 을 사용하면 기본적으로 응답을 보낼 때 포함시키지 않도록 할 수 있다.

이때 컨트롤러에서는 `@UseInterceptors(ClassSerializerInterceptor)` 를 추가해주어야 한다.

일일이 컨트롤러마다 추가하지 말고 app.module.ts 파일에 하나만 추가하면 된다.

+) 만약 기본적으로 모두 보이지 않도록 하고 일부만 선택적으로 보이게 하고 싶다면 그냥 클래스 자체를 `@Exclude()` 해주면 된다. 그리고 보여주고 싶은 것들만 `Expose()` 해주면 된다.

```tsx
// Entity
import { Exclude } from 'class-transformer';
  
  @Exclude()
  password: string;
```

```tsx
// Controller에 추가할 경우
 @Get()
  @UseInterceptors(ClassSerializerInterceptor)
  getUsers() {
    return this.usersService.getAllUsers();
  }
```

```tsx
// app.module.ts에 추가할 경우
  providers: [
    AppService,
    {
      provide: APP_INTERCEPTOR,
      useClass: ClassSerializerInterceptor,
    },
  ],
```

## 페이지네이션
