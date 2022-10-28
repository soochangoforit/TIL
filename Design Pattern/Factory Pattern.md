# 팩토리 메소드 (Factory method ) 패턴


### 목차

- Factory Method란?
- 왜 필요한가?
- 문제점?
- 구현방법
- OCP를 준수하면서 확장시켜보자
- 새로운 Factory가 생길때마다, Client 코드가 바뀌는 문제점 수정하기
- 팩토리 메서드의 장단점
- 다른 패턴과의 관계


<br>
 
### **Factory Method란?**

---

![Untitled](https://user-images.githubusercontent.com/91618389/198712515-b4f609e4-36e0-4cb5-8fd3-f919c84aa739.png)

- 구체적으로 어떤 인스턴스를 만들지는 서브 클래스가 정한다.
- 다향한 구현체 (Product)가 있고, 그 중에서 특정한 구현체를 만들 수 있는 다양한 팩토리 (Creator)를 제공할 수 있다.

> 구체적인 객체 생성 (`concrete product`)을 하위 클래스 (`concrete creator`)에서 구현하여, 부모 클래스(`creator`)가 생성되는 구체 클래스 (`concrete product`)를 모르게 캡슐화하는 패턴
> 

<br>
 

### 왜 필요한가?

---

- 좋은 객체지향 코드를 위해서는 OCP, SRP를 준수해야 한다.
- 만약 새로운 상품 (product)가 추가됬을때, 기존에 코드들이 변경된다면 이는 좋은 객체지향 프로그래밍이라고 볼 수 없다.
- 팩토리 메서드 패턴을 사용하면 `creator`와 `product`간의 결합도를 낮춤으로써, 위와 같은 생생로직에서 새로운 아이템이 추가되더라도 기존코드를 거의 손대지 않아도 된다. (`OCP`)
- 단일책임원칙을 고려할때, `구체적인 product`를 생성하는 책임은 `구체적인 creator`가 맡은 것이 바람직하다.

<br>
 

### 문제점?

---

- 많은 하위 클래스를 작성해야 하므로 애플리케이션의 코드 복잡도가 올라간다.
- 추상 골격 클래스까지 작성하게 되면 고계층 구조가 되어 더욱 복잡해진다.

<br>
 

### 구현 방법

---

- 확장에 열려 있고 변경에 닫혀 있는 구조로 만들어보자

![Untitled1](https://user-images.githubusercontent.com/91618389/198712512-c244a0f8-c2d1-4527-8c9f-edfdea792b5c.png)


**ShipFactory Interface**

```java
public interface ShipFactory {

    default Ship orderShip(String name, String email) {
        validate(name, email);
        prepareFor(name);
        Ship ship = createShip(); // 하위 클래스에서 정의하도록 한다.
        sendEmailTo(email, ship);
        return ship;
    }

    void sendEmailTo(String email, Ship ship){
			System.out.println(ship.getName() + " 다 만들었습니다." );
		}

		// 하위 클래스에서 정의
    Ship createShip();

    private void validate(String name, String email) {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("배 이름을 지어주세요.");
        }
        if (email == null || email.isBlank()) {
            throw new IllegalArgumentException("연락처를 남겨주세요.");
        }
    }

    private void prepareFor(String name) {
        System.out.println(name + " 만들 준비 중");
    }

}
```

<br>
 

<aside>
✅ Interface 안의 Default Method와 Private Method는 Java 9부터 사용 가능

</aside>

<br>
 

**DefaultShipFactory**

```java
public abstract class DefaultShipFactory implements ShipFactory {

    @Override
    public void sendEmailTo(String email, Ship ship) {
        System.out.println(ship.getName() + " 다 만들었습니다.");
    }

}
```
<br>
 

<aside>
✅ Interface 안의 Private Method를 만들지 않고 사용하기 위해서는 하위에 
Abstract Class를 만들어서 정의를 하도록 할 수 있다.

</aside>

<br>
 

**WhiteshipFactory Class**

```java
public class WhiteshipFactory extends DefaultShipFactory {

    @Override
    public Ship createShip() {
        return new Whiteship();
    }
}
```

**WhiteShip Class**

```java
public class Whiteship extends Ship {

    public Whiteship() {
        setName("whiteship");
        setLogo("\uD83D\uDEE5️");
        setColor("white");
    }
}
```

**실행 Client**

```java
public class Client {

    public static void main(String[] args) {
        Client client = new Client();

				Ship whiteship = new WhiteshipFactory().orderShip("Whiteship", "asd@gmail.com");
				System.out.println(whiteship);
	
    }

}
```


<br>
 

### OCP를 준수하면서 확장시켜보자

---

BlackshipFactory Class

```java
public class BlackshipFactory extends DefaultShipFactory {
    @Override
    public Ship createShip() {
        return new Blackship();
    }
}
```

**BlackShip** Class

```java
public class Blackship extends Ship {

    public Blackship() {
        setName("blackship");
        setColor("black");
        setLogo("⚓");
    }
}
```
<br>
 

<aside>
✅ 기존 코드를 건들지 않고, 새로운 공장과 새로운 제품을 추가했다.

확장을 했고, 기존 코드를 건드리지 않았다.

</aside>

<br>
 

**실행 Client**

```java
public class Client {

    public static void main(String[] args) {
        Client client = new Client();

				Ship whiteship = new WhiteshipFactory().orderShip("Whiteship", "asd@gmail.com");
				System.out.println(whiteship);
				Ship blackship = new BlackshipFactory().orderShip("BlackShip","asw@gmail.com");
			  System.out.println(blackship);
    }

}
```


<br>
 

<aside>
✅ BlackShip을 만들기 위해서, WhiteShipFactory에서 구체적인 객체를 생성하지 않고
`BlackShip`을 위한 `BlackShipFactory`를 따로 만들어 줬다.

</aside>

<br>
 

<aside>
❓ BlackShip을 만들기 위해, 현재 client 코드가 수정이 되었다.

그래서 이게 과연 변경에는 닫혀 있는지 의문을 품어야 한다.

Interface 기반으로 코드를 작성하고, Client 측에서 구체적인 클래스를 의존성 주입하는 방향을 사용하면 Client 코드도 최대한 변경이 되지 않게끔 사용할 수 있다.

</aside>

<br>
 

### 새로운 Factory가 생길때마다, Client 코드가 바뀌는 문제점 수정하기

---

```java
public class Client {

    public static void main(String[] args) {
        Client client = new Client();
        client.print(new WhiteshipFactory(), "whiteship", "keesun@mail.com");

        client.print(new BlackshipFactory(), "blackship", "keesun@mail.com");
    }

    private void print(ShipFactory shipFactory, String name, String email) {
        System.out.println(shipFactory.orderShip(name, email));
    }

}
```

<br>
 

<aside>
✅ 구체적인 Factory만 Client는 넘겨주면 된다.

print 하는걸 ShipFactory Interface로 바라보게 된다.
print 하는 역할을 최종적으로는 ShipFactory에서 하도록 위임했다.

Client 코드의 변경 최소화 가능

</aside>

<br>
 

### 전체적인 Class Diagram 보기

---

![Untitled2](https://user-images.githubusercontent.com/91618389/198712504-df2b25f9-fde2-48ff-81e1-1decef6e3faa.png)

<br>
 

<aside>
✅ ShipFactory : 배를 완전히 다 만든는데 필요한 공정들이 있다.

Ship : 배를 만드는데 필요한 기본적인 데이터

Client는 ShipFactory만 바라보고, 원하고자 하는 배를 만들도록 요청할 수 있다.

</aside>

<br>
 

<aside>
✅ Creator (Ship)을 만든곳도 계층 구조로 이루어져 있고

Product (ShipFactory)을 만드는 곳도 계층 구조로 이루어져 있다.

구처젝인 `Factory` 안에서 구체적인 `Product`를 만드는것이 중요하다

</aside>

<br>
 

### 팩토**리 메서드의 장단점**

---

- **팩토리 메소드 패턴을 적용했을 때의 장점은? 단점은?**
    - **장점**
        - OCP 원칙을 적용해서, 기존 코드를 건드리지 않고 확장이 가능하다.
        - `Product(인스턴스)`와 `Creator(Factory)` 사이의 관계를
        `느슨하게` `(느슨한 결합)` 가져갔기 때문이다.
        - `ShipFactory Interface`는 `Ship Class`만 바라보고
        Factory Interface를 구현하는 **Factory Class**가 **구체적인 Ship Class**를 바라보고 있다.
    - **단점**
        - Factory 패턴을 사용하게 되면, 각자의 역할을 나누다보니
        Class가 늘어다는 단점은 피할 수가 없다.
        
- **“확장에 열려있고 변경에 닫혀있는 객체 지향 원칙”**
    - 변경에 닫혀 있다는 의미는 기존 코드를 변경하지 않는다는 의미이다.
    - 기존 코드를 변경하지 않으면서, 새로운 코드를 확장할 수 있다.
    - BlackShip 공장을 만들기 위해, 기존의 WhiteShip 공장의 소스코드를 건드리지 않았다.

- **자바 8에 추가된 default 메소드**
    - Interface의 기본적인 구현체를 만들 수 있다.
    - 이전에는 Interface는 추상 메서드만 구현할 수 있었고, 자바 8부터는 Interface에 기본적인 구현체를 만들 수 있어서
    - Interface를 상속하는 혹은 구현하는 Class들이 해당 기능들을 사용할 수 있다.
    - Interface에 추가된 Private 메서드도 자바 9부터 사용할 수 있다.

<br>
 

### 다른 패턴과의 관계

---

- 많은 설계가 최초엔 팩토리 메서드 패턴을 사용해서 구현되지만, 점차 추상 팩토리, 프로토 타입 혹은 빌더 패턴으로 변화해가는 경향이 있다.
- 추상 팩토리 클래스들은 종종 여러 팩토리 메서드 패턴을 가지고 만들어지기도 한다.
- 팩토리 메서드 패턴은 탬플릿 메서드 패턴의 생성 패턴이라고 볼 수 있다.

