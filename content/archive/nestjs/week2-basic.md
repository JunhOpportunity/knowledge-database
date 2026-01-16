---
title: "REST API와 요청/응답 구현"
date: "2025-11-20"
category: "nestjs"
---

# Nest 기본

### 응답 설정 : `src/app.controller.ts`

컨트롤러 내부에서 @Get, @Post 등으로 먼저 지정한 뒤에 응답 함수를 작성한다.

```sql
  @Get('test') // 파라미터는 경로를 의미
  getHello() {
    return 'Home Page';
  }
```

```sql
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()
  getHello() {
    return 'Home Page';
  }

  @Get('post')
  getPost() {
    return 'Post Page';
  }

  @Get('user')
  getUser() {
    return 'User Page';
  }
}
```

### 요청 라이프 사이클

> 요청이 보내진 다음에 응답으로 돌아오는 것 까지의 과정
> 

Middleware, Guard, Interceptor, Pipe 는 필수 사항이 아니다.

하지만 요청 로직을 처리하는 부분인 Controller, Service, Repository는 필수사항이다.

그 후에 Exception Filter와 Interceptor을 거쳐서 응답이 반환된다.

### 응답 보내기

아래와 같은 경우 @Controller 에서 엔드 포인트인 a, @Get 에서 엔드 포인트인 b 를 설정해주었으므로 아래의 getPost 요청을 보내기 위해서는 domain/a/b 경로로 요청을 보내야 한다.

+) 실제로 프로젝트를 구현할 때는 app.controller.ts 에 이것들을 적지 않는다. interface 별로 모듈을 만들어서 구현한다.

```sql
@Controller('a')
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get('b')
  getPost(): Post {
    return {
      author: 'author',
      title: 'title',
      content: 'content',
      likeCount: 100000,
      commentCount: 100,
    };
  }
}
```

### 모듈 생성하기

Nest는 CLI를 통해 편리하고 빠르게 모듈을 생성할 수 있다.

```sql
$ nest g resource
$ 모듈 이름
$ REST API
$ No
```

## REST API

> GET 요청은 Query를 사용하고 나머지는 전부 Body를 사용한다.
> 
- [GET] http://localhost:3000/posts ⇒ 다수의 posts를 가져온다.
- [GET] http://localhost:3000/posts/11 ⇒ 11이라는 ID를 갖고 있는 post 하나를 가져온다.
- [POST] http://localhost:3000/posts ⇒ 새로운 post를 생성한다.
- [PATCH] http://localhost:3000/posts/8 ⇒ 8이라는 ID를 갖고 있는 post를 부분 변경한다. (변경하고 싶은 부분만 Body에 넣어준다.)
- [PUT] http://localhost:3000/posts/8 ⇒ 8이라는 ID를 갖고 있는 post를 변경하거나 생성한다. (없으면 생성하기 때문에 전체를 모두 Body에 넣어야 한다.)
- [DELETE] http://localhost:3000/posts/3 ⇒ 3이라는 ID를 갖고 있는 post를 삭제한다.

### 특정 ID에 대한 요청

특정 ID에 대한 요청인 경우에는 `/posts/:id` 이렇게 작성한다.

```sql
@Get(':id')
getPost(@Param('id') id : string){
  return posts.find((post:PostModel) => post.id === +id)
}
```

### Not Found (기본으로 제공되는 Exception 사용하면 된다.)

```sql
if(!post) {
  throw new NotFoundException(); 
}
```

### POST 구현

바디에서 입력받은 데이터들을 새 포스트로 입력해야 하므로 @Body 를 사용해서 변수로 만든다.

```tsx
@Post()
postPosts(
  @Body('author') author: string,
  @Body('title') title: string,
  @Body('content') content: string,
) {
  const post: PostModel = {
    id: posts[posts.length - 1].id + 1,
    author,
    title,
    content,
    likeCount: 0,
    commentCount: 0,
  };
  posts = [...posts, post];
  return post;
}
```

### PUT 구현

```tsx
@Put(':id')
putPost(
  @Param('id') id: string,
  @Body('author') author?: string,
  @Body('title') title?: string,
  @Body('content') content?: string,
) {
  const post = posts.find((post) => post.id == +id);
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
  posts = posts.map((prevPost) => (prevPost.id === +id ? post : prevPost));
  return post;
 }
```

### DELETE 구현

```tsx
@Delete(':id')
deletePost(@Param('id') id: string) {
  const post = posts.find((post: PostModel) => post.id === +id);
  if (!post) {
    throw new NotFoundException();
  }
  posts = posts.filter((post) => post.id !== +id);
  return id;
}
```
