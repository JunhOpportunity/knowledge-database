---
title: "Pipe"
date: "2026-01-08"
category: "nestjs"
---
## Pipe

파라미터 값을 입력 받을 때 타입이 정확히 오지 않으면 에러를 발생시킨다.

즉, 유효성 검사를 해주는 것이라고 보면 된다.

추가적으로, 파이프는 데이터 타입을 변환해주는 역할을 하기도 한다.

```tsx
@Get(':id')
getPost(@Param('id', ParseIntPipe) id: number) {
	return this.postsService.getPostById(id);
}
```

### 커스텀 파이프

```tsx
// src/auth/pipe/password.pipe.ts
// 커스텀 파이프 생성

@Injectable()
export class PasswordPipe implements PipeTransform{
  transform(value: any, metadata: ArgumentMetadata) {
    if(value.toString().length < 8) {
      throw new BadRequestException('비밀번호는 최소 8자 이상이어야 합니다.');
    }
    
    return value.toString();
  }
}

// 커스텀 파이프 사용
 @Post('register/email')
  postRegisterEmail(
    @Body('email') email: string,
    @Body('password') password: string,
    @Body('nickname', PasswordPipe) nickname: string,
  ) {
    return this.authService.registerWithEmail({
      nickname,
      email,
      password,
    });
  }
```

<img width="433" height="155" alt="image" src="https://github.com/user-attachments/assets/3c459bc1-eecc-49fc-8ba6-35213347c609" />


이런 식으로 여러 개를 넣어 줄 수도 있다.

```tsx
// src/auth/pipe/password.pipe.ts

@Injectable()
export class MaxLengthPipe implements PipeTransform {
  constructor(private readonly length: number) {}

  transform(value: any, metadata: ArgumentMetadata) {
    if (value.toString().length > this.length) {
      throw new BadRequestException(`최대 길이는 ${this.length}자 입니다.`);
    }

    return value.toString();
  }
}

@Injectable()
export class MinLengthPipe implements PipeTransform {
  constructor(private readonly length: number) {}

  transform(value: any, metadata: ArgumentMetadata) {
    if (value.toString().length < this.length) {
      throw new BadRequestException(`최소 길이는 ${this.length}자 입니다.`);
    }

    return value.toString();
  }
}

// 커스텀 파이프 사용
@Body('password', new MaxLengthPipe(8), new MinLengthPipe(3))
```
