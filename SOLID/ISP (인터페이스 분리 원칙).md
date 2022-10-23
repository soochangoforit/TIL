# SOLID - ISP (인터페이스 분리 원칙)

### 목차

- 인터페이스 분리 원칙
- ISP 위반 사례
- ISP 준수 사례
- 정리

<br>
 
### 인터페이스 분리 원칙, Interface Segrepation Principle

---

**인터페이스 분리 원칙**이란 **객체는 자신이 호출하지 않는 메소드에 의존하지 않아야 한다**는 원칙이다.

구현할 객체에게 무의미한 메소드의 구현을 방지하기 위해 반드시 필요한 메서드만을

상속/구현 하도록 권고한다. 

만약 상속할 객체의 규모가 너무 크다면, 해당 객체의 메소드를 작은 인터페이스로 나누는 것이 좋다.

![image](https://user-images.githubusercontent.com/91618389/197391165-990041c2-5930-4171-8273-a1bba0859114.png)


위 그림은 규모가 너무 큰 객체를 상속했을 때 발생하는 문제와, 이를 인터페이스로 분리하여 해결

하는 방법을 도식한 것이다.

왼쪽과 오른쪽 객체가 가운대 객체를 상속할 경우, 왼쪽 객체는 필요한 메소드가 모두 구현되기 

때문에 아무런 문제가 없다. 

그러나 오른쪽 객체의 경우, Method1을 제외한 나머지 메소드는 필요가 없다. 

하지만 이를 상속했기 때문에, 좋든 싫든 해당 메소드를 가지고 있거나, 최악의 경우

필요 없는 메소드를 구현까지 해야만 한다.

하지만 밑에 그림처럼 상속 대상인 객체의 메소드를 각 동작별로 구분해 인터페이스로 만들어 보면

각 객체가 필요한 인터페이스만을 상속하여 구현하면 되므로 

**각자가 필요한 메소드만을 가지게 된다.** 이것이 인터페이스 분리 원칙이 지향하는 바다.

<br>
 

### ISP 위반 사례

---

예를 들어, 스마트 폰이라는 객체가 있다고 가정하자. 이 스마트폰 객체는 비교적 최신에 나온 

덕분에 일반적인 스마트폰 기능 외에도 무선 충전, AR 뷰어, 생체 인식 등의 다채로운 기능을 포함하고 있다.

이를 가지고 S20을 구현하면 스마트폰 객체의 동작 모두가 필요하므로 ISP를 만족한다.

그러나 S2를 구현할 경우, 무선 충전, 생체 인식과 같은 기능을 제공하지 않는다.

그럼에도 불구하고 부모 객체인 스마트폰에 이러한 인터페이스가 포함되어 있으므로,

S2입장에서는 필요하지도 않은 기능을 구현해야하는 낭비가 발생한다.

```java
/**
 * 스마트폰 추상 객체
 *
 */
abstract public class SmartPhone
{
    /**
     * 통화 함수
     */
    public void call(String number)
    {
        System.out.println(number + " 통화 연결");
    }
    
    /**
     * 문자 메시지 전송 함수
     */
    public void message(String number, String text)
    {
        System.out.println(number + ": " + text);
    }
    
    /**
     * 무선충전 함수
     */
    public void wirelessCharge()
    {
        System.out.println("무선 충전");
    }
    
    /**
     * AR 함수
     */
    public void ar()
    {
        System.out.println("AR 기능");
    }
    
    /**
     * 생체인식 추상 함수
     */
    abstract public void biometrics();
}
```

위와 같이 구현된 SmartPhone 객체가 있다.

생체 인식을 담당하는 `bigmetrics()` 메소드의 경우, 

기기에 등록된 생체 정보를 활용해야 하므로 추상 메소드로 선언되어있다. 이 객체를 상속하여 S20과 S2를 구현할 수 있을 것이다.

```java
/**
 * S20 객체
 */
public class S20 extends SmartPhone
{
    /**
     * 생체인식 함수
     */
    @Override
    public void biometrics()
    {
        System.out.println("S20 생체인식 기능");
    }
}
```

S20은 모든 기능이 필요하기 때문에, SmartPhone의 모든 메소드를 사용해야하므로, 

**불필요한 메소드가 없는 상태다.**

```java
/**
 * S2 객체
 *
 */
public class S2 extends SmartPhone
{
    /**
     * 무선충전 함수
     */
    @Override
    public void wirelessCharge()
    {
        System.out.println("지원 불가능한 기기");
    }
    
    /**
     * AR 함수
     */
    @Override
    public void ar()
    {
        System.out.println("지원 불가능한 기기");
    }
    
    /**
     * 생체인식 추상 함수
     */
    @Override
    public void biometrics()
    {
        System.out.println("지원 불가능한 기기");
    }
}
```

S2 는 무선충전, AR, 생체 인식이 지원되지 않는 기기다. 그럼에도 불구하고 SmartPhone의 상속으로 인해 해당 기능의 메소드를 강제로 상속 받게 된다.

더군다나 `biometrics()`의 경우 추상 메소드이므로 필요하지도 않은 기능을 구현까지 해야한다.

이러한 상속의 특징은 부모 객체의 규모가 매우 클 경우, 개발 편의성의 극심한 저하로 이뤄진다.

<br>
 

### ISP 준수 사례

---

객체의 특성을 확장을 통해 다른 객체를 편하게 구현하기 위한 것이 상속인데

위와 같은 상황은 전혀 편하지 않다. 이는 부모 객체의 설계가 잘못 됐을 수도 있고,

취지에 맞지 않는 객체를 상속했을 수도 있다. 

해결 방법은 **객체의 메소드를 각각 인터페이스로 만들면 된다.** 

**각 객체는 필요한 인스턴스만 상속하면 되므로 필요한 동작만을 상속/구현할 수 있을것이다.**

```java
/**
 * 스마트폰 객체
 *
 */
public class SmartPhone
{
    /**
     * 통화 함수
     */
    public void call(String number)
    {
        System.out.println(number + " 통화 연결");
    }
    
    /**
     * 문자 메시지 전송 함수
     */
    public void message(String number, String text)
    {
        System.out.println(number + ": " + text);
    }
}
```

SmartPhone 객체는 모든 스마트폰에 적용되는 보편적인 동작만을 가지도록 변경했다.

```java
/**
 * 무선충전 인터페이스
 *
 */
public interface WirelessChargable
{
    /**
     * 무선충전 추상 함수
     */
    void wirelessCharge();
}

/**
 * AR 인터페이스
 */
public interface ARable
{
    /**
     * AR 추상 함수
     */
    void ar();
}

/**
 * 생체인식 인터페이스
 */
public interface Biometricsable
{
    /**
     * 생체인식 추상 함수
     */
    void biometrics();
}
```

각 기능의 인터페이스를 위와 같다. 원래 SmartPhone의 객체의 메소드였던 각 기능은 인터페이스 단위로 나뉘어졌음에 주목하자,

이를 통해 S20과 S2는 모두 각자가 필요한 객체만을 상속 받아 구현할 수 있게 된다.

**ISP 원칙을 준수하는 S20 객체**

```java
/**
 * S20 객체
 */
public class S20 extends SmartPhone implements WirelessChargable, ARable, Biometricsable
{
    /**
     * 무선충전 함수
     */
    @Override
    public void wirelessCharge()
    {
        System.out.println("무선충전 기능");
    }
    
    /**
     * AR 함수
     */
    @Override
    public void ar()
    {
        System.out.println("AR 기능");
    }
    
    /**
     * 생체인식 함수
     */
    @Override
    public void biometrics()
    {
        System.out.println("생체인식 기능");
    }
}
```

S20 객체의 코드다. SmartPhone 을 상속 받았으며, 인터페이스로 `WirelessChargable`, `ARable`, `Biometricsable`을 모두 상속 받아 구현하고 있다.

**ISP 원칙을 준수하는 S2 객체**

```java
/**
 * S2 객체
 */
public class S2 extends SmartPhone
{
    /**
     * 문자 메시지 전송 함수
     */
    @Override
    public void message(String number, String text)
    {
        System.out.println("In S2");
        
        super.message(number, text);
    }
}
```

S2는 특수 기능이 구현되어 있지 않으므로, 기본적인 SmartPhone 객체만들 상속 받아 구현된다.

인터페이스는 다중 상속을 지원하므로, 필요한 기능을 인터페이스로 나누면 해당 기능만들 상속

받을 수 있다. 그 밖에 추후 업데이트 등을 통해 추가적인 기능이 탑재되다면, 같은 원리로 

인터페이스를 설계해서 사용하면 객체에 필요한 기능을 쉽게 추가할 수 있게 된다.

<br>
 

### 정리

---

**인터페이스 분리 원칙은 객체가 반드시 필여한 기능(행동)만들 가지도록 제한하는 원칙이다.**

불필요한 기능의 상속/구현을 최대한 방지함으로써 객체의 불필요한 책임을 제거한다.

큰 규모의 객체는 필요에 따라 인터페이스로 잘게 나누어 확장성을 향상 시킨다.

객체를 상속할 땐 해당 객체에 적합한 객체인지, 의존적인 기능이 없는 지, 상속할때 불필요한 Operation은 없는 판단하여 올바른 객체를 구현, 상속 하도록 하자.