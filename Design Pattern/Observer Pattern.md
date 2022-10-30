# 옵저버 (Observer) 패턴

<br>
 
### 목차

- 옵저버 패턴
- 왜 필요한가?
- 어떻게 사용할까?
- Before
- After
- 장단점
- 다른 패턴과의 관계


<br>
 

### 옵저버 (Observer) 패턴

---

![Untitled](https://user-images.githubusercontent.com/91618389/198884339-505fb462-2e26-427c-8aaa-1ed7547bddcb.png)


> Observer는 행동 디자인 패턴으로 구독 매커니즘을 정의한다.  관찰자는 관찰중인 객체에 발생하는 모든 이벤트에 대해 여러 관찰자에게 알리는 구독 메너니즘을 정의한다.
> 

- 다수의 객체(ConcreteObserver)가 특정 객체(Subject) 상태 변화를 감지하고 알림을 받는 패턴
- 발생 (Publish) - 구독 (Subscribe) 패턴을 구현할 수 있다.

- **Subject**
    - 여러 Observer 들을 등록하거나, 해제하는 역할을 담당
    - Client는 이러한 Subject를 사용해서 여러 Observer 들을 특정 Subject에 등록을 할거고Subject가 제공하는 특정 Method를 사용해서, Subject의 상태를 변경할것이다.
    - Subject의 상태가 변경이 되면, 자신이 관리하고 있는 Observer 들을 순회하면서
    Observer가 제공하는 특정한 Method를 모두 호출한다.

- **Observer Interface**
    - 어떤 이벤트가 외부에서 발생 했을때, Observer들을 호출해줘야 하는 특정 Method가 필요하다.
    - 그 이벤트 자체를 Observer에게 제공함으로써, Observer를 호출시키고, 그 공통된 Method 안에서 실질적으로 Observer가 해야할 행동들을 수행한다.
    
- ConcreteObserver
    - Observer Interface에서 제공하는 Method를 정의함으로써 실제 행동을 하게 된다.
    
<br>
 

### 왜 필요한가?

---

- Customer, Store라는 2개의 객체가 있다고 생각해보자
- Store에 새로운 신상 아이폰이 들어올 예정인데, 언제 들어올지 몰라서 Customer는 매번 Store에 가서 확인해야 하는 문제가 발생한다.
- Observer Pattern을 활용하면 Store에 iphone 재고가 들어오는 순간 알림 메시지를 보내버리면 많은 수고로움을 덜어줄 수 있다.

![u1](https://user-images.githubusercontent.com/91618389/198884338-d65904ca-5e36-4136-9eea-2e2f9b6478b7.png)

<br>
 

### 어떻게 사용할까?

---

- Publisher
    - 자기 자신의 상태 변화를 구독자에게 알려주는 역할
- Subscribers
    - Publisher의 상태를 추적해서 최신의 상태 정보를 얻고 싶어하는 객체들

- Observer Pattern은 구독 메커니즘을 이용하여 개별적인 Subscribers들이 Publisher에서 오는 이벤트 스트림을 구독하거나 구독 취소할 수 있도록 할 수 있다.

![u2](https://user-images.githubusercontent.com/91618389/198884337-31e22119-e97f-4d76-93fe-6c48ffb421ee.png)

- Publisher
    - 구독자들을 저장하기 위한 일렬의 배열 필요
    - 구독자 추가 및 삭제를 위한 특정 Method 필요

> 만약 Publisher에게 상태 변화가 생겨서 각각의 구독자들에게 알림을 보내야 하는데, 각각 개별적인 Subsciber가 서로 다른 알림 method를 가지고 있으면 Publisher는 모든 구독자에 대해 너무 많은것을 알아야 한다.
> 

이를 해결하기 위해서는 각각의 Subscriber는 동일한 Interface를 Implement 해야 한다.

![u3](https://user-images.githubusercontent.com/91618389/198884336-f06481b2-303f-4f41-859f-d3f5d52f0140.png)

> The Observer pattern lets any object that implements the subscriber interface subscribe for event notifications in publisher objects
> 

<br>
 

### Before

---

**ChatServer** 

```java
public class ChatServer {

    private Map<String, List<String>> messages;

    public ChatServer() {
        this.messages = new HashMap<>();
    }

    public void add(String subject, String message) {
        if (messages.containsKey(subject)) {
            messages.get(subject).add(message);
        } else {
            List<String> messageList = new ArrayList<>();
            messageList.add(message);
            messages.put(subject, messageList);
        }
    }

    public List<String> getMessage(String subject) {
        return messages.get(subject);
    }
}
```

**User** 

```java
public class User {

    private ChatServer chatServer;

    public User(ChatServer chatServer) {
        this.chatServer = chatServer;
    }

    public void sendMessage(String subject, String message) {
        chatServer.add(subject, message);
    }

    public List<String> getMessage(String subject) {
        return chatServer.getMessage(subject);
    }
}
```

**Client** 

```java
public class Client {

    public static void main(String[] args) {
        ChatServer chatServer = new ChatServer();

        User user1 = new User(chatServer);
        user1.sendMessage("디자인패턴", "이번엔 옵저버 패턴입니다.");
        user1.sendMessage("롤드컵2021", "LCK 화이팅!");

        User user2 = new User(chatServer);
        System.out.println(user2.getMessage("디자인패턴"));

        user1.sendMessage("디자인패턴", "예제 코드 보는 중..");
        System.out.println(user2.getMessage("디자인패턴"));
    }
}
```

<br>
 

<aside>
💡 위와 같은 코드의 문제점은 User1이 어떤 주제에 대해서 ChatServer로 보냈는데
그걸 확인하려면, User2 입장에서는 계속해서 getMessage를 호출해서 알아야 한다.

즉, User1이 채팅 메시지를 언제 보냈는지 User2는 모르며, 수시로 getMessage()를 호출
할 수도 있다.

</aside>
<br>
 

### After

---

![u4](https://user-images.githubusercontent.com/91618389/198884320-59fed621-1089-4823-92da-5b3182082f7e.png)

**Subscriber (Observer)**

```java
public interface Subscriber {

    void handleMessage(String message);
}
```

- 메시지를 전달 받을 수 있는 Observer

**User** 

```java
public class User implements Subscriber {

    private String name;

    @Override
    public void handleMessage(String message) {
        System.out.println(message);
    }
}
```
<br>
 

<aside>
✅ Subscriber 를 구현하는 ConcreteObserver

message가 들어오면 그냥 출력하는 역할 수행

</aside>
<br>
 

**ChatServer** 

```java
public class ChatServer {

    private Map<String, List<Subscriber>> subscribers = new HashMap<>();

    public void register(String subject, Subscriber subscriber) {
        if (this.subscribers.containsKey(subject)) {
            this.subscribers.get(subject).add(subscriber);
        } else {
            List<Subscriber> list = new ArrayList<>();
            list.add(subscriber);
            this.subscribers.put(subject, list);
        }
    }

    public void unregister(String subject, Subscriber subscriber) {
        if (this.subscribers.containsKey(subject)) {
            this.subscribers.get(subject).remove(subscriber);
        }
    }

    public void sendMessage(String subject, String message) {
        if (this.subscribers.containsKey(subject)) {
            this.subscribers.get(subject).forEach(s -> s.handleMessage("구독자들에게 전송 중 : "  +message));
        }
    }

}
```
<br>
 

<aside>
✅ 여러 Observer들을 등록하고 해제하고 알림을 전달 할 수 있어야 한다.

ChatServer의 상태를 변경할 수 있는 (누군가가 메시지를 보내는 행위)
다만 여기서 해당 메시지에 대해서는 저장하지 않는다.
→ 단순히 다른 Observer에게 알려주는 역할 (전파시키는 역할)

</aside>
<br>
 

**Client** 

```java
public class Client {

    public static void main(String[] args) {
        ChatServer chatServer = new ChatServer();
        User user1 = new User("keesun");
        User user2 = new User("whiteship");

        chatServer.register("오징어게임", user1);
        chatServer.register("오징어게임", user2);

        chatServer.register("디자인패턴", user1);
        chatServer.register("디자인패턴", user2);

        chatServer.sendMessage( "오징어게임", "아.. 이름이 기억났어.. 일남이야.. 오일남");
        chatServer.sendMessage("디자인패턴", "옵저버 패턴으로 만든 채팅");

        chatServer.unregister("디자인패턴", user2);

        chatServer.sendMessage("디자인패턴", "옵저버 패턴 장, 단점 보는 중");
    }
}
```

<br>
 

### 장단점

---

- 장점
    - 상태를 변경하는 객체(Publisher)와 변경을 감지하는 객체(Subscriber)의 관계를 느슨하게 유지할 수 있다.
    - Subject의 상태 변경을 주기적으로 조회하지 않고 자동으로 감지할 수 있다.
    - 런타임에 옵저버를 추가하거나 제거할 수 있다.
- 단점
    - 복잡도가 증가한다.
    - 다수의 Observer 객체를 등록 이후 해지 않는다면 memory leak이 발생할 수도 있다.
    
<br>
 

### 다른 패턴과의 관계

---

- Chain of Responsibility, Command, Mediator , Observer들은 요청의 발신자와 수신자를 연결하는 다양한 방법을 다룬다.
    - Chain of Responsibility는 수신자 중 하나가 처리할 때까지 잠재적 수신자의 동적 사슬을 따라 순차적으로 요청을 전달한다.
    - Command는 발신자와 수신자 간의 단방향 연결을 설정한다.
    - Mediator는 송신자와 수신자 간의 직접 연결을 제거하여 중재자 개체를 통해 간접적으로 통신하도록 한다.
    - Observer를 사용하면 수신자가 수신 요청을 동적으로 구독 및 구독 취소를 할 수 있다.

- Mediator vs Observer
    - Mediator의 주요 목표는 시스템 구성 요소 집합간의 상호 종속성을 제거하는 것이다. 대신에 이러한 구성 요소는 단일 중재자 객체에 종속된다.
    - Observer의 목적은 일부 객체(Subscriber)가 다른 객체(Publisher)의 종속 역할을 하는 객체 간에 동적 단방향 연결을 설정하는 것이다.
    - Mediator의 중재자 객체는 Observer에서 마치 Publisher의 역할을 가지고, Mediator에 연결된 구성 요소들은 마치 Subscriber 처럼 Mediator 중재자의 이벤트를 구독 및 구독 취소하는 역할을 가지게 구현할 수 있다.
    - 만약 Mediator가 이런식으로 Implement 하면 이러한 방식은 Observer와 굉장히 유사하다.

