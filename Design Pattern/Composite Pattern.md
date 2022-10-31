# 컴포짓 (Composite)  패턴


<br>
 
### 목차

- Composite Pattern
- 적용 가능성
- Composition?
- What is Composition Over Inheritance
- Composition vs Composite Pattern?
- 왜 필요할까?
- 문제점은?
- Before
- After
- 전체 Diagram 보기
- 장단점
- 다른 패턴과의 관계

<br>
 

### 컴포짓 패턴

---

- 그룹 전체와 개별 객체를 동일하게 처리할 수 있는 패턴
- 클라이언트 입장에서는 ‘**전체**’나 ‘**부분**’이나 모두 동일한 컴포넌트로 인식할 수 있는 계층 구조를 만든다.
    - 대신 특정한 구조로 정해져 있다. (트리 구조)
- 컴포지트 패턴은 객체들의 관계를 트리구조로 표현하는 패턴으로, 사용자가 단일 객체와 복합 객체를 모두 동일하게 다둘 수 있도록 구조화한 패턴이다.

![0](https://user-images.githubusercontent.com/91618389/199470352-f672e18f-f46f-483b-b9b7-2f9ba8b784cd.png)

Client는 Component라는 Interface만 바라보게 된다.

Leaf는 가장 기본적인 단위가 된다.

Leaf와 같이 기본적인 단위를 “**그룹**”형태로 가져갈 수 있는 Composite 타입의 객체가 있다.

그래서 Composite는 여러개의 Component 배열을 가지고 있다.

하지만 보면 알겠지만 절대 Leaf 타입이 아니다.

Type이 **Component 타입**이다. 그래야지 `전체와 부분`을 동일하게 취급 가능

그룹으로 갖는 제네릭 Type이 Component Interface가 됨으로써
그룹을 다루는 커다란 **자기 자신의 객체(Composite)도 같은 Component에 담을 수 있게 가능**함으로써 ‘**전체**’나 ‘**부분**’이나 모두 동일한 컴포넌트로 인식할 수 있게 한다.

Composition Pattern을 사용하는 것은 앱의 핵심 모델을 트리로 나타낼 수 인는 경우에만 의미가 있다.

![1](https://user-images.githubusercontent.com/91618389/199470347-adf96398-bb21-46d6-a9f1-7691b6a619c8.png)

1. **Component Interface**
    - 트리의 단순 요소와 복잡한 요소 모두에 공통적인 작업을 설명한다.
    
2. **Leaf**
    - 하위 요소가 없는 트리의 기본 요소이다.
    - 일반적으로 리프 구성 요소는 작업을 위임할 사람이 없기 때문에 대부분의 실제 작업을 수행하게 된다.
    
3. **Composite**
    - 여러 하위 요소를 가지고 있는 요소이다.
    - Composite는 자식의 구체적인 클래스를 알지 못한다.
    - Component 인터페이스를 통해서 만든 모든 하위 요소와 함께 동작한다.
    - 요청을 받으면 Composite는 작업을 하위 요소에 위임하고 중간 결과를 처리한 다음 최종 결과를 클라이언트에 반환한다.

<br>
 

### 적용 가능성

---

**Tree와 같은 객체 구조를 구현해야 하는 경우 Composite 패턴을 사용한다.**

- Composite 패턴은 공통 인터페이스를 공유하는 2가지 기본 요소 유형인 Leaf와 Composite를 제공한다.
Composite는 Leaf와 다른 Composite으로 구성될 수 있다. 이를 통해 나무와 유사한 중첩된 재귀 객체 구조를 구성할 수 있다.

**클라이언트 코드가 단순 요소와 복합 요소를 모두 균일하게 처리하도록 하려면 Composite 패턴을 사용할 수 있다.**

- Composite 패턴에 의해 정의된 모든 요소는 공통 인터페이스를 공유한다.
- 이 인터페이스를 사용하면 클라이언트는 작업하는 객체의 구체적인 클래스에 대해 걱정할 필요가 없다.

<br>
 

### Composition?

---

컴포지션은 패턴이라기 보다는 설계, 개념, 컨셉으로서 하나의 객체가 다른 객체를 품고있는 경우를

일반적으로 말한다.

여기서 조금 더 나아가, 컴포지션(Composite)의 가장 밖에 위치한 객체 = Wrapper 객체는 내부에 품고 있는 객체와 생명주기가 강결합 되어 있는 편이다.

<br>
 

### What is `Composition Over Inheritance`

---

자바나 OOP에 대해 공부하다 보면 어느 순간 상속 보다는 결합(Composition)을 사용하라는 말을 듣게 된다.

다형성 구현에 있어서 상속은 부모 클래스가 구현하는 내용을 자식 클래스가 모두 물려받다 보니, 

하위 클래스에서 정작 쓰이지 않는 메서드도 갖게 되고, 부모 클래스의 변화에 따라 자식 클래스도 크게 영향을 받게 되므로 

캡슐화가 깨지게 된다는 주장이 있다

따라서, `is - a` 관계인 경우에만 상속을 사용하고, 보통은 `has - a` 객체간 결합을 사용해야하며 많은 공감을 받아 현재 OOP 진형의 대표적인 설계 기법으로 자리잡고 있다.

<br>
 

### Composition vs Composite Pattern?

---

Composite Pattern 의도

→ 복잡하게 얽혀있는 데이터 구조를 트리 구조로 계층화 시키고, 이러한 구조의 노드와 리프들을 모두 같은 인터페이스로 접근하도록 하려는 패턴

절대!! 상속의 한계와 그 해결책으로 나온 패턴이 아니라는 점이다.

`Composition`으로서의 패턴도 `Decorator` 패턴이지, 컴포지트 패턴이랑은 거리가 멀다.

> 컴포지트 패턴은 `Composition Over Inheritance` 와는 전혀 관련이 없다.
> 

<br>
 

### 왜 필요할까??

---

- 데이터를 다루다 보면, 계층형 트리 자료구조로 저장되고 이를 다루게 되는 경우가 종종 생긴다.
    - 흔히 폴더 - 파일 시스템 관점
- 이떄 Composite 패턴을 사용하면 클라이언트 측에서 모든 데이터를 모든 데이터를 모르더라도 복잡한 트리 구조를 쉽게 다룰 수 있다.
- 새로은 `Leaf` 로서의 클래스를 추가하더라도 클라이언트는 상위 추상화된 인터페이스만을 바라보기 때문에, OCP를 준수할 수 있다.

<br>
 

### 문제점은?

---

- 계층형 구주에서 `Leaf` 에 해당하는 객체와 Node에 해당하는 객체들 모두 동일한 인터페이스로 다루어야 하는데, 이 인터페이스 설계가 어려울 수 있다.
- 이럴 때는 디자인 패턴에 억지로 끼워 맞추려는 것은 아닌지 다시 생각해볼 필요가 있다.

<br>
 

### Before

---

```java
public class Bag {

    private List<Item> items = new ArrayList<>();

    public void add(Item item) {
        items.add(item);
    }

    public List<Item> getItems() {
        return items;
    }
}

-------------------------------------------------------------------------------
public class Item {

    private String name;

    private int price;
}

-------------------------------------------------------------------------------

public class Client {

    public static void main(String[] args) {
        Item doranBlade = new Item("도란검", 450);
        Item healPotion = new Item("체력 물약", 50);

        Bag bag = new Bag();
        bag.add(doranBlade);
        bag.add(healPotion);

        Client client = new Client();
        client.printPrice(doranBlade);
        client.printPrice(bag);
    }

    private void printPrice(Item item) {
        System.out.println(item.getPrice());
    }

    private void printPrice(Bag bag) {
        int sum = bag.getItems().stream().mapToInt(Item::getPrice).sum();
        System.out.println(sum);
    }

}

```

<br>
 

<aside>
💡 Client가 너무 많은걸 알고 있어야 한다.
하위의 Print 관련 메소드가 Client가 알아야할 메서드이다.

</aside>

<br>
 

<br>
 

### After

---

![2](https://user-images.githubusercontent.com/91618389/199470345-069cfb8b-d199-42ac-a67a-b87bab1c000a.png)

→ 그룹 전체와 개별 객체를 동일하게 처리할 수 있는 패턴.

**Component**

```java
public interface Component {

    int getPrice();

}
```

<br>
 

<aside>
✅ Component 대상인 **Item**과 **Bad**이 공통적으로 제공해야 하는 행동은
가격을 구하는것이다.

따라서 Component에는 getPrice()를 통해서 **Component 들이 가질 수 있는 공통적인 행동**을 선언했다.

</aside>

<br>
 

**Item**

```java
public class Item implements Component {

    private String name;

    private int price;

    public Item(String name, int price) {
        this.name = name;
        this.price = price;
    }

		// Component에 의해서 재정의 필요
    @Override
    public int getPrice() {
        return this.price;
    }
}
```

**Bag**

```java
public class Bag implements Component {

    private List<Component> components = new ArrayList<>();

    public void add(Component component) {
        components.add(component);
    }

    public List<Component> getComponents() {
        return components;
    }

		// Component에 의해서 재정의 수행
    @Override
    public int getPrice() {
        return components.stream().mapToInt(Component::getPrice).sum();
    }
}
```

- 앞전에는 Bag이 Leaf 단위의 Item을 바라보고 있었지만 지금은 Bag이 **Component를 제네릭**으로 담고 있다.
- Component를 구현하고 있는 하위 Leaf 들을 담을 수 있다.
- 앞전에는 Client 측에서 Bag에 들어있는 Item의 가격을 알기 위해 그 책임이 Client에 있었지만
지금은 그 책임의 행위가 Bag 쪽으로 위임이 되었다.

**Client**

```java
public class Client {

    public static void main(String[] args) {
        Item doranBlade = new Item("도란검", 450);
        Item healPotion = new Item("체력 물약", 50);

        Bag bag = new Bag();
        bag.add(doranBlade);
        bag.add(healPotion);

        Client client = new Client();
        client.printPrice(doranBlade);
        client.printPrice(bag);
    }

    private void printPrice(Component component) {
        System.out.println(component.getPrice());
    }

}
```

<br>
 

<aside>
✅ Client는 어떠한 물건의 가격을 알기 위해 
구체적인 클래스 Item 혹은 Bag를 알고 있는게 아니라, **Component라는 추상적인 객체**를 바라봄으로써 유연한 설계가 가능하다.

</aside>

<br>
 

<br>
 

<aside>
✅ 이런식으로 설계가 가능했던 이유는 Client가 Item과  Bag이라는 구체적인 Class를 바라보지 않고
Component라는 **추상적인 객체**로 바라보고

또한 **Component라는 Interface는** 궁극적으로 사용자가 **원하는 공통적인 행위에 대해서 선언**을 함으로써 Override를 활용하여 설계가 가능했다.

Client는 자신이 원하는 동작을 **Leaf 객체 혹은 Composite에게 행동을 위임했다.**

</aside>

<br>
 

<br>
 

### 전체 Diagram 보기

---

![3](https://user-images.githubusercontent.com/91618389/199470335-2eb11dc0-5f4d-4c24-9bad-57033eef1d5a.png)

<br>
 

### 장단점

---

→ 그룹 객체와 개별 객체를 동일하게 처리할 수 있는 패턴

- 장점
    - 복잡한 트리 구조를 편리하게 사용할 수 있다.
    - 다형성과 재귀를 활용할 수 있다.
    - 클라이언트 코드를 변경하지 않고 새로운 엘리먼트 타입을 추가할 수 있다.

- 단점
    - 트리를 만들어야 하기 때문에 (공통된 인터페이스를 정의해야 하기 때문에) 지나치게 일반화 해야하는
    경우도 생길 수 있다.

<br>
 

### 다른 패턴과의 관계

---

- 재귀적인 구조를 만들떄, Builder 패턴을 사용하면 훨씬 가독성이 좋아진다.
- iterator 패턴이나 visitor 패턴을 composite 패턴 트리에 작동되도록 할수도 있다.
- Leaf 노드에 경량 패턴(Flyweight)을 적용하여 램 사용을 줄일 수 있다.
- Decorator는 Composite와 비슷하지만 하나의 자식 구성 요소만 있다. 
또 다른 차이점은 Decorator는 래핑된 객체에 추가 책임을 추가하는 반면, Composite는 자식의 결과를 “요약”한다.

