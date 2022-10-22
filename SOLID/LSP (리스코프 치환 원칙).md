# SOLID - LSP (리스코프 치환 원칙)

### 목차

- 리스코프 치환 원칙
- LSP 위반 사례
- LSP 준수 사례
- 정리

<br>
 
### 리스코프 치환 원칙, Liskov Subsitution Principle

---

리스코프 치환 원칙은 부모 객체와 이를 상속한 자식 객체가 있을 때 부모 객체를 호출하는 동작에서 

자식 객체가 부모 객체를 완전히 대체할 수 있다는 원칙이다.

객체지향 언어에선 객체의 상속이 일어난다. 이 과정에서 부모/자식 관계가 정의된다.

자식 객체는 부모 객체의 특성을 가지며, 이를 토대로 확장할 수 있다.

하지만 이 과정에서 무리하거나 객체의 의의와 어긋나는 확장으로 인해 잘못된 방향으로 상속되는 경우가 생긴다.

리스코프 치환 원칙은 올바른 상속을 위해 자식 객체의 확장이 부모 객체의 방향을 온전히 따르도록

권고하는 원칙이다.

<br>
 

### LSP 위반 사례

---

```java
/**
 * 직사각형 클래스
 * Getter, Setter 생략
 */
public class Rectangle
{
    protected int width;
    protected int height;
   
    /**
     * 넓이 반환 함수
     *
     * @return [int] 넓이
     */
    public int getArea()
    {
        return width * height;
    }
}
```

```java
/**
 * 정사각형 클래스
 * 
 */
public class Square extends Rectangle
{
    /**
     * 너비 할당 함수
     *
     * @param width: [int] 너비
     */
    @Override
    public void setWidth(int width)
    {
        super.setWidth(width);
        super.setHeight(getWidth());
    }
    
    /**
     * 높이 할당 함수
     *
     * @param height: [int] 높이
     */
    @Override
    public void setHeight(int height)
    {
        super.setHeight(height);
        super.setWidth(getHeight());
    }
}
```

위 처럼 정사각형 객체 Square 를 Rectangle의 상속을 통해 쉽게 구현할 수 있었다.

정사각형의 경우 직사각형과 달리 너비와 높이가 같으니, 너비나 높이를 지정하면

그에 맞게 너부와 높이를 모두 일치시켜주도록 오버라이딩을 수행 했다.

```java
public class Main
{
    public static void main(String[] args)
    {
        Rectangle rectangle = new Rectangle();
        rectangle.setWidth(10);
        rectangle.setHeight(5);
        
        System.out.println(rectangle.getArea());
    }
}
```

올바른 결과 출력

- 50 출력

리소코프 치환 원칙에 의하면, 자식 객체는 부모 객체를 완전히 대체할 수 있다고 했으므로,

Reactangle을 상속 받은 Square로 대체하여 넓이를 구해보자

Square가 Rectangle을 완전히 대체했다면 동일한 결과인 50이 반환되어야 한다.

```java
public class Main
{
    /**
     * 메인 함수
     *
     * @param args: [String[]] 매개변수
     */
    public static void main(String[] args)
    {
        Rectangle rectangle = new Square();
        rectangle.setWidth(10);
        rectangle.setHeight(5);
        
        System.out.println(rectangle.getArea());
    }
}
```

리스코프 대체 원리를 위반하는 출력

- 25 출력

출력 결과를 확인해보면, 50이 아닌 25로 반환됐다. 마지막에 수행된 `setHeight(5)` 가 객체의 너비/높이 모두 5로 할당했다.

그러니 넓이도 당연히 25가 출력될 수 밖에 없었던 걸로 보인다. 

**즉, 이 객체는 리스코프 치환 원칙에 위배되는 코드이다.**

잘 생각해보면 직사각형과 정사각형은 상속 관계가 전혀 될 수 없다.

사각형의 특징을 서로 갖고 있긴 하지만, 두 사각형 모두 사각형의 한 종류일뿐으로,

하나가 다른 하나를 완전히 포함하지 못 하는 구조이다.

