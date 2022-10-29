# 추상 팩토리 (Abstract factory) 패턴


### 목차

- 추상 팩토리란?
- 왜 필요한가?
- 문제점은?
- Before
- 추상 팩토리 패턴을 사용해서, Factory에서 구체적인 부품에 의존하지 않도록..
- 추상 팩토리 적용의 결과
- 추상 팩토리 패턴 장점과 단점 (팩토리 메소드와의 차이점)
- 다른 패턴과의 관계는?

### 추상 팩토리 (Abstract factory) 패턴

---

![Untitled](https://user-images.githubusercontent.com/91618389/198835973-268aac49-d8e9-49f2-93b0-07c38d460efa.png)

- 아래 예제 기준으로 `부품 Factory`에서 서로 관련있는(같은 목적성) 
여러 객체(다른 Interface 부품 구현체)를 만들어주는 인터페이스.
    - 의존하는 객체들은 서로 다른 Product Interface를 구현하고 있지만, 그 목적성은 같을때.
    - 부품 Factoty에서 서로 다른 Product Interface를 구현하는 구현체인 Wheel과 Anchor를 의존하지만, 의존하는 Wheel과 Anchor가 같은 객체를 만들기 위한 같은 목적성을 가지고 있다.
    - 일반적인 팩토리 메소드 패턴과는 다르게 `하나의 Factory`가 `여러 Product 객체 생성`을 담당한다.

- `Concrete제품Factory`에서는 구체적으로 `어떤 부품 클래스`의 인스턴스를 (concrete product)를 사용하는지 감출 수 있다.

- Abstract Factory를 Interface (Abstract) 형태로 정의하는 패턴

- 구체적인 Factory에서 구체적인 Product를 만드는것 까지는 Factory Pattern과 유사하지만
    - `팩토리 메소드 패턴`은 하나의 `Factory Interface 구현체`가 `하나의 Product Interface를 구현`하는 하나의 객체를 생성하는데 사용된다.
    - `추상 팩토리 패턴` 은 하나의 `Factory Interface 구현체`가 `여러 Product Interface를 구현`하는 여러 객체를 생성하는데 사용된다.

이처럼 팩토리 메소드 패턴은 그 자체로 하나의 객체를 생성하는 역할을 해서 그 자체가 객체를 생서하는데 사용된다.

하지만 이렇게 객체를 생성할 경우 타입이 다른 다양한 객체를 생성해야 할 경우에 문제가 생기게 되는데, 이를 해결하기 위한 것이 바로 추상 팩토리 패턴이다.

<br>
 
### 왜 필요한가?

---

- 하나의 목적을 가진 여러 객체들을 만들때, 각각의 객체들을 개별적인 클래스에 의존해서 만들지 않고, 추상 팩토리 구현체만 의존하여 만들 수 있게 해준다.
    - 하나의 Factory에서 같은 목적성을 가진 서로 다른 Product 객체를 만들기 때문에, 
    팩토리 메소드 패턴을 보완해서 사용 가능하다.

- 하나의 추상 팩토리안에서 정의된 구성요소들을 클라이언트는 만들거나 사용하기만 하면 되기 때문에, 구성 요소들이 서로 같은 목적인지 의심할 필요가 없다.
- `다른 목적을 가진, 새로운 객체 집합`을 만들 필요가 있을때, 기존 코드를 건드리지 않고 새로운 추상팩토리 구현체만 만들면 되므로 OCP를 준수한다.

<br>
 

### 문제점은?

---

- 수 많은 인터페이스와 클래스를 정의해야 하므로 코드가 복잡해진다.
- SRP 관점에서 추상 팩토리가 `여러 객체를 생성`하게 되므로 여러 책임을 가졌다고 해석할 여지가 있다.

<br>
 

### Before

---

```java
public class WhiteshipFactory extends DefaultShipFactory {

    @Override
    public Ship createShip() {
        Ship ship = new Whiteship();
        ship.setAnchor(new WhiteAnchor());
        ship.setWheel(new WhiteWheel());
        return ship;
    }
}
```

<br>
 

<aside>
💡 WhiteshipFactory에서 해당 Whiteship을 만들기 위해서 구체적인 부품을 직접 넣어줘서
생산하지만

이와 같은 코드들은 부품이 달라지는 경우 위의 코드를 찾아서 수정해야 하는 문제점이 발생한다.

</aside>

<br>
 
<br>
 

<aside>
✅ 현재 이번 실습에서는 위의 코드는 Client 코드에 해당한다.

</aside>

<br>
 

### 추상 팩토리 패턴을 사용해서, Factory에서 구체적인 부품에 의존하지 않도록..

---
<br>
 

<aside>
✅ 위에서 보이는 것처럼 Client 코드들이 구체적인 부품에 의존하고 있다. 개선해보자

</aside>
<br>
 

**ShipPartsFactory**

```java
public interface ShipPartsFactory {

    Anchor createAnchor();

    Wheel createWheel();

}
```
<br>
 

<aside>
✅ 비슷한 부품들을 만들어주는 Interface를 정의하자.

위의 코드는 추상 팩토리이고, 구체적인 팩토리를 만들 필요가 있다.

</aside>
<br>
 

**WhiteshipPartsFactory**

```java
public class WhiteshipPartsFactory implements ShipPartsFactory {

    @Override
    public Anchor createAnchor() {
        return new WhiteAnchor();
    }

    @Override
    public Wheel createWheel() {
        return new WhiteWheel();
    }
}
```

**Anchor, WhiteAnchor**

```java
public interface Anchor {
}

public class WhiteAnchor implements Anchor{
}
```

**Wheel , WhiteWheel**

```java
public interface Wheel {
}

public class WhiteWheelPro implements Wheel {
}
```

**Ship에서 부품을 끼울때 구체적인 Class가 아니라 Interface를 바라보도록 설계**

```java
public void setWheel(Wheel wheel) {
        this.wheel = wheel;
}

public void setAnchor(Anchor anchor) {
        this.anchor = anchor;
}
    
```

**Client Code에서 추상 팩토리를 사용**

```java
public class WhiteshipFactory extends DefaultShipFactory {

    private ShipPartsFactory shipPartsFactory;

    public WhiteshipFactory(ShipPartsFactory shipPartsFactory) {
        this.shipPartsFactory = shipPartsFactory;
    }

    @Override
    public Ship createShip() {
        Ship ship = new Whiteship();
        ship.setAnchor(shipPartsFactory.createAnchor());
        ship.setWheel(shipPartsFactory.createWheel());
        return ship;
    }
}
```
<br>
 

<aside>
💡 만약 여기서 WhiteShip를 만드는데, 일반적인 WhiteWheel이 필요한게 아니라

추상 팩토리를 구현하기 전까지는 WhiteWheelPro가 필요하다고 하면,  직접 구체적인
Class를 new를 통해서 생성해야 했지만 지금은 그렇지 않다.

</aside>
<br>
 

**WhiteShip 에 들어갈 부품을 다른걸로 바꿔보자**

```java
public class WhiteAnchorPro implements Anchor{
}

public class WhiteWheelPro implements Wheel {
}

public class WhitePartsProFactory implements ShipPartsFactory {

    @Override
    public Anchor createAnchor() {
        return new WhiteAnchorPro();
    }

    @Override
    public Wheel createWheel() {
        return new WhiteWheelPro();
    }
}
```

- 부품을 생산하는 공장에서만 코드 수정이 생긴다.
- WhiteAnchor → WhiteAnchorPro로 변경


 
<br>
 

### 추상 팩토리 적용의 결과적으로..

---

```java
public class WhiteshipFactory extends DefaultShipFactory {

    private ShipPartsFactory shipPartsFactory;

    public WhiteshipFactory(ShipPartsFactory shipPartsFactory) {
        this.shipPartsFactory = shipPartsFactory;
    }

    @Override
    public Ship createShip() {
        Ship ship = new Whiteship();
        ship.setAnchor(shipPartsFactory.createAnchor());
        ship.setWheel(shipPartsFactory.createWheel());
        return ship;
    }
}
```
<br>
 

<aside>
✅ WhiteshipFactory의 생성자에 어떤 Factory를 넣어서 생성하냐에 따라
다른 부품을 가진 WhiteShip이 나온다.

</aside>
<br>
 
<br>
 

<aside>
✅ `ship.setAnchor(shipPartsFactory.createAnchor());`
클라이언트 코드에서 구체적인 클래스의 의존성을 제거한다.

</aside>
<br>
 

**실행하는 Main**

```java
public class ShipInventory {

    public static void main(String[] args) {
        ShipFactory shipFactory = new WhiteshipFactory(new WhiteshipPartsFactory());
        Ship ship = shipFactory.createShip();
        System.out.println(ship.getAnchor().getClass());
        System.out.println(ship.getWheel().getClass());

        ShipFactory shipProFactory = new WhiteshipFactory(new WhitePartsProFactory());
        Ship shipPro = shipProFactory.createShip();
        System.out.println(shipPro.getAnchor().getClass());
        System.out.println(shipPro.getWheel().getClass());
    }
}
```

<br>
 

<aside>
✅ WhiteshipFactory 안에 어떤 부품으로 만들건지는 생성자를 통해서
부품 Factory를 넣어주면서 각기 다른 부품으로 구성된 ship을 얻을 수 있다.

제품군의 부품을 제공하는 곳을 ShipPartsFactory로 위임을 했다. → OCP, SRP 준

</aside>
<br>
 

<br>
 

### 추상 팩토리 패턴 장점과 단점 (팩토리 메소드와 차이점)

---

→ 팩토리 메소드 패턴과 굉장히 흡사한데 무엇이 다른건가.

- 모양과 효과는 비슷하지만…
    - 둘 다 구체적인 객체 생성 과정을 추상화한 인터페이스를 제공한다.

- 관점이 다르다.
    - `팩토리 메소드 패턴`은 “팩토리를 구현하는 방법 (inheritance)”에 초점을 둔다.
        - 구체적인 Factory에서 하나의 구체적인 Product (인스턴스)를 만들어낸다.
    - `추상 팩토리 패턴`은 “팩토리를 사용하는 방법 (composition)”에 초점을 둔다.
        - Factory를 통해서 추상화된 Interface만 쓸 수 있게금 해준다.
        - Client에서 Product를 생성하는데 구체적인 Class를 바라보지 않아도 된다.
        - Client에서 Interface 타입을 사용해서, composition에 초점을 맞추었다.
            - 여러 Composition을 통해서 여러 객체 생성 가능
        

- 목적이 조금 다르다.
    - **팩토리 메소드 패턴**은 `구체적인 객체 생성 과정`을 `하위 또는 구체적인 클래스`로 옮기는 것이 목적.
        - 구체적인 Factory에서 하나의 구체적인 Product (제품)을 만들어낸다.
        
    - **추상 팩토리 패턴**은 관련있는 `여러 객체(Produc,부품)`를 `구체적인 클래스(부품)`에 의존하지 않고 만들 수 있게 해주는 것이 목적.
        - 구체적인 객체 생성 과정을 하위 또는 구체적인 클래스로 옮긴것도 맞지만
        - `하위 또는 구체적인 Factory 클래스`가 `구체적인 Product (제품)를 생성`할시, 구체적인 Product (부품)을 바라보는게 아닌
        - `추상화된 Product (부품)`을 바라보도록 설계한다.

<br>
 
<br>
 

### 다른 패턴과의 관계

---

- 추상 팩토리 패턴은 보통 팩터리 메서드 패턴으로 이루어져 있지만, 프로토타입 패턴을 사용해서도 구현 가능
- 추상 팩토리 패턴은 파사드 패턴의 대안으로 사용될 수도 있다.
- 추상 팩토리 패턴과 브릿지 패턴을 같이 사용하여 코드의 복잡성을 감추는 방법도 존재한다.

