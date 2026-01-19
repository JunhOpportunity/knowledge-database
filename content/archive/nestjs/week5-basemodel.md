---
title: "Base Model"
date: "2026-01-11"
category: "nestjs"
---
# Base Model

> 자주 사용될 것 같은 엔티티들을 모아서 재사용 하는 것
> 

```tsx
// common 리소스 생성한 뒤에 entity 파일에 작성
import { CreateDateColumn, PrimaryGeneratedColumn, UpdateDateColumn } from "typeorm";

export abstract class BaseModel {
  @PrimaryGeneratedColumn()
  id: number;

  @UpdateDateColumn()
  createdAt: Date;

  @CreateDateColumn()
  updatedAt: Date;
}
```

```tsx
// 사용
export class UsersModel extends BaseModel{}
```
