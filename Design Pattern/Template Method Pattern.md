# 템플릿 메소드 (Template method) 패턴

<br>
 
### 목차

- 템플릿 메소드 패턴
- 적용 가능성
- Before
- After (템플릿 메서드 패턴)
- 템플릿 콜백 패턴
- After (템플릿 콜백 패턴)
- 장단점
- 다른 패턴과의 관계


<br>
 

### 템플릿 메소드 (Template method) 패턴

---

![a](https://user-images.githubusercontent.com/91618389/199918261-208e2a35-7d80-4cbc-bbc9-46ddbf9766a6.png)

- 알고리즘 구조를 서브 클래스가 확장할 수 있도록 템플릿으로 제공하는 방법
- 추상 클래스는 템플릿을 제공하고 하위 클래스는 구체적인 알고리즘을 제공한다.

- **Abstract Class**
    - templateMethod : 알고리즘의 구조를 표현한 메서드
    - 일렬의 작업 (templateMethod)
        - 파일 가져오기 → 각각이 Step()로써 확장 가능
        - 파일 읽기 → 각각이 Step()로써 확장 가능
        - 읽은 숫자 더하기 → 각각이 Step()로써 확장 가능
        
- **Concrete Class**
    - 각각의 구체적인 하위 Class에서 부모의 Step()를 Override 한다.

> **템플릿 메서드** 는 슈퍼클래스에서 알고리즘의 골격을 정의하지만 서브클래스가 구조를 변경하지 않고 상속을 활용하여 알고리즘의 특정 단계를 재정의할 수 있도록 하는 행동 디자인 패턴이다.
> 

![b](https://user-images.githubusercontent.com/91618389/199918258-517a09a4-4a02-44ac-b066-54ff6ddf78c5.png)

위의 사진처럼 집을 구성하는데 기초적으로 필요한 4개의 단계가 있다. 상속을 이용한

하위 클래스에 따라서 4개의 단계를 각각 다르게 정의함으로써 최종적으로 다른 집의 형태를 갖출 수 있다.

![c](https://user-images.githubusercontent.com/91618389/199918256-744db804-8d0c-4fcf-8275-1d7d8de9d0e5.png)

1. 추상 클래스는 알고리즘의 단계로 작동하는 메서드와 이러한 메서드를 특정 순서로 호출하는 실제 `templateMethod()`를 선언한다. 단계를 의미하는 각각의 `step()` 메소드는 Abstract이 되거나 혹은 기본 구현이 될 수 있다.
2. 구체 클래스는 모든 `step()`  메서드를 재정의할 수 있지만 `templateMethod()` 자체는 이미 상위 Abstract Class에서 구현을 했기 때문에 재정의 할 수 없다.

<br>
 

### 적용 가능성

---

**클라이언트가 알고리즘의 특정 단계만 확장하고 전체 알고리즘이나 해당 구조는 확장하지 않도록 하려면 템플릿 메서드 패턴을 사용한다.**

- 템플릿 메서드를 사용하면 슈퍼클래스에 정의된 구조를 그대로 유지하면서 서브클래스에서 쉽게 확장할 수 있는 일련의 개별 단계로 전환할 수 있다.

**알고리즘을 Template Method Pattern으로 전환하면 유사한 구현이 있는 단계를 슈퍼 클래스로 끌어올 수 있어 코드 중복을 제거할 수 있다.** 

**하위 클래스 간에 다른 코드는 하위 클래스에 남을 수 있다.**

<br>
 

### Before

---

**FileProcessor** 

```java
public class FileProcessor {

    private String path;
    public FileProcessor(String path) {
        this.path = path;
    }

    public int process() {
        try(BufferedReader reader = new BufferedReader(new FileReader(path))) {
            int result = 0;
            String line = null;
            while((line = reader.readLine()) != null) {
                result += Integer.parseInt(line);
            }
            return result;
        } catch (IOException e) {
            throw new IllegalArgumentException(path + "에 해당하는 파일이 없습니다.", e);
        }
    }
}
```

<br>
 

<aside>
💡 파일을 읽어서 숫자 더하기

</aside>

<br>
 

<aside>
💡 만약, 곱하기가 추가 된다고 하면 새로운 Processor를 만들어서 Client가 사용해야 한다.
또한 중복 되는 코드가 발생한다.

</aside>

<br>
 

**Client** 

```java
public class Client {

    public static void main(String[] args) {
        FileProcessor fileProcessor = new FileProcessor("number.txt");
        int result = fileProcessor.process();
        System.out.println(result);
    }
}
```

<br>
 

### After (Template Method Pattern)

---

![d](https://user-images.githubusercontent.com/91618389/199918254-41a416e7-eb83-4bfc-9763-5067b0ef58f5.png)

**FileProcessor (Abstract Class)**

```java
public abstract class FileProcessor {

    private String path;
    public FileProcessor(String path) {
        this.path = path;
    }

    public final int process() {
        try(BufferedReader reader = new BufferedReader(new FileReader(path))) {
            int result = 0;
            String line = null;
            while((line = reader.readLine()) != null) {
                result = **getResult**(result, Integer.parseInt(line));
            }
            return result;
        } catch (IOException e) {
            throw new IllegalArgumentException(path + "에 해당하는 파일이 없습니다.", e);
        }
    }

    protected abstract int **getResult**(int result, int number);

}
```

<br>
 

<aside>
✅ process 는 TemplateMethod이다.

</aside>

<br>
 

<aside>
✅ process() 안에서 각각의 흐름을 step()별로 재정의가 필요한 경우
Abstarct Class를 구체화 한 Concrete Class에서 step()을 Override 한다.

</aside>

<br>
 

<aside>
✅ 하위 클래스에서 변경이 가능하도록 하기 위해서 Abstract Method 로 만들어준다.

</aside>

<br>
 

**Plus (Concreate Class)**

```java
public class Plus extends FileProcessor {

		public Plus(String path) {
			super(path);
		}

    @Override
    protected int getResult(int result, int number) {
        return result += number;
    }
}
```

**Multiply** **(Concreate Class)**

```java
public class Multiply extends FileProcessor {

    public Multiply(String path) {
        super(path);
    }

    @Override
    protected int getResult(int result, int number) {
        return result *= number;
    }

}
```

**Client** 

```java
public class Client {

    public static void main(String[] args) {
        FileProcessor fileProcessor = new Multiply("number.txt");
        int result = fileProcessor.process();
        System.out.println(result);
    }
}
```

<br>
 

### 템플릿 콜백 (Template - CallBack) 패턴

---

![e](https://user-images.githubusercontent.com/91618389/199918250-c2d3334d-fef9-4a0f-b8bb-7bc0daf78931.png)

- 콜백으로 상속 대신 위임으로 사용하는 템플릿 패턴
- 상속 대신 익명 내부 클래스 또는 람다 표현식을 활용할 수 있다.

- CallBack Interface
    - step() 이라는 계산을 Abstract로 담지 않고, Call Back Interface에 넣어둔다.
    - **Strategy Pattern** 같은 경우는 하나의 Process를 완성하기 위해
    여러 Method에서 구현한 하위 클래스를 런타임 시점에 바꿨지만
    - **CallBack Interface** 안에서의 Operation은 하나의 Method 만이
    하위 클래스에서 다양한 방식으로 구현한다.
        - 만약 CallBack에서 여러 Operation이 필요하면, 여러 Interface가 필요하다.
    

<br>
 

<aside>
✅ CallBack를 사용하게 되면, 상속을 사용하지 않아도 된다.
Strategy Pattern 처럼 **위임**을 사용 가능, Call Back Interface Operation을 구현하기 위해서
내부 익명 클래스나 람다식을 사용 가능하다.

</aside>

<br>
 

<br>
 

### **After (Template CallBack Pattern)**

---

**Operator  (CallBack Interface)**

```java
public interface Operator {

     int getResult(int result, int number);
}
```

<br>
 

<aside>
✅ 수행해야 하는 한가지의 Method를 Operator Interface로 선언한다.

</aside>

<br>
 

**FileProcessor (Class)**

```java
public class FileProcessor {

    private String path;
    public FileProcessor(String path) {
        this.path = path;
    }

    public final int process(Operator operatpr) {
        try(BufferedReader reader = new BufferedReader(new FileReader(path))) {
            int result = 0;
            String line = null;
            while((line = reader.readLine()) != null) {
                result = **operatpr**.**getResult**(result, Integer.parseInt(line));
            }
            return result;
        } catch (IOException e) {
            throw new IllegalArgumentException(path + "에 해당하는 파일이 없습니다.", e);
        }
    }

}
```

<br>
 

<aside>
✅ Operator를 process 파라미터로 입력 받고, 내부적으로 operator를 사용한다.
그리고 더 이상 Abstract Class가 될 필요가 없다.

</aside>

<br>
 

<aside>
✅ 해당 예제에서 CallBack Pattern 사용이 가능했던 이유는
**전략(행동)이 바뀌어야 하는 대상이 단 한가지 Method여서 가능했다.
물론 Interface를 여러개 만듬으로써 여러 Operation에 대해서 CallBack이 가능하도록 가능**

</aside>

<br>
 

```java
public class Plus implements Operator {

    @Override
    public int getResult(int result, int number) {
        return result += number;
    }

}
```

```java
public class Multiply implements Operator {

    @Override
    public int getResult(int result, int number) {
        return result *= number;
    }

}
```

```java
public class Client {

    public static void main(String[] args) {
        FileProcessor fileProcessor = new Multiply("number.txt");
				// 람다 표현식 사용
        int result = fileProcessor.process((sum, number) -> sum += number);

				// Operator를 implements한 ConcreteCallBack 사용
				int result2 = fileProcessor.process(new Plus());
        System.out.println(result);
    }
}
```

<br>
 

<aside>
✅ 큰 특징은 Template Method 패턴에서는 Concrete Class가 Abstract Class로 활용했지만

**Template CallBack 패턴**에서는 상속이 아닌 Interface를 통한 **“위임”** 방식으로 사용한다.

</aside>

<br>
 

<br>
 

### 장단점 (Template Method 패턴 장단점)

---

- 장점
    - 템플릿 코드를 재사용하고 중복 코드를 줄일 수 있다.
    - 템플릿 코드를 변경하지 않고 상속을 받아서 구체적인 알고리듬만 변경할 수 있다.
        - CallBack 같은 경우는 상속이 아니라 interface를 통한 위임이 가능하다.
    
- 단점
    - 리스코프 치환 원칙을 위반할 수도 있다.
        - 리스코프 치환 원칙
        - 상속 관계에서 부모가 의도했던 방향을 하위 자식 클래스에서 그대로 이행해야 한다.
            
            ```java
            // 원래 목적
            @Override
            protected int getResult(int result, int number) {
                return result *= number;
            }
            
            // 변형된 목적 (리스코프 원칙 위배)
            @Override
            protected int getResult(int result, int number) {
                return -1;
            }
            
            ```
            
    - 알고리듬 구조가 복잡할 수록 템플릿을 유지하기 어려워진다

<br>
 

### 다른 패턴과의 관계

---

- `Template Method Pattern`는 **상속**을 기반으로 한다.
    - 이 메서드를 사용하면 하위 클래스에서 해당 부분을 확장하여 알고리즘의 일부를 변경할 수 있다.
    - 또한 클래스 수준에서 작동하므로 정적이다.
- `Strategy Pattern` 은 **구성**을 기반으로 한다.
    - 해당 동작에 해당하는 다른 전략을 제공하여 개체 동작의 일부를 변경할 수 있다.
    - 또한 개체 수준에서 작동하므로 런타임에 동작을 전환할 수 있습니다.
