---
title: "Relation 옵션 모음"
date: "2025-12-29"
category: "nestjs"
---

## Relation Options

모든 관계가 같은 옵션을 사용한다.

아래는 일단 OneToOne에 대한 예시

```tsx
@OneToOne(() => ProfileModel, (profile) => profile.user, {
	eager: true,
})
```

- eager (T/F) : find() 실행 할때마다 항상 같이 가져올 것들 결정.
true로 할 경우에 아래 부분에 해당하는 relations 를 직접 적어주지 않아도 됨.
    
    ```tsx
      @Get('users')
      getUsers() {
        return this.userRepository.find({
          relations: {
            profile: true,
            posts: true,
          },
        });
      }
    ```
    
- cascade (T/F) : relation 을 한 번에 같이 저장할지 말지를 결정
- nullable (T/F) : 기본 값은 True.
- onDelete : 관계가 삭제됐을때
    - ‘NO ACITON’: 아무것도 안함
    - ‘CASCADE’ : 참조하는 Row도 같이 삭제
    - ‘SET NULL’ : 참조하는 Row 에서 참조 id를 null로 변경
    - ’SET DEFAULT’ : 기본 세팅으로 설정
    - ‘RESTRICT’: 참조하고 있는 Row가 있는 경우 참조당하는 Row 삭제 불가

## Find Options

```tsx
  @Get('tags')
  getTags() {
    return this.tagRepository.find({
	    select: {
		    id: true,
		    createAt: true,
		    updatedAt: true,
		    version: true
	    },
	    // AND 조건
	    where: {
		    version: 1
	    },
	    // OR 조건
	    where: [
		    {
			    id: 3
		    },
		    {
			    id: Not(1)
		    }
	    ],
	    order: {
		    id: 'ASC'
	    },
	    skip: 1,
	    take: 3
    });
  }
```

- select : select 는 MySQL의 SELECT와 같은 것을 의미한다. 가져올 것들을 정하는데, select 옵션을 사용하는 순간 선택하지 않은 모든 칼럼은 가져오지 않는다.
- where : 필터링을 할 때 사용. 여러 개의 값을 넣을 경우 AND 조건으로 가져온다고 보면 된다. 만약 OR 조건으로 가져오고 싶다면 배열 형태로 가져오면 된다.
- relations : 관계를 가져오는 건데, 위에서 사용한 것 그대로다. 특이한 점은, 여기서 호출한 경우 select나 where에서도 호출이 가능하다는 것이다.
- order : 오름차순(ASC), 내린차순(DESC)
- skip : 처음 몇 개를 제외할지 결정
- take : 몇 개를 가져올지. 처음부터 몇 개를 가져올지 결정
- Not(1) : 1이 아닌 경우 가져오기
- LessThan(30) : 30보다 작은 경우 가져오기
- MoreThan(30) : 30보다 큰 경우 가져오기
- Equal(30) : 30과 같은 경우 가져오기
- Like(’%abc%’) : abc가 중간에 들어가는 문자 가져오기
- ILike(’%aBc%’) : 소문자 대문자 구분 안하고 Like 수행
- Between(10, 15) : 10과 15 사이의 값
- In([1, 3, 5, 7]) : 배열 내부에 해당하는 것들을 모두 가져옴
- IsNull() : Null인 경우 가져옴
