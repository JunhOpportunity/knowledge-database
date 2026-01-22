---
title: "Config 모듈"
date: "2026-01-22"
category: "nestjs"
---

# Config 모듈 사용하기

1. 환경 설정

```tsx
$ npm i @nestjs/config
```

1. .env 파일 작성
2. app.moudle.ts 수정
    
    ```tsx
    @Module({
      imports: [
        ConfigModule.forRoot({
          envFilePath: '.env',
          isGlobal: true,
        }),
      ]
    })
    ```
    
3. 적용

```tsx
@Injectable()
export class AuthService {
  constructor(
    private readonly configService: ConfigService,
  ) {}
  
  // ...
  
  secret: this.configService.get<string>(ENV_JWT_SECRET_KEY)
```

- 하드 코딩된 내용들도 .env 파일에서 관리하는 것이 좋다.
