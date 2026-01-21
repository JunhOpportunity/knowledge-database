---
title: "페이지네이션"
date: "2026-01-20"
category: "nestjs"
---

# 페이지네이션

> 많은 데이터를 부분적으로 나눠서 불러오는 기술
> 
- Page Based Pagination : 페이지 숫자를 누르면 다음 페이지로 넘어갈 때 많이 사용. 알고리즘이 매우 간단하지만 데이터가 추가되거나 삭제되는 경우 누락되는 데이터가 발생할 수 있음.
- Cursor Based Pagination : 가장 최근에 가져온 데이터를 기준으로 다음 데이터를 가져옴. 스크롤 형태의 페이지에서 많이 사용.

## Cursor Based Pagination

1. dto 작성 (프로퍼티)

```tsx
import { IsIn, IsNumber, IsOptional } from 'class-validator';

export class PaginatePostDto {
  // 이전 마지막 데이터의 ID
  // 이 프로퍼티에 입력된 ID 보다 높은 ID 부터 값 가져오기
  @Type(() => Number)
  @IsNumber()
  @IsOptional()
  where__id_more_than?: number; // 아무것도 없으면 0이라는 가정.

  // 정렬
  @IsIn(['ASC'])
  @IsOptional()
  order__createdAt?: 'ASC' = 'ASC';

  // 몇 개의 데이터를 응답으로 가져올지
  @IsNumber()
  @IsOptional()
  take: number = 20;
}
```

+) 추가적으로 알아둘 Annotation

```tsx
// 무조건 아래 리스트에 해당하는 값이 들어와야만 유효성 검사에 통과한다.
@IsIn(['ASC', 'DESC'])
order__createdAt?: 'ASC';

// Type을 자동으로 Number로 바꿔줌.
@Type(() => Number)

// main.ts 에서 글로벌 파이프에 전체적으로 임의 변경을 허용해도 된다.
  app.useGlobalPipes(
    new ValidationPipe({
      transform: true,
      transformOptions: {
        enableImplicitConversion: true,
      },
    }),
  );
```

2. service 함수 작성

```tsx
// posts.service.ts
@Injectable()
export class PostsService {
  getAllPosts() {
    return `This action returns all posts`;
  }

  async paginatePosts(dto: PaginatePostDto) {
    const posts = await this.postsRepository.find({
      where: {
        // 더 크다, 더 많다
        id: MoreThan(dto.where__id_more_than ?? 0),
      },
      order: {
        createdAt: dto.order__createdAt,
      },
      take: dto.take,
    });
  }
}
```

3. 컨트롤러 작성

```tsx
  @Get()
  getPosts(
    @Query() query: PaginatePostDto,
  ) {
    return this.postsService.paginatePosts(query);
  }
```

## Page based pagination
