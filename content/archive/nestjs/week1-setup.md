---
title: "Set up"
date: "2025-11-19"
category: "nestjs"
---

# 기본 지식

Compiled : 프로그램을 실행하기 전에 작성된 코드를 기계어로 한번에 모두 변환 후 실행

Interpreted : 프로그램을 실행하면서 동시에 코드를 한줄씩 변환해서 실행

JIT : 컴파일과 인터프리터 둘의 장점만 합친 것

## Node

NodeJS는 싱글 쓰레드 모델이다. 느릴 것 같지만 Blocking 모델과 Non-Blocking 모델을 따로 처리한다.

Blocking 모델은 이벤트 큐 → 이벤트 루프 를 거쳐서 바로 실행된다.

Non-Blocking 모델은 이벤트 큐 →이벤트 루프 → 워커 스레드 를 거쳐서 실행된다.

여기서 싱글 쓰레드인 이벤트 루프가 막히지 않도록 잘 관리해야 한다.

## HTTP 요청

- Header : 메타데이터 정의
- Body : 실제 요청에 관련된 데이터를 보내는 곳
- 메타데이터 : 데이터에 관련된 데이터

- GET : 데이터 조회
- POST : 데이터 생성
- PUT : 데이터 업데이트 또는 데이터 생성 (데이터가 존재하지 않으면 생성)
- PATCH : 데이터 업데이트 (데이터가 존재하지 않으면 에러)
- DELETE : 데이터 삭제

## HTTP 응답

- 100 ~ 199 : 정보 응답 (거의 안 씀)
- 200 ~ 299 : 성공 응답
- 300 ~ 399 : 리다이렉션 메세지
- 400 ~ 499 : 클라이언트 에러 응답
- 500 ~ 599 : 서버 에러 응답

## URL
https:// github.dev / JunhOpportunity
Scheme / Host / Path Query-Parameter
