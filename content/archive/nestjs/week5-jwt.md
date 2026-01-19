---
title: "JWT와 암호화"
date: "2026-01-06"
category: "nestjs"
---
# JWT

## Session

Session ID는 데이터베이스에 저장되기 때문에 요청이 있을때마다 매번 데이터베이스를 확인해야 한다.

서버에 데이터가 저장되기 때문에 클라이언트에 사용자 정보가 노출될 위험이 없다.

데이터베이스에 Session을 저장해야하기 때문에 Horizontal Scaling이 어렵다.

## JWT

> 유저의 정보를 Base 64로 인코딩된 String 값에 저장하는 도구
> 

Header, Payload, Signature 로 구성

JWT 토큰 ID를 같이 보내면 현재 요청을 보내는 사용자가 누구인지 서버에서 알 수 있다.

JWT 토큰은 데이터베이스에 저장되지 않고 Signautre 값을 이용해서 검증할 수 있다. 따라서 Session처럼 검증 시 매번 데이터베이스를 들여다 볼 필요가 없다.

정보가 모두 토큰에 담겨있고, 클라이언트에서 토큰을 저장하기 때문에 정보 유출의 위험이 있다.

데이터베이스가 필요 없기 때문에 Horizontal Scaling이 쉽다.

## Refresh Token & Access Token

두 토큰 모두 JWT 기반

Access Token은 대부분의 API 요청을 할 때 검증용 토큰으로 사용된다.

Refresh Token은 Access Token을 다시 발급할 때 사용한다.

Access Token은 자주 노출되기 때문에 유효기간이 짧다.

|  | Access Token | Refresh Token |
| --- | --- | --- |
| 유효기간 | 짧다 | 길다 |
| 용도 | 검증 | 재발급 |

# 암호화

## bcrypt

같은 값에는 항상 같은 결과(해시)를 내보낸다.

즉, 결과를 가지고 암호화 되기 전의 비밀번호를 알아낼 수 있다.

결과가 나오기 까지의 시간이 굉장히 오래걸린다. (일부러 이렇게 알고리즘을 짜두었음)

느릴수록 보안 강도가 높아진다.

- Dictionary Attack : 모든 해시값에 대응하는 패스워드들을 작성해두고 비교하면서 해킹하는 것
- Salt : 글자에 Salt 값을 추가해버린 다음에 bcrypt 를 해버리면 미리 딕셔너리 테이블을 만들 수 없기 때문에 보안이 강화된다.

## 구현

회원가입 후 다시 로그인 페이지로 이동하는 것이 아니라 Access Token과 Refresh Token을 반환해서 바로 로그인이 가능하도록 구현하는 것이 좋다.

참고로 Salt는 자동 생성되기 때문에 따로 입력을 해주지 않아도 된다. 

```tsx
// 1. jwt, bcrypt 설치
npm i @nestjs/jwt bcrypt

// 2. Module 추가
// src/auth/auth.module.ts
@Module({
  imports: [JwtModule.register({})],
  controllers: [AuthController],
  providers: [AuthService],
})

// 3. 의존성 주입
// src/auth/auth.service.ts
@Injectable()
export class AuthService {
  constructor(
    private readonly jwtService: JwtService,
  ) {}
}
```

## 토큰 파싱

Header로 부터 토큰을 받을 때 두 가지 방식으로 받는다.

- 로그인 할 때 email과 password를 Base64로 인코딩한 형태  {authorization: ‘Basic {token}’}
- 발급 받은 토큰을 그대로 넣었을 때 {authorization: ‘Bearer {token}’}

따라서 이때 Basic과 Bearer 를 구분해서 토큰 값만 뽑아내야 하는데 여기서 파싱이 필요한 것이다.

```tsx
  async extractTokenFromHeader(header: string, isBearer: boolean) {
    // 데이터가 이런 식으로 들어오기 때문에 split이 가능하다.
    // 'Basic {token}' 또는 'Bearer {token}'
    const splitToken = header.split(' ');

    const prefix = isBearer ? 'Bearer' : 'Basic';

    // 위 과정대로 나누었는데 두 개로 나뉘지 않은 경우에는 잘못된 값이 넘어온 것이기 때문에 에러를 발생시킨다.
    if (splitToken.length !== 2 || splitToken[0] !== prefix) {
      throw new UnauthorizedException('잘못된 토큰입니다.');
    }

    const token = splitToken[1];

    return token;
  }
```
