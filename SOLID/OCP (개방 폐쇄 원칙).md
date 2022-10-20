# SOLID - OCP (개방 폐쇄 원칙)

### 목차

- [개방 폐쇄 원칙](#개방-폐쇄-원칙)
- [OCP 위반 사례](#OCP-위반-사례)
- [OCP 준수 사례](#OCP-준수-사례)
- [정리](#정리)

<br>
 
### 개방 폐쇄 원칙

---

**개방 폐쇄 원칙**이란 객체를 다룸에 있어서 객체의 확장은 개방적으로 객체의 수정은 폐쇄적으로 대하는 원칙이다.

한 마디로, 보여줄 건 보여주고, 숨길 건 숨긴다는 의미.

만약, 객체 하나를 수정한다고 가정하자. 이때 단순히 해당 객체만 수정하는 것 뿐만 아니라 해당 객체에 의존하는 다른 객체들의 코드까지 줄줄이 고쳐야 한다면 좋은 설계로 보기 힘들다.

대표적으로 라이브러리를 생각해보자. 라이브러리를 사용하는 객체의 코드가 변경된다고 변경에 따른 영향력을 낮추기 위한 원칙이다.

<br>
 

### OCP 위반 사례

---

```java
public class Pos
{
    /**
     * 결제 및 결과 반환 함수
     *
     * @param card : [Object] 카드 객체
     * @param name : [String] 카드사명
     * @param price: [int] 금액
     *
     * @return [boolean] 결제 결과
     */
    public boolean purchase(Object card, String name, int price)
    {
        boolean result;
        
        switch (card.toUpperCase())
        {
            case "A" -> result = ((CardA) card).send(price);
            case "B" -> result = ((CardB) card).send(price);
            case "C" -> result = ((CardC) card).send(price);
            
            default -> {
                System.out.println("유효하지 않은 카드사");
                result = false;
            }
        }
        
        return result;
    }
}
```

새로운 카드 결제를 위해 “C”라는 카드를 우리 결제 로직에 추가 했다.

Case 구문에서 초콜릿뱅크를 구분하여 결제 정보를 전송하면 해결되는것 처럼 보인다.

이렇게 하면 당장 급한 불은 끌 수 있지만, 후에 또 다른 카드사가 들어오면 또 다시 로직을 수정해야 하는 문제점이 발생한다.

이 방법은 매우 비효율적이다. 동작의 점위만 넓혔을 뿐, 근본적인 문제는 전혀 해결되지 않는다.

<br>
 

### OCP 준수 사례

---

```java
public interface Purchasable
{
    /**
     * 카드사 정보 전송 및 결과 반환 함수
     *
     * @param price: [int] 금액
     *
     * @return [boolean] 전송 결과
     */
    boolean send(int price);
}
```

공통된 형태로 로직을 수행하기 위해 Purchasable Interface를 구현했다.

또한 리더기에서 전송하는 모든 카드 객체는 Purchasable를 상속 받도록 강제했다.

```java
class CardA implements Purchasable
{
    /**
     * 카드사 정보 전송 및 결과 반환 함수
     *
     * @param price: [int] 금액
     *
     * @return [boolean] 전송 결과
     */
    @Override
    public boolean send(int price)
    {
        System.out.println(getClass().getSimpleName() + " " + price + "원 결제 요청");
        return true;
    }
}

----------------------------------------------------------------

class CardB implements Purchasable
{
    /**
     * 카드사 정보 전송 및 결과 반환 함수
     *
     * @param price: [int] 금액
     *
     * @return [boolean] 전송 결과
     */
    @Override
    public boolean send(int price)
    {
        System.out.println(getClass().getSimpleName() + " " + price + "원 결제 요청");
        return true;
    }
}

----------------------------------------------------------------

class CardC implements Purchasable
{
    /**
     * 카드사 정보 전송 및 결과 반환 함수
     *
     * @param price: [int] 금액
     *
     * @return [boolean] 전송 결과
     */
    @Override
    public boolean send(int price)
    {
        System.out.println(getClass().getSimpleName() + " " + price + "원 결제 요청");
        return true;
    }
}

```

이제 리더기에서 전달하는 모든 카드 객체는 Purchasable 인터페이스를 상속 받는다.

카드 객체를 부모 객체인 Purchasable로 다둘 수 있을 것이다. 각 카드 객체의 동작에 전송이 각각

구현되어있어, 타 객체의 코드에 의존하지 않는다.

```java
public class Pos
{
    /**
     * 결제 및 결과 반환 함수
     *
     * @param purchasable : [Purchasable] Purchasable 인터페이스
     * @param price: [int] 금액
     *
     * @return [boolean] 결제 결과
     */
    public boolean purchase(Purchasable purchasable, int price)
    {
        return purchasable.send(price);
    }
}
```

CardA, CardB, CardC 등 각각 개별적인 객체지만, 이제 Purchasable 이라는 부모 객체가 있으므로

이를 묶을 수 있다. 이젠 외부에서 전달해온 Purchasable 인터페이스 객체만 해당 객체의 send를 

호출하면 된다.

<br>
 

### 정리

---

```java
/**
* 리팩토링 전
*/
public boolean purchase(Object card, String name, int price)
{
    boolean result;
    
    switch (card.toUpperCase())
    {
        case "A" -> result = ((CardA) card).send(price);
        case "B" -> result = ((CardB) card).send(price);
        case "C" -> result = ((CardC) card).send(price);
        
        default -> {
            System.out.println("유효하지 않은 카드사");
            result = false;
        }
    }
    
    return result;
}

/**
* 리팩토링 후
*/
public boolean purchase(Purchasable purchasable, int price)
{
    return purchasable.send(price);
}
```

위는 이전 코드, 아래는 리팩토링한 코드다. 

**“기능이 변하거나 확장 가능하지만, 해당 기능의 코드는 수정하면 안된다”** 의미를 여기에서 찾을 수 있다.

리팩토링 이전 코드의 경우, 새로운 카드 인식. 즉 기능 추가를 위해선 코드의 추가가 요구됐다.

다시 말해, 기능을 확장하기 위해선 코드의 수정이 필요하다는 의미다.

반대로 리팩토링 후의 코드를 보면, Purchasable라는 통합된 인터페이스를 사용하기 때문에

카드 추가에 따라 코드 단계에서 대응할 필요가 없다.

즉, **코드의 변경 없이 기능이 확장**된다.

단일 책임 원칙과 마찬가지로, 비슷한 형태의 분기가 반복될 경우 개방-폐쇄 원칙을 준수하지 않았을 가능성이 높다.

이는 곧 높은 리팩토링 비용으로 직결된다. 이를 잘 준수하여 독립적인 모듈을 설계하는것이 좋다.
