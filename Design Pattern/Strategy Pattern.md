# 전략 (Strategy) 패턴

<br>
 
### 목차

- 전략 (Strategy) 패턴
- 적용 가능성
- Before
- After
- 장단점
- 다른 패턴과의 관계


<br>
 

### 전략 (Strategy) 패턴

---

![0](https://user-images.githubusercontent.com/91618389/199911231-b9dc8641-462b-413b-8358-f87ba20914da.png)

- 여러 알고리즘을 캡슐화하고 상호 교환 가능하게 만드는 패턴
- 컨텍스트에서 사용할 알고리즘을 클라이언트에서 선택한다.

> Strategy Pattern은 일련의 알고리즘을 정의하고 각 알고리즘을 별도의 클래스에 넣고 
해당 객체를 교환할 수 있도록 하는 행동 디자인 패턴이다.
> 

- **Context**
    - if - else 처럼 상황에 따라 달라지는 경우를 Strategy로 뽑아냈다.
    - 그래서 Context가 Strategy를 참조하고 있다.
    - 여기서 중요한 점은 Interface로 참조하게 해야 한다.
    - 그래야지 상호 교환 가능한 여러가지 전략들을 바꿔 끼워넣을 수 있다. (DIP 준수)

- ConcreteStarategy
    - Strategy Interface의 구현체이고, 각각의 알고리즘을 해당 Strategy 내에서 구현했다.

<br>
 

<aside>
✅ **특징**

- Client가 사용할 Strategy를 직접 선택해서 Context를 만들때 (Context의 Operation을 적용할때) Strategy를 넣어줄 수 있다.
- 생성자 시점에 Context의 Strategy를 초기화 할 수 있고, 
특정한 Operation 실행할때 그때 그때 넘겨줄 수 있다.
</aside>

<br>
 

*context* 라는 원래 클래스 에는 전략 중 하나에 대한 참조를 저장하기 위한 필드가 있어야 합니다. 

컨텍스트는 자체적으로 실행하는 대신 연결된 전략 개체에 작업을 위임한다.

*context는* 작업에 적합한 알고리즘을 선택할 책임이 없다.

대신 클라이언트는 원하는 전략을 컨텍스트에 전달한다.

사실, *context 는* 전략에 대해 많이 알지 못한다.

*context 는* 선택한 전략 내에 캡슐화된 알고리즘을 사용하기 위해서 한가지 방법만 노출하고 있는 

일반 인터페이스를 통해 모든 전략과 함께 작동합니다. (DIP 준수)

이러한 방식으로  *context 는* 구체적인 전략과 무관하므로 *context* 또는 기타 전략의 코드를 변경하지 않고도 새 알고리즘을 추가하거나 기존 알고리즘을 수정할 수 있습니다 (OCP 준수)

![1](https://user-images.githubusercontent.com/91618389/199911230-1a0ecdc2-5efe-4881-8a81-cf1f31bf5a4f.png)

1. Context
    - 구체적인 전력 중 하나에 대한 참조를 유지하고 Strategy Interface를 통해섬나 이 객체와 통신한다.

1. Strategy Interface
    - 모든 구체적인 전략의 공통이다.
    - Context가 Strategy를 실행 하는데 사용하는 방법을 선언한다.

1. Concrete Strategies
    - Context가 사용하는 알고리즘의 다양한 변형을 구현한다.

1. Context는 알고리즘을 실행해야 할 때마다 연결된 Strategy 구현체의 실행 Method를 호출한다. Context는 어떤 유형의 Strategy과 함께 작동하는지 또한 알고리즘이 어떻게 실행되는지 모른다.

1. Client 특정 Strategy 구현체를 생성하여 Context에게 전달한다. Context는 Client가 런타임에 Context와 관련된 Strategy를 대체할 수 있도록 하는 Setter를 노출한다.

<br>
 

### 적용 가능성

---

**객체 내에서 다양한 알고리즘 변형을 사용하고 런타임 중에 한 알고리즘에서 다른 알고리즘으로 전환할 수 있도록 하려는 경우 전략 패턴을 사용한다.**

- 전략 패턴을 사용하면 다른 방식으로 특정 하위 작업을 수행할 수 있는 다른 하위 개체와 연결하여 런타임에 개체의 동작을 간접적으로 변경할 수 있다.

**일부 동작을 실행하는 방식만 다른 유사한 클래스가 많이 있는 경우 전략을 사용한다.**

- 전략 패턴을 사용하면 다양한 동작을 별도의 클래스 계층으로 추출하고 원래 클래스를 하나로 결합하여 중복 코드를 줄일 수 있다.

**클래스에 동일한 알고리즘의 다른 변형 간에 전환하는 큰 if-else 조건문이 있는 경우 패턴을 사용한다.**

- 전략 패턴을 사용하면 모든 알고리즘을 동일한 인터페이스를 구현하는 별도의 클래스로 추출하여 이러한 조건을 제거할 수 있다.
- 원래 개체는 알고리즘의 모든 변형을 구현하는 대신 이러한 개체 중 하나에 실행을 위임한다.

<br>
 

### Before

---

**BlueLightRedLight** 

```java
public class BlueLightRedLight {

    private int speed;

    public BlueLightRedLight(int speed) {
        this.speed = speed;
    }

    public void blueLight() {
        if (speed == 1) {
            System.out.println("무 궁 화    꽃   이");
        } else if (speed == 2) {
            System.out.println("무궁화꽃이");
        } else {
            System.out.println("무광꼬치");
        }

    }

    public void redLight() {
        if (speed == 1) {
            System.out.println("피 었 습 니  다.");
        } else if (speed == 2) {
            System.out.println("피었습니다.");
        } else {
            System.out.println("피어씀다");
        }
    }
}
```

**Client**

```java
public class Client {

    public static void main(String[] args) {
        BlueLightRedLight blueLightRedLight = new BlueLightRedLight(3);
        blueLightRedLight.blueLight();
        blueLightRedLight.redLight();
    }
}
```

<br>
 

<aside>
💡 어떤 파라미터에 따라, if - else문으로 분기하면서 각각 다 다른 행동을 하고 있는 코드

</aside>

<br>
 

<br>
 

<aside>
💡 전략 패턴을 사용하여, 원하는 전략을 쉽게 바꿔 끼워 넣음으로써 행동하게 만들 수 있다.

</aside>

<br>
 

<br>
 

### After

---

![2](https://user-images.githubusercontent.com/91618389/199911227-7205000a-8b7d-4a4f-ac90-9c3ed74e2985.png)

**BlueLightRedLight (Context Class)**

```java
public class BlueLightRedLight {

    public void blueLight(Speed speed) {
        speed.blueLight();
    }

    public void redLight(Speed speed) {
        speed.redLight();
    }
}
```

→ 방법 1

→ Operation을 실행할때 그때 전략을 선택할 수 있다.

```java
public class BlueLightRedLight {

		private Speed speed;

		public BlueLightRedLight (Speed speed) {
			this.speed = speed;
		}

    public void blueLight() {
        speed.blueLight();
    }

    public void redLight() {
        speed.redLight();
    }
}
```

→ 방법 2

<br>
 

<aside>
✅ Strategy Interface (Speed)를 사용해서, 이를 처리한다.

</aside>

<br>
 

**Speed** interface (Strategy Interface)

```java
public interface Speed {

    void blueLight();

    void redLight();

}
```

Normal **(ConcreteStrategy)**

```java
public class Normal implements Speed {
    @Override
    public void blueLight() {
        System.out.println("무 궁 화    꽃   이");
    }

    @Override
    public void redLight() {
        System.out.println("피 었 습 니  다.");
    }
}
```

Faster **(ConcreteStrategy)**

```java
public class Faster implements Speed {
    @Override
    public void blueLight() {
        System.out.println("무궁화꽃이");
    }

    @Override
    public void redLight() {
        System.out.println("피었습니다.");
    }
}
```

**Fastest (ConcreteStrategy)**

```java
public class Fastest implements Speed{
    @Override
    public void blueLight() {
        System.out.println("무광꼬치");
    }

    @Override
    public void redLight() {
        System.out.println("피어씀다.");
    }
}
```

```java
public class Client {

    public static void main(String[] args) {
        BlueLightRedLight game = new BlueLightRedLight();
        game.blueLight(new Normal());
        game.redLight(new Fastest());
        game.blueLight(new Speed() {
            @Override
            public void blueLight() {
                System.out.println("blue light");
            }

            @Override
            public void redLight() {
                System.out.println("red light");
            }
        });
    }
}
```

→ 방법 1를 사용한 경우, Operation을 실행할때 그때 전략을 선택할 수 있다.

<br>
 

<aside>
✅ 코드를 전략 패턴으로 수정해서 좋은 점은

1. 새로운 전략을 추가해서 적용할떄, OCP를 만족할 수 있다.
2. Context Class는 Strategy Interface를 바라봄으로써 그 구현체를 손쉽게 바꿔 끼워넣을 수 있다.
</aside>

<br>
 

<br>
 

### 장단점

---

- 장점
    - 새로운 전략을 추가하더라도 기존 코드를 변경하지 않는다.
        - Context의 코드에 영향을 받지 않는다.
    - 상속 대신 **위임(구성,Composition)**을 사용할 수 있다.
        - 상속은 다중상속에 안되는 큰 문제점이 있다.
    - 런타임에 전략을 변경할 수 있다.
        - 추가적으로 익명 클래스로 적용이 가능하다.
- 단점
    - 복잡도가 증가한다.
    - 클라이언트 코드가 구체적인 전략을 알아야 한다. 각각의 전략에 대해서 차이점을 알고 있어야 한다.
    - 클라이언트는 적절한 전략을 선택할 수 있도록 전략 간의 차이점을 알고 있어야 한다.
    

<br>
 

### 다른 패턴과의 관계

---

- Bridge , State , Strategy (그리고 어느 정도 Adapter )는 매우 유사한 구조를 가지고 있다.. 실제로 이러한 모든 패턴은 작업을 다른 개체에 위임하는 구성을 기반으로 한다.

- Command와 Strategy 는 비슷해 보이지만 서로 다른 목적성을 가지고 있다.
    - *`Command`* 를 사용 하면 모든 작업을 객체로 변환할 수 있다.  변환을 통해 작업 실행을 연기하고, 대기열에 넣고, 명령 기록을 저장하고, 원격 서비스에 명령을 보내는 등의 작업을 수행할 수 있습니다. (**서로 각각 전혀 다른 행동**을 수행한다.)
    - 반면에 *`Strategy`* 은 일반적으로 **동일한 작업**을 수행하는 여러 가지 방법을 설명하므로 단일 컨텍스트 클래스 내에서 이러한 알고리즘을 교환할 수 있습니다.

- `*Decorator*`를 사용하면 객체의 껍질 (Class)을 변경할 수 있고 `*Strategy*` 를 사용하면 Method 행동 방식을 변경할 수 있다.

- `*템플릿 메서드*` 는 **상속**을 기반으로 한다. 이 메서드를 사용하면 하위 클래스에서 해당 부분을 확장하여 알고리즘의 일부를 변경할 수 있습니다. 또한 클래스 수준에서 작동하므로 정적이다.
- `*전략*` 은 **구성**을 기반으로 합니다. 해당 동작에 해당하는 다른 전략을 제공하여 개체 동작의 일부를 변경할 수 있습니다.  *`전략`* 은 개체 수준에서 작동하므로 런타임에 동작을 전환할 수 있다.

- `*상태*` 는 *`전략`* 의 확장으로 간주될 수 있다. 두 패턴 모두 **구성**을 기반으로 한다.
    - 공통적으로, 일부 작업을 Composition 개체에 위임하여 컨텍스트의 동작을 변경한다.
    - *`전략*` 은 이러한 개체를 완전히 독립적으로 만들고 서로를 인식하지 못하게 한다.
    - 그러나 *`State*` 는 구체적인 상태(Concrete State) 간의 종속성을 제한하지 않고 컨텍스트의 상태를 마음대로 변경할 수 있습니다.
