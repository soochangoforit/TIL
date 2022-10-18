# SOLID - DIP (의존성 역전 원칙)

### 목차

- [의존성 역전 원칙 (Dependency Inversion Principle)](https://www.notion.so/SOLID-DIP-a74baeb60dd341e6a09894f655a8db68)
- [의존성 역전 원칙을 준수하지 않은 코드](https://www.notion.so/SOLID-DIP-a74baeb60dd341e6a09894f655a8db68)
- [의존성 역전 원칙을 준수한 코드](https://www.notion.so/SOLID-DIP-a74baeb60dd341e6a09894f655a8db68)
- [정리](https://www.notion.so/SOLID-DIP-a74baeb60dd341e6a09894f655a8db68)

<br>

### 의존성 역전 원칙

---

의존성 역전 원칙이란 객체는 저수준 모듈보다 고수준 모듈에 의존해야한다는 원칙이다.

- 고수준 모듈 : 인터페이스와 같은 객체의 형태나 추상적 개념
- 저수준 모듈 : 구현된 객체

고/저수준 모델의 정의는 위와 같다. 위 정의를 의존성 역전 원칙에 대입하면, 
**객체는 객체보다 인터페이스에 의존해야한다**로 치환할 수 있다.

즉, 가급적 객체의 상속은 인터페이스를 통해 이루어져야 한다는 의미로 해석할 수 있다.

<br>

### 의존성 역전 원칙을 준수하지 않은 코드

---

```java
public class OneHandSword
{
    private final String NAME;
    private final int DAMAGE;
    
    /**
     * OneHandSword 생성자 함수
     *
     * @param name: [String] 무기 이름
     * @param damage: [int] 데미지
     */
    public OneHandSword(String name, int damage)
    {
        NAME = name;
        DAMAGE = damage;
    }
    
    /**
     * 공격 데미지 반환 함수
     *
     * @return [int] 공격 데미지 (데미지 +-5)
     */
    public int attack()
    {
        return DAMAGE + new Random().nextInt(10) - 5;
    }
    
    /**
     * 객체 문자열 반환 함수
     *
     * @return [String] 이름
     */
    @Override
    public String toString()
    {
        return NAME;
    }
}
```

수 많은 무기 가운데 하나인 한손검을 구현한 `OneHandSword` 객체가 있다.

캐릭터는 위와 같은 무기를 장비할 수 있다. 인스턴스 생성시 무기의 이름과 데미지를 입력하여 생성한다.

```java
public class Character
{
    private final String NAME;
    private int health;
		// 구체적인 객체에 의존
    private OneHandSword weapon;
    
    /**
     * Character 생성자 함수
     *
     * @param name: [String] 이름
     * @param health: [int] 체력
     * @param weapon: [OneHandSword] 무기
     */
    public Character(String name, int health, OneHandSword weapon)
    {
        NAME = name;
        this.health = health;
        this.weapon = weapon;
    }
    
    /**
     * 공격 데미지 반환 함수
     *
     * @return [int] 공격 데미지
     */
    public int attack()
    {
        return weapon.attack();
    }
    
    /**
     * 피격 함수
     *
     * @param amount: [int] 피격 데미지
     */
    public void damaged(int amount)
    {
        health -= amount;
    }
    
    /**
     * 무기 교체 함수
     *
     * @param weapon: [OneHandSword] 무기
     */
    public void chageWeapon(OneHandSword weapon)
    {
        this.weapon = weapon;
    }
    
    /**
     * 캐릭터 정보 출력 함수
     */
    public void getInfo()
    {
        System.out.println("이름: " + NAME);
        System.out.println("체력: " + health);
        System.out.println("무기: " + weapon);
    }
}
```

게임 캐릭터를 구현한 `Character` 객체다. 게임 캐릭터가 취할 수 있는 기본적인 행동 일부가 구현되어 있으며, 인스턴스 생성 시 캐릭터 이름, 체력, 무기를 입력하여 생성한다.

하지만, 무기엔 한손검만 있는 게 아니다. 무기만 하더라도 두손검, 단검, 창, 도끼, 둔기 등 다양한 종류가 존재할 수 있다. 그러나 이 `Character` 객체, 애초에 한손검 외엔 쓸 수가 없는 구조다.

`Character`의 인스턴스 생성 시 `OneHandSword`에 의존성을 가지기 때문, 공격 동작을 담당하는 `attack()` 메소드 역시 `OneHandSword`에 의존성을 가진다.

이 상황에서 한손검을 제외한 다른 무기를 사용하려면, `Character`의 코드를 바꿔야 한다. 

즉, OCP 규칙을 위배한다.

<br>

### 의존성 역전 윈칙을 준수한 코드

---

위 코드의 가장 큰 문제는 이미 완전하게 구현된 저수준 모듈을 의존하고 있다는 점이다.

즉, 추상적인 고수준 모듈을 의존하도록 리팩토링 해야 한다.

```java
public interface Attackable
{
    /**
     * 공격 추상 함수
     *
     * @return [int] 공격 데미지
     */
    int attack();
    
    /**
     * 객체 문자열 반환 추상 함수
     *
     * @return [String] 이름
     */
    @Override
    String toString();
}
```

우선, 고수준 모둘인 `Weapon` 인터페이스를 생성한다. 공격 데미지를 반환하는 추상 함수

`attack()`과 무기 이름을 반환하는 추상 함수 `toString()`가 선언되어 있다.

앞으로 모든 공격 가능한 무기 객체는 이 인터페이스를 상속 받게 될것이다.

```java
public class OneHandSword implements Attackable
{
    private final String NAME;
    private final int DAMAGE;
    
    /**
     * OneHandSword 생성자 함수
     *
     * @param name: [String] 무기 이름
     * @param damage: [int] 데미지
     */
    public OneHandSword(String name, int damage)
    {
        NAME = name;
        DAMAGE = damage;
    }
    
    /**
     * 공격 데미지 반환 함수
     *
     * @return [int] 공격 데미지 (데미지 +-5)
     */
    @Override
    public int attack()
    {
        return DAMAGE + new Random().nextInt(10) - 5;
    }
    
    /**
     * 객체 문자열 반환 함수
     *
     * @return [String] 이름
     */
    @Override
    public String toString()
    {
        return NAME;
    }
}
```

`Attackable`를 상속 받은 한손검 객체 `OneHandSword` 객체다.

상속 받은 점 외에 크게 달라진 점은 없다.

```java
public class Character
{
    private final String NAME;
    private int health;

		// 고수준 모듈의 Interface를 의존하고 있다.
    private Attackable weapon;
    
    /**
     * Character 생성자 함수
     *
     * @param name: [String] 이름
     * @param health: [int] 체력
     * @param weapon: [Attackable] 무기
     */
    public Character(String name, int health, Attackable weapon)
    {
        NAME = name;
        this.health = health;
        this.weapon = weapon;
    }
    
    /**
     * 공격 데미지 반환 함수
     *
     * @return [int] 공격 데미지
     */
    public int attack()
    {
        return weapon.attack();
    }
    
    /**
     * 피격 함수
     *
     * @param amount: [int] 피격 데미지
     */
    public void damaged(int amount)
    {
        health -= amount;
    }
    
    /**
     * 무기 교체 함수
     *
     * @param weapon: [Attackable] 무기
     */
    public void chageWeapon(Attackable weapon)
    {
        this.weapon = weapon;
    }
    
    /**
     * 캐릭터 정보 출력 함수
     */
    public void getInfo()
    {
        System.out.println("이름: " + NAME);
        System.out.println("체력: " + health);
        System.out.println("무기: " + weapon);
    }
}
```

게임 캐릭터 `Character` 객체다. 기존의 `OneHandSword`를 파라미터에서 좀 더 고수준 모듈인 

`Attackable`을 파라미터로 받는 걸 확인할 수 있다. 그 밖의 무기와 관련된 메소드 전부가 그렇다.

하나의 객체였던 저수준 모듈에서 고수준 모듈로 의존성이 바뀌게 되니, `Attackable`을 상속하는

모든 객체를 다룰 수 있다. 

게임 시스템 내부적으로 모든 공격 가능한 무기는 `Attackable`을 상속 받기로 가졍했으므로,

공격 가능한 모든 무기를 사용할 수 있는 셈이다.

이러한 변경으로 무기의 변경에 따라 `Character`의 코드를 변경할 필요가 없음으로, 

개방 폐쇄 원칙 (OCP) 또한 준수할 수 있다.

<br>

### 정리

---

의존성 역전 원칙 (DIP)은 코드의 확장성 및 재사용성을 추구하기 위한 원칙이다.

경직된 객체보다 구현되지 않아 유연한 인터페이스가 더욱 확장 가능성이 높을 것이다.

다른 원칙에 비해 의존성 역전 원칙은 중요도가 좀 떨어지는데, 그 이유는 타 원칙의 하위호환 격이기 때문이다.

당장 위에서도 언급했듯이, 의존성 역전 원칙은 **개방-폐쇄 원칙을 준수할 경우 자연스레 준수**한다.

뿐만 아니라 1객체 = 1책임인 **단일 책임 원칙**, 

기능별 인터페이스화를 추구하는 **인터페이스 분리 원칙**을 준수할 경우 역시 마찬가지다.

객체 생성 시 객체로 구현해야할 것과 인터페이스로 구현해야 할 것을 적절히 구분하여 올바른 의존 관계를 가지도록 구현하자.
