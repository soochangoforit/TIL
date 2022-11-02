# 커맨드 (Command) 패턴

<br>
 
### 목차

- 커맨드 패턴
- 적용 가능성
- 왜 필요할까?
- 문제점은?
- Before
- After
- 장단점
- 다른 패턴과의 관계


<br>
 

### 커맨드 패턴

---

![ㅁㅁ](https://user-images.githubusercontent.com/91618389/199550176-661a4de8-f3c9-4701-a4d3-1affaf4f3de3.png)

- 요청을 캡슐화 하여 호출자 (Invoker)와 수신자 (Receiver)를 분리하는 패턴
- 요청을 처리하는 방법이 바뀌더라도, 호출자(Invoker)의 코드는 변경되지 않는다.

- Command 패턴을 활용해서, 요청 자체를 캡슐화 해서, Command 안에 어떤 Operation을 호출해야 하는지
- Receiver가 누구이며, 해당 Receiver의 어떤 행동을 호출해야 하는지 , 필요한 파라미터가 뭔지
명령을 수행하기 위한 모든 작업들을 Command라는 Interface 안으로 캡슐화 한다.
- Invoker는 매우 추상화되어 있는 Command라는 인터페이스에 execute()라는 메서드를 호출한다.
- 모든 정보는 Command 안에 담겨져 있고, Command를 이런식으로 구성을 하면 재사용도 가능

<br>
 

<aside>
✅ 각각의 역할

Invoker : Command Interface를 참조하고, execute를 통해서 실행 ex) Button

Command Interface : 여러 구체적인 Command를 추상화한 Interface

ConcreteCommand : 구체적인 Command Type, ⇒ 구현체 Class, 

해당 Class들이 어떤 구체적인 Receiver를 사용할지, Receiver의 Operation 사용하는데 

필요한 파라미터들이 ConcreteCommand 안에 담긴다.

Receiver : 요청에 의한 실질적인 작업을 담당, ex) Light 

</aside>

<br>
 

