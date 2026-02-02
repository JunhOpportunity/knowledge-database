---
title: "App Router - 이미지 최적화, SEO 최적화, vercel 배포"
date: "2025-07-15"
category: "nextjs"
---
# App Router
## 이미지 최적화

Next에서 제공해주는 <Image/> 컴포넌트를 사용하면 기본적인 최적화가 다 이루어진다.

1. 이미지 미리 등록하기

```tsx
// next.config.mjs

images: {
	domain: ["도메인 입력"]
}
```

1. 이미지 사이즈 작게 설정하기 (width={240} height={300})

## SEO 최적화

### Metadata 설정하기

```tsx
export const metadata : Metadata = {
	title: "타이틀",
	description: "설명",
	images: ["/"], // public 디렉토리 안에있는 이미지
}
```

## 배포하기

1. `$ sudo npm i -g vercel`
2. `$ vercel login`
3. `$ vercel`
4. `$ vercel --prod` (재배포)