이렇게 **잘못된 객체를 상속하거나, 올바르게 확장하지 못 할 경우** 겉으로 보기엔 정상적이지만 올바른 객체는 아니다.

<br>
 

### LSP 준수 사례

---

직사각형과 정사각형은 상속의 관계가 성립되기 어렵다.

따라서 이보다 더 상위 개념인 사각형 객체를 구현하고 정사각형, 직사각형이 이를 상속 받으면

될 것이다.

```java
/*
* 사각형 객체 
* Getter, Setter 생략
*/
public class Shape
{
    protected int width;
    protected int height;
    
    /**
     * 넓이 반환 함수
     *
     * @return [int] 넓이
     */
    public int getArea()
    {
        return width * height;
    }
}
```

위와 같이 Shape라는 사각형 객체를 구현한다.

```java
/**
 * 직사각형 클래스
 */
class Rectangle extends Shape
{
    public Rectangle(int width, int height)
    {
        super.setWidth(width);
        super.setHeight(height);
    }
}

-----------------------------------------------------------

/**
 * 정사각형 클래스
 */
class Square extends Shape
{
    public Square(int length)
    {
        super.setWidth(length);
        super.setHeight(length);
    }
}
```

Shape를 상속받는 두 사각형 Rectangle과 Square 객체는 위와 같다.

Rectangle은 인스턴스 생성 시 width와 height를 파라미터로 받으며, 

Square는 각 변의 길이가 모두 동일하므로 length 하나만을 파라미터로 받는다.

```java
public class Main
{
    /**
     * 메인 함수
     *
     * @param args: [String[]] 매개변수
     */
    public static void main(String[] args)
    {
        Shape rectangle = new Rectangle(10, 5);
        Shape square = new Square(5);
        System.out.println(rectangle.getArea());
        System.out.println(square.getArea());
    }
}
```

LSP 준수한 코드의 출력

- 50
- 25

이젠 더 이상 Rectangle과 Square가 상속 관계가 아니므로, 리스코프 치환 원칙의 영향에서 벗어났다.

<br>
 

### 정리

---

**리스코프 치환 원칙**

<aside>
✅ 상속되는 객체는 반드시 부모 객체를 완전히 대체해도 
아무런 문제가 없도록 권고한다.

</aside>

위의 직사각형과 정사각형의 케이스처럼 올바르지 못한 상속 관계는 제거하고,

더 추상적으로 접근하여 **부모 객체의 동작을 완벽하게 대체할 수 있는 관계만 상속 하도록 코드를 설계 해야 한다.**

리스코프 치환 원칙을 지키기 위해선 가급적 부모 객체의 일반 메소드를 그 의도와 다르게 오버라이딩 하지 않는 것이 중요하다.

애초에, 직사각형과 정사각형은 서로 상속 관계에 있다는것 부터 틀렸지만, 
그래도 코드를 한번 살펴보면

```java
/**
* 직시각형 너비 할당 (부모 객체)
*/
public void setWidth(int width)
    {
        this.width = width;
    }

---------------------------------------

/**
* 정사각형 너비 할당 (자식 객체)
*/
public void setWidth(int width)
    {
        super.setWidth(width);
        super.setHeight(getWidth());
    }
```

부모 (직사각형) 입장에선 `setWidth`는 사각형의 너비를 지정하기 위한 목적성을 가지고 있다.

하지만 자식(정사각형) 입장에선 `setWidth`는 사각형의 너비 뿐만 아니라 높이도 지정하는 목정성을 갖고 있다.

이처럼 부모 메서드의 목적성에 맞지 않는 추가적인 작업을 하는 코드를 추가하는 경우,

LSP 원칙을 위배했다고 판단할 수 있다.

앞서 언급했듯이, **LSP 원칙은 자식이 하는 행동을 부모가 대신 수행해도 그 결과가 같아야 하기 때문.**

부모 객체의 오버라이딩은 주로 동일한 메서드를 목적성을 위배하지 않는 선에서 

자식 객체만의 동작을 추가하기 위해 한다는걸 감안하면 매우 준수하기 까다로운 원칙이다.