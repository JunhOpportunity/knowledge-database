---
title: "Web Socket"
date: "2026-01-27"
category: "nestjs"
---
# 웹소켓

> 웹소켓 프로토콜을 사용해서 만든 낮은 지연시간, 양방향 소통, 이벤트 기반으로 클라이언트와 서버가 통신할 수 있게 해주는 기능
> 

연결은 무조건 클라이언트에서 해야 한다.

## 기본 구조

- emit : 연결된 모든 소켓들에 메세지를 보낸다. `emit(이벤트명, 보낼메세지)`
- on : 메세지를 듣는다. `on(이벤트명, 콜백함수)`
- of : 네임 스페이스를 입력받는다. `of(”chat”)`
클라이언트에서는 간단하게 연결 가능 `io(”https://localhost:3000/chat”)`
- join : 특정 룸에 소켓을 넣는다. `join(”room1”)`
클라이언트에서는 룸을 조작할 수 없다. 서버에서만 조작이 가능하다.
- broadcast.emit : 나 빼고 모든 소켓들에 메세지를 보낸다. `broadcast.emit(”hello”, “world”)`

## 확장된 구조 (Acknowledgement)

- emit : 메세지를 보낸다. `emit(이벤트명, 보낼메세지, 콜백함수)`
- on : 메세지를 듣는다. `on(이벤트명, (메세지, 콜백함수))`

이때는 on 쪽에서 서버로 전달이 된다.

emit → on → emit 순서대로 계속해서 반복된다.

```tsx
// 서버
socket.emit('hello', 'world', (response) => {
	console.log(response); // 수신 양호 출력
})

// 클라이언트
socket.on('hello', (message, callback) => {
	console.log(message); // world 출력
	callback("수신 양호")
})
```

# NestJS 웹소켓

```tsx
$ npm i @nestjs/websockets @nestjs/platform-socket.io socket.io
```

## 1. gateway 파일 생성

소켓과 관련된 것들을 관리하는 파일이다.

```tsx
// chats.gateway.ts 생성
@WebSocketGateway({
  namespace: 'chats',
})
export class ChatsGateway implements OnGatewayConnection{
  handleConnection(socket: Socket) {
    console.log('클라이언트 접속:', socket.id);
  }

  // socket.on('message', (message) => { console.log(message); }
  @SubscribeMessage('send_message')
  sendMessage(
    @MessageBody() message: string,
  ) {
    console.log('클라이언트로부터 받은 메시지:', message);
  }
}
```

## 2. 모듈 등록

```tsx
@Module({
  controllers: [ChatsController],
  providers: [ChatsService, ChatsGateway],
})
export class ChatsModule {}
```

## 연결된 모든 소켓에 메세지 보내기

> 여러개가 듣고 있는 상황에서, 한 쪽이 메세지를 보내서 응답을 받도록 할 경우 나머지 듣고있는 소켓들도 모두 메세지를 받도록 할 수 있다.
이것이 채팅의 기본.
> 

<img width="538" height="199" alt="image" src="https://github.com/user-attachments/assets/ee650d19-e7a3-4986-9862-c58f11d37813" />


`server` 를 추가하면 서버에서 연결된 모든 소켓들에게 receive_message 이벤트를 보내서 hello from server 를 메세지로 넘겨주라는 코드를 작성할 수 있다.

```tsx
  @WebSocketServer()
  server: Server;
  
  @SubscribeMessage('send_message')
  sendMessage(@MessageBody() message: string) {
    this.server.emit('receive_message', 'hello from server');
  }
```

## Room 활용하기

Room을 들어가기 위해서는 `socket.join()` 을 사용.

즉, 특정 방에 들어가 있는 소켓들에게만 메세지를 보낼 수 있음.

```tsx
@WebSocketGateway({
  namespace: 'chats',
})
export class ChatsGateway implements OnGatewayConnection {
  @WebSocketServer()
  server: Server;

  handleConnection(socket: Socket) {
    console.log('클라이언트 접속:', socket.id);
  }

  @SubscribeMessage('enter_chat')
  enterChat(
    // 방의 ID들을 리스트로 받는다.
    @MessageBody() data: number[],
    @ConnectedSocket() socket: Socket,
  ) {
    for (const chatId of data) {
      //socket.join()
      socket.join(chatId.toString());
    }
  }

  // socket.on('message', (message) => { console.log(message); }
  @SubscribeMessage('send_message')
  sendMessage(
    @MessageBody() message: { message: string; chatId: number },
    @ConnectedSocket() socket: Socket,
  ) {
    this.server
      .in(message.chatId.toString())
      .emit('receive_message', message.message);
  }
}

```

## BroadCast

> 나 빼고 해당 룸에 있는 모두에게 메세지 보내기
> 

간단하게 위 sendMessage 함수에서 `this.server.in` 을 `socket.to` 로 바꾸어주면 된다.

```tsx
  @SubscribeMessage('send_message')
  sendMessage(
    @MessageBody() message: { message: string; chatId: number },
    @ConnectedSocket() socket: Socket,
  ) {
    socket
      .to(message.chatId.toString())
      .emit('receive_message', message.message);
  }
```

즉, `this.server` 를 사용하면 모두가 받고 `socket.to` 를 쓰면 방에서 나를 빼고 받는다.
