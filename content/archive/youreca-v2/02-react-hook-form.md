---
title: "React Hook Form 정리"
date: "2026-07-29"
category: "youreca"
---

# React Hook Form

> Form 의 상태 관리 + Validation + 제출을 쉽게 해주는 라이브러리.
> 

보통 React에서 기본적으로 Form은 이렇게 작성한다.

Youreca 프로젝트도 마찬가지로 이런 방식으로 Form을 작성했다.

```jsx
const [name, setName] = useState("");
const [email, setEmail] = useState("");

return (
  <>
    <input
      value={name}
      onChange={(e) => setName(e.target.value)}
    />

    <input
      value={email}
      onChange={(e) => setEmail(e.target.value)}
    />
  </>
);
```

아직까진 괜찮지만, 입력 창이 많아질수록 상태의 개수가 증가한다.

게다가, 검증해야 할 것들도 점점 많아진다.

이를 대신 관리해주는 라이브러리가 React Hook Form인 것이다.

# 핵심 훅

```jsx
const {
    register,
    handleSubmit,
    watch,
    reset,
    setValue,
    formState
} = useForm();
```

### register

```jsx
<input {...register("username")} />
```

이 한 줄로 value, onChange, onBlur, ref 등을 자동으로 연결한다.

### handleSubmit

원래는 제출을 위한 콜백 함수를 다음과 같이 작성했다.

```jsx
const onSubmit = (e) => {
    e.preventDefault();
}
```

```jsx
<form onSubmit={handleSubmit(onSubmit)}>
```

### Validation

```jsx
<input
 {...register("username", {
     required: "닉네임은 필수입니다.",
     minLength: {
         value:3,
         message:"3글자 이상"
     }
 })}
/>
```

### watch

실시간으로 확인하는 값이다.

보통 비밀번호 확인에서 많이 사용한다.

```jsx
const password = watch("password");
```

### 타입스크립트에서는 어떻게 사용할까?

```jsx
type FormValues = {
    username:string;
    email:string;
    password:string;
}

const {
    register
} = useForm<FormValues>();

register("username")
```