![ss](https://user-images.githubusercontent.com/91618389/199550173-7483f1e6-88e4-4e61-9705-c159edc3e052.png)

- Command 패턴에서 중요한 책임을 가진 객체들은 총 4개로 `Invoker`, `Receiver`, `Client`, 그리고 `Command`가 있다.
- `Client` 는 `Context`와 결합되어, 객체들을 생성하거나 오케스트레이션하여 진행하는 역할을 한다.
- `Command`는 요청을 캡슐화하고 요청을 실행할 `Reciever`를 알고 있으며, 요청을 하는 역할을 한다.
- `Invoker`는 추상화된 `Command`를 주입받을수 있고, 이를 실행하는 일을 하며, `Template Method` 패턴으로서 다른 부수적인 책임을 수행하기도 한다
- `Reciver` 는 실질적으로 요청을 실행하는 일을 한다

![dd](https://user-images.githubusercontent.com/91618389/199550171-baae2ff7-8aaf-47b0-bd68-ccfd5273eede.png)

1. **Invoker**
    - 요청을 시작하는 역할을 한다.
    - 이 클래스는 명령 객체에 대한 참조를 저장하기 위한 필드가 있어야 한다.
    - 발신자는 요청을 수신자에게 직접 보내는 대신 해당 명령을 Trigger 한다.
    - 발진자는 Command Object를 생성할 책임이 없다.

1. **Command Interface**
    - 일반적으로 명령을 실행하기 위한 단일 메서드만 선언한다.

1. **Concrete Command**
    - 다양한 종류의 요청을 구현한다.
    - 구체적인 명령은 Concrete Command 자체적으로 수행하는 것이 아니라 비지니스 논리 객체들 중 하나에게 “요청”을 전달해야 한다.
    - 코드를 단순화하기 위해 이러한 클래스(Reveicer)를 Concrete Command로 병합할 수 있다.
    - Receiver 객체의 메서드를 실행 하는데 필요한 매개 변수는 Concrete Command의 필드로 선언할 수 있다.
    - Concrete Command의 생성자를 통해서만 이러한 필드의 초기화를 허용함으로써 명령에 필요한 객체들을 불변으로 만들 수 있다.

1. **Receiver**
    - 해당 Receiver Class에는 몇가지 비지니스 로직이 포함되어 있다.
    - 거의 모든 객체가 Receiver 역할을 할 수 있다.
    - 대부분의 명령은 요청이 수신자에게 전달되는 방법에 대한 세부 정보만 처리하는 반면, 수신자 자체는 실제 자신에게 할당된 작업을 수행한다.

1. Client
    - Command Interface의 구현체인 Concrete Command를 생성하고 구성한다.
    - Client는 Receiver 객체와 필요한 요청 파라미터들을 포함해서 Command Command 생성자로 넘겨준다.

<br>
 

### 적용 가능성

---

**Receiver 객체를 매개변수화 하려면 Command Pattern를 사용하자**

- Command Pattern는 특정 Receiver의 Method 호출을 독립 실행형 객체(Concrete Command)로 전환할 수 있댜.
- 이렇게 전환 함으로써, 명령을 Method의 인수로 전달하고, 다른 객체 내부에 저장하고, 런 타임에 연결된 명령을 전환하는 등의 작업이 가능해진다.
- 예를 들어, 상황에 맞는 메뉴와 같은 GUI 구성 요소를 개발 중이고 최종 사용자가 항목을 클릭할때 작업을 Trigger하는 메뉴 항목을 사용자가 구성할 수 있게 할 수 있다.

**작업을 대기열에 넣거나 실행을 예약하거나 원격으로 실행하려는 경우, 명령 패턴을 사용하자.**

- 명령 실행을 지연하고 예약할 수 있다.
- 네트워크를 통해 명령을 대기열에 추가하거나 기록해서, 한번의 실행으로 다양한 명령을 순차적으로 실행할 수 도 있다.

**되돌릴 수 있는 작업을 구현하려는 경우, 명령 패턴을 사용한다.**

- 실행 취소/다시 실행을 구현하는 방법에는 여러 가지가 있지만, 명령 패턴이 아마도 가장 많이 사용되는 패턴이다.
- 작업을 되돌리려면 수행한 작업의 기록을 구현해야 한다. 명령 기록은 응용 프로그램 상태의 관련 백업과 함께 실행된 모든 명령 객체를 포함하기 위해 “**스택**”으로 구현할 수 있다.
- 단점은, 상태 백업에는 상당히 많은 RAM을 소모할 수 있다. 따라서 과거 상태를 복원하는 대신 명령의 역연산을 수행해서 RAM 사용을 최소화 할 수도 있다. 하지만 역연산에 대해서도 구현이 어렵거나 혹은 불가능한 경우도 있을 수 있다.

<br>
 

### 왜 필요할까?

---

- 단순히 `Client`와 `Server`의 역할에서 벗어나 보다 구체적인 책임을 할당함에 따라, `SRP`를 준수하게된다.
- 책임에 따라 작게 분할된 객체들은 `OCP`를 준수하기 더 쉬워진다
- `Invoker`의 패턴에 따라 부수적인 다양한 기능을 구현하기 수월하다.
    - `Undo, Logging` 등
- 기존의 요청들을 조합하는 복잡한 요청이 새로 생겨도, 여러 `Command`를 조합하여 구현 가능하다.

<br>
 

### 문제점은?

---

• 단순히 `Server`와 `Client`가 존재하는것에 비해 여러 계층이 추가되므로 코드가 복잡해진다.

<br>
 

### Before

---

```java
public class Light {

    private boolean isOn;

    public void on() {
        System.out.println("불을 켭니다.");
        this.isOn = true;
    }

    public void off() {
        System.out.println("불을 끕니다.");
        this.isOn = false;
    }

    public boolean isOn() {
        return this.isOn;
    }
}

---------------------------------------------------------------
public class Button {

    private Light light;

    public Button(Light light) {
        this.light = light;
    }

    public void press() {
        light.off();
    }

    public static void main(String[] args) {
        Button button = new Button(new Light());
        button.press();
        button.press();
        button.press();
        button.press();
    }
}
```

<br>
 

<aside>
💡 버튼을 활용해서 Light의 행동을 바꾸고자 한다.

Button 입장에서 Light를 키고 끄기 위해서 press 라는 코드를 계속해서 수정해야 한다.

즉, Invoker (요청을 보내는) Button 과 Receiver (요청에 응답하는) Light와의 관계가 굉장히

강하게 연결되어 있다고 생각한다.

</aside>

<br>
 

<br>
 

### After

---

![ff](https://user-images.githubusercontent.com/91618389/199550165-98ff4fa5-eafe-44d0-acbf-b0f045c73a2a.png)


**Command Interface**

```java
public interface Command {

    void execute();

    void undo();

}
```

**LightOnCommand** 

```java
public class LightOnCommand implements Command {

    private Light light;

    public LightOnCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.on();
    }

    @Override
    public void undo() {
        new LightOffCommand(this.light).execute();
    }
}
```

<br>
 

<aside>
✅ 어떤 Instance의 코드를 어떤 파라미터와 함께 호출할건지 정해야 한다.

Light의 Operation을 호출한다.

</aside>

<br>
 

**LightOffCommand** 

```java
public class LightOffCommand implements Command {

    private Light light;

    public LightOffCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.off();
    }

    @Override
    public void undo() {
        new LightOnCommand(this.light).execute();
    }
}
```

**GameStartCommand** 

```java
public class GameStartCommand implements Command {

    private Game game;

    public GameStartCommand(Game game) {
        this.game = game;
    }

    @Override
    public void execute() {
        game.start();
    }

    @Override
    public void undo() {
        new GameEndCommand(this.game).execute();
    }
}
```

**GameEndCommand** 

```java
public class GameEndCommand implements Command {

    private Game game;

    public GameEndCommand(Game game) {
        this.game = game;
    }

    @Override
    public void execute() {
        game.end();
    }

    @Override
    public void undo() {
        new GameStartCommand(this.game).execute();
    }
}
```

**Button** 

```java
public class Button {

    private Stack<Command> commands = new Stack<>();

    public void press(Command command) {
        command.execute();
        commands.push(command);
    }

    public void undo() {
        if (!commands.isEmpty()) {
            Command command = commands.pop();
            command.undo();
        }
    }

    public static void main(String[] args) {
        Button button = new Button();
        button.press(new GameStartCommand(new Game()));
        button.press(new LightOnCommand(new Light()));
        button.undo();
        button.undo();
    }

}
```

→ 게임을 시작합니다.

→ 불을 켭니다.

→ 불을 끕니다.

→ 게임을 종료합니다.

<br>
 

<aside>
✅ 버튼을 사용할때, Command와 실질적으로 요청을 수행할 Receiver를 넣어주면 된다.

Invoker의 Button 입장에서는 코드 변경이 일어나지 않는다.

</aside>

<br>
 

<br>
 

<aside>
✅ Receiver의 Code가 변경이 발생해도, Invoker의 코드 변경이 발생하지 않는다.

대신에, Command의 코드 변경은 함께 일어나야 한다.

ConcreteCommand는 Receiver(Lignt)를 어떻게 써야하는지 구체적으로 알고 있어야 한다.

</aside>

<aside>
✅ 추가적으로 Command는 재사용이 가능해진다.

</aside>

<br>
 

<br>
 

### 장단점

---

- 장점
    - 기존 코드를 변경하지 않고 새로운 커맨드를 만들 수 있다. (OCP)
    - 수신자(Receiver)의 코드가 변경되어도 호출자(Invoker)의 코드는 변경되지 않는다.
    - 커맨드 객체를 로깅, DB에 저장, 네트워크로 전송 하는 등 다양한 방법으로 활용할 수도 있다.
    - 각각의 ConcreteCommand는 각자 자기 자신의 역할을 수행하고 있다. (SRP)
    
- 단점
    - 발신자와 수신자 사이에서 완전히 새로운 계층을 도입하기 때문에 코드가 복잡하고 클래스가 많아진다.

<br>
 

### 다른 패턴과의 관계

---

- 책임연쇄패턴, 커맨드패턴, 중재자패턴, 옵저버 패턴은 모두 요청에 대한 수신자와 송신자간 다양한 결합에 대한 패턴들이다.
    - `Chain of Responsibility`은 동적으로 연결된 체인에 요청을 순차적으로 전달하는 패턴이다.
    - `Command Pattern`은 수신자와 송신자를 오직 단방향으로만 연결하는 패턴이다.
    - `Mediator Pattern`은 송신자와 수신자간 방향성을 제거하고 중재자 객체가 그 사이에서 간접적으로 통신하도록 한다.
    - `Observer Pattern`은 수신자가 동적으로 구독/구독해제를 통해 메시지 수신을 결정할 수 있게한다.
    
- Command Pattern과 Memento Pattern을 사용하여 `Undo` 기능을 구현할 수 있다.

- **커맨드 패턴**과 **전략 패턴(Strategy Pattern)**은 구조적으로 매우 유사하지만, 의도가 다르다.
    - 전략패턴은 하나의 `Context`안에 다른 알고리즘이 필요할때 사용하는 패턴이지만,
    - 커맨드 패턴은 기본적으로 요청을 객체화시켜 다루며, 여러 요청들을 큐나 스택 구조에 기록하거나 다루는데 특화되있다.

