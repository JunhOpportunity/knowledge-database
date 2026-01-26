---
title: "Interceptor (인터셉터)"
date: "2026-01-26"
category: "nestjs"
---
# Interceptor (인터셉터)

요청을  변경할수도 있고, 응답을 변경할수도 있음.

## 기본 구조

```tsx
@Injectable()
export class LogInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {

  }
}
```

## 구현

핸들이 실행되었을 때 response가 반환된다. 즉, 응답이 들어온다.

이때 반환된 것이 pipe의 tap내부에 있는 observable로 들어온다.

그렇기 때문에 observable을 통해서 response 값을 볼 수 있다.

- tap : 모니터링 할 수 있는 함수
- map : 변형 할 수 있는 함수

```tsx
@Injectable()
export class LogInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    // 요청이 들어올 때 REQ 요청이 들어온 타임스탬프를 찍는다.
    // [REQ] {요청 path} {요청 시간}
    const req = context.switchToHttp().getRequest();
    const path = req.originalUrl;
    const now = Date.now();
    console.log(`[REQ] ${path} ${now.toLocaleString('kr')}`);
    // 여기부터는 라우트의 로직이 전부 실행되고 응답이 observable로 반환된다.
    // 즉, 윗 부분은 로직이 실행되기 전에 요청 부분에서 실행하는 부분.
    // 리턴 부분은 응답 값을 실행하는 부분.
    return next.handle().pipe(
      tap((observable) => console.log(observable)),
      map((observable) => {
        return { message: '응답이 변경되었습니다', response: observable };
      }),
    );
    // 요청이 끝났을 때 (응답이 나갈 때) 다시 타임스탬프를 찍는다.
    // [RES] {요청 path} {응답 시간} {얼마나 걸렸는지 ms}
  }
}
```

## 활용

트랜잭션의 경우에 조건에 따라 커밋하거나 롤백하는데 이를 구현하기 위해 try-catch 문을 사용해야함.

하지만 인터셉터를 활용해서도 이 기능을 구현할 수 있음.

```tsx
@Injectable()
export class TransactionInterceptor implements NestInterceptor {
  constructor(private readonly dataSource: DataSource) {}
  async intercept(
    context: ExecutionContext,
    next: CallHandler,
  ): Promise<Observable<any>> {
    const req = context.switchToHttp().getRequest();

    // 1. 쿼리 러너 생성 (트랜잭션과 관련된 모든 쿼리 담당)
    const qr = this.dataSource.createQueryRunner();
    // 2. 쿼리 러너 연결
    await qr.connect();
    // 3. 쿼리 러너에서 트랜잭션 시작.
    // 이 시점부터 같은 쿼리러너를 사용하면
    // 트랜잭션 안에서 데이터베이스 액션 실행 가능
    await qr.startTransaction();

    req.queryRunner = qr;

    return next.handle().pipe(
      catchError(async (e) => {
        // 에러가 발생하면 롤백 수행
        await qr.rollbackTransaction();
        await qr.release();
        throw new InternalServerErrorException(e.message);
      }),
      tap(async () => {
        await qr.commitTransaction();
        await qr.release();
      }),
    );
  }
}

```
