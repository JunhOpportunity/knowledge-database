---
title: "TypeORM 실전"
date: "2025-12-10"
category: "nestjs"
---

# TypeOrm 실전

## 세팅

```tsx
// 1. NestJS 세팅
$ nest new _프로젝트이름_

// 2. TypeOrm 설치
$ npm i @nestjs/typeorm typeorm pg

// 3. Docker 세팅
docker-compose.yaml 파일 작성
postgres-data 폴더 생성

services:
  postgres:
    image: postgres:15
    restart: always
    volumes:
      - ./postgres-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: typeormstudy

// 4. TypeOrm 세팅
app.module.ts 파일에서 imports 수정

@Module({
  imports: [
    TypeOrmModule.forFeature([UserModel]),
    TypeOrmModule.forRoot({
      type: 'postgres',
      host: '127.0.0.1',
      port: 5432,
      username: 'postgres',
      password: 'postgres',
      database: 'typeormstudy',
      entities: [UserModel],
      synchronize: true,
    }),
  ],
  controllers: [AppController],
  providers: [AppService],
})

// 5. Docker 실행
$ docker-compose up

// 6. Nest 실행
$ npm run start:dev

// 7. entity 생성
src/entity/user.entity.ts 파일 생성

@Entity()
export class UserModel{
  //ID
  @PrimaryGeneratedColumn()
  id: number;

  // 제목
  @Column()
  title: string;

  // 데이터 생성 일자
  @CreateDateColumn()
  createdAt: Date;

  // 데이터 업데이트 일자
  @UpdateDateColumn()
  updatedAt: Date;

  // 버전관리
  @VersionColumn()
  version: number;

  @Column()
  @Generated('increment')
  additionalId: number;
}

// 8. Entity 적용
app.module.ts 파일에서 entites 부분에 엔티티 이름 추가

// 9. app.controller.ts 수정
app.controller.ts 에서 요청 처리 함수 작성
```
