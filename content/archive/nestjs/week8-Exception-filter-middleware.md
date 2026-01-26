---
title: "Exception Filter & Middleware"
date: "2026-01-26"
category: "nestjs"
---

# Exception Filter (예외 필터)

모든 예외 처리는 `HttpException`을 포함한다.

## 구현

```tsx
@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse();
    const request = ctx.getRequest();
    const status = exception.getStatus();
    
    // 필요하다면 로그 모니터링 코드 작성

    response.status(status).json({
      statusCode: status,
      message: exception.message,
      timestamp: new Date().toLocaleString(),
      path: request.url,
    });
  }
}

// 만약 전역적으로 http 예외 필터를 적용하고 싶은 경우
// main.ts
app.useGlobalFilters(new HttpExceptionFilter())
```

# Middleware (미들웨어)

> Express에서 그대로 가져온 기능
> 

<img width="529" height="80" alt="image" src="https://github.com/user-attachments/assets/67128b90-67d2-4511-84f3-8982f6e341b6" />


클라이언트 사이드에서 요청을 넣으면 미들웨어를 거친 뒤에 라우트 핸들러로 전달된다.

## 구현

`path: ‘*’` 이렇게 작성하면 app 모듈을 통하는 모든 라우트에 middleware를 전부 적용한다.

```tsx
// 구현
@Injectable()
export class LogMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    const now = new Date();
    console.log(`[${now.toLocaleString('kr')}] ${req.method} ${req.originalUrl}`);
    next();
  }
}

// 모듈 파일에서 적용
// app.module.ts
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer.apply(LogMiddleware).forRoutes({
      path: '*',
      method: RequestMethod.ALL
    });
  }
}
```
