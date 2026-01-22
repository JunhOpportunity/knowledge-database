---
title: "✋ 파일 업로드"
date: "2026-01-22"
category: "nestjs"
---
# ✋ 파일 업로드

## Multer (클래식한 방법)

```tsx
$ npm i multer @types/multer uuid @types/uuid
```

+) 파일 확장자명 가져오기

extname을 사용해 xxx.jpg => .jpg 추출

```tsx
const ext = extname(file.originalname);

fileFilter: (req, file, cb) => {
  // cb(에러, boolean): 콜백함수
  // 첫 번째 파라미터는 에러 객체에 대한 정보
  // 두 번째 파라미터는 파일을 받을지 말지 boolean으로 허용 여부
  // 파일 확장자 검사
  // extname을 사용해 xxx.jpg => .jpg 추출
  const ext = extname(file.originalname);
  if (ext !== '.jpg' && ext !== '.jpeg' && ext !== '.png') {
    return cb(new Error('Only images are allowed'), false);
  }
  return cb(null, true);
},
```

+) 현재 프로젝트를 실행한 위치

```tsx
process.cwd();
```
