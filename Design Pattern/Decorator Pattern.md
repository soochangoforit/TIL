# 데코레이터 패턴



### 목차

- 데코레이터 패턴
- Before
- After
- 전체 Diagram 보기
- 장단점

<br>
 
### 데코레이터 패턴

---

- 기존 코드를 변경하지 않고 부가 기능을 추가하는 패턴
- 상속이 아닌 위임을 사용해서 보다 유연하게(런타임에) 부가 기능을 추가하는 것도 가능하다.

![Untitled0](https://user-images.githubusercontent.com/91618389/198057808-080bff3f-b521-4706-9dc4-01a86f859d02.png)

- Component를 구현하고 있는, ConcreteComponent가 존재하고, Decorator가 존재한다.
    - 둘다 같은 Operation을 갖고 있다.
    
- 마치 여기까지는 컴포짓 패턴과 비슷하지만
    - Decorator에서는 여러개의 Component를 구현한 구현체를 가지고 있는게 아니라
    - 단 하나의 Component Type의 인스턴스를 가지고 있다.
    
- 그래서 Decorator는 자신이 감싸고 있는 단 하나의 Component를 호출함으로써 부가적인 정의가 가능하다.

<br>
 
<aside>
💡 Component interface : CommentService Interface
ConcreteComponent : CommentService Class

Decorator :
Concrete Decorator1 : `SpamFilteringCommentService` ,
Concrete Decorator 2: `TrimmingCommentService`

</aside>

<br>
 

### Before

---

```java
/**
* 댓글을 남겨주는 서비스
*/
public class CommentService {
    public void addComment(String comment) {
        System.out.println(comment);
    }
}

----------------------------------------------------------------------------------

public class Client {

    private CommentService commentService;

    public Client(CommentService commentService) {
        this.commentService = commentService;
    }

    private void writeComment(String comment) {
        commentService.addComment(comment);
    }

    public static void main(String[] args) {
        Client client = new Client(new CommentService());
        client.writeComment("오징어게임");
        client.writeComment("보는게 하는거 보다 재밌을 수가 없지...");
    }

}
```

출력
 → 오징어게임

 → 보는게 하는거 보다 재밌을 수가 없지…

<br>
 

<aside>
💡 간단하게 Client 객체의 생성 시점에 “댓글을 작성할 수 있는 서비스”를 넣어주고
해당 서비스를 통해서 Comment를 작성할 수 있게 되었다.

writeComment를 통해서 댓글을 출력할 수 있게 되었다.

</aside>

<br>
 

**만약, 사용자가 작성한 Comment에서 필요 없는 문자 “…”에서는 Trimming을 해주고 싶다.**

1. TrimmingCommentService 가 필요하다.
    
    ```java
    public class TrimmingCommentService extends CommentService {
    
        @Override
        public void addComment(String comment) {
            super.addComment(trim(comment));
        }
    
        private String trim(String comment) {
            return comment.replace("...", "");
        }
    
    }
    ```
    
2. Client 생성 시점에 해당 Service를 주입해줘야 한다.
    
    ```java
    public static void main(String[] args) {
            Client client = new Client(new TrimmingCommentService ());
            client.writeComment("오징어게임");
            client.writeComment("보는게 하는거 보다 재밌을 수가 없지...");
            client.writeComment("http://whiteship.me");
        }
    ```
    
3. 결과
    
→ 오징어 게임

→ 보는게 하는거 보다 재밌을 수가 없지

→ http://whiteship.me

<br>
 

<aside>
💡 기존의 코드는 변경 없이 필요한 Service를 생성하고 활용해주었다.

문제점은 뒤에서 차차 더 알아보자

</aside>

<br>
 

**만약, 댓글을 작성하는데 Http가 들어가있는 광고성 메시지를 차단해야 한다.**

1. SpamFilteringCommentService 를 만들어야 한다.
    
    ```java
    public class SpamFilteringCommentService extends CommentService {
    
        @Override
        public void addComment(String comment) {
            boolean isSpam = isSpam(comment);
            if (!isSpam) {
                super.addComment(comment);
            }
        }
    
        private boolean isSpam(String comment) {
            return comment.contains("http");
        }
    }
    ```
    
2. Client 생성 시점에 해당 Service를 주입해줘야 한다.
    
    ```java
    public static void main(String[] args) {
            Client client = new Client(new SpamFilteringCommentService());
            client.writeComment("오징어게임");
            client.writeComment("보는게 하는거 보다 재밌을 수가 없지...");
            client.writeComment("http://whiteship.me");
        }
    ```
    
3. 결과

    → 오징어 게임
    
    → 보는게 하는거 보다 재밌을 수가 없지…
    
<br>
 
<aside>
💡 광고성 메시지를 삭제하는데는 성공했지만, 현재 Trimming 이 동작하지 않는다.

**그러면!!!**
Trimming과 광고성 메시지를 차단한는 그런 특별한 Service를 새롭게 만들어서 적용해줘야 한다.

CommentService를 통한 **상속은 컴파일 시점에 해당 의존성이 정해져서, 유연한 설계를 하지 못하게 된다.

상속은 단일 상속만 가능하고, Trimming과 광고성 제거 Service를 2개 다 상속 받아서 만들고 싶지만,
대부분의 프로그래밍 언어에서는 다중 상속을 지원하지 않는다.

데코레이터 패턴을 사용하지 않고 상속으로 문제를 해결하려고 하면
경우의 수에 따 여러 Service를 만들어야 하는 문제점이 발생한다.**

</aside>

<br>
 
### After

---

![Untitled1](https://user-images.githubusercontent.com/91618389/198057801-ae7cc254-56f6-4747-9915-86f4eab3fee1.png)

**CommentService**

```java
public interface CommentService {

    void addComment(String comment);
}
```

**DefaultCommentService** 

```java
public class DefaultCommentService implements CommentService {
    @Override
    public void addComment(String comment) {
        System.out.println(comment);
    }
}
```

**CommentDecorator** 

```java
public class CommentDecorator implements CommentService {

    private CommentService commentService;

    public CommentDecorator(CommentService commentService) {
        this.commentService = commentService;
    }

    @Override
    public void addComment(String comment) {
        commentService.addComment(comment);
    }
}
```
<br>

<aside>
✅ 해당 Decorator는 중요한 특징이
바로 Component에 해당하는 CommentService **Interface**를 필드로 주입 받고 있는점이다.

CommentService Interface를 필드로 주입을 받고 있기 때문에
CommentService 구현체인

1. DefaultCommentService 
2. TrimmingCommentDecorator 
3. SpamFilteringCommentDecorator  가 들어올 수 있다.

왜냐하면 해당 Decorator도 **Comment Service Interface** 타입이기 떄문이다.

</aside>

<br>

**TrimmingCommentDecorator** 

```java
public class TrimmingCommentDecorator extends CommentDecorator {

    public TrimmingCommentDecorator(CommentService commentService) {
        super(commentService);
    }

    @Override
    public void addComment(String comment) {
        super.addComment(trim(comment));
    }

    private String trim(String comment) {
        return comment.replace("...", "");
    }
}
```
<br>

<aside>
✅ CommentDecorator를 **상속**해서 만들었다.

부모 Decorator에 필드로 Component Interface를 주입 받도록 했기 때문에
구현 Class인 TrimmingCommentDecorator 에서는 생성자로 부터 받은 값(Component)를 가지고  부모 Class인 CommentDecorator 의 Component 필드를 초기화 한다.

자식에서 부모의 Operation (addComment)을 **재정의**를 하는데 해당 ConcreteDecorator의 **목적에 맞게끔** 추가적인 정의를 한다.

</aside>

<br>

**SpamFilteringCommentDecorator** 

```java
public class SpamFilteringCommentDecorator extends CommentDecorator {

    public SpamFilteringCommentDecorator(CommentService commentService) {
        super(commentService);
    }

    @Override
    public void addComment(String comment) {
        if (isNotSpam(comment)) {
            super.addComment(comment);
        }
    }

		/**
		* 광고성 댓글 삭제
		*/
    private boolean isNotSpam(String comment) {
        return !comment.contains("http");
    }
}
```

**Client** 

```java
public class Client {

    private CommentService commentService;

    public Client(CommentService commentService) {
        this.commentService = commentService;
    }

    public void writeComment(String comment) {
        commentService.addComment(comment);
    }
}
```
<br>

<aside>
✅ 추상화를 바라보기 위해서 CommentService Interface를 바라보게 된다.
DIP 만족하기 위해서

</aside>

<br>

**App**

```java
public class App {

    private static boolean enabledSpamFilter = true;

    private static boolean enabledTrimming = true;

    public static void main(String[] args) {
        CommentService commentService = new DefaultCommentService();

        if (enabledSpamFilter) {
            commentService = new SpamFilteringCommentDecorator(commentService);
        }

        if (enabledTrimming) {
            commentService = new TrimmingCommentDecorator(commentService);
        }

        Client client = new Client(commentService);
        client.writeComment("오징어게임");
        client.writeComment("보는게 하는거 보다 재밌을 수가 없지...");
        client.writeComment("http://whiteship.me");
    }
}
```
<br>

<aside>
✅ 우리가 사용할 service들이 런타임 시점에 동적으로 변경될 수 있다.

런타임에 동적으로 변경이 가능했던 이유는 각각의 **CommentDecorator 구현체들이 다 CommentService Interface를 모두 Implements 했기 때문이다.**

</aside>

<br>

**출력**

→ 오징어 게임

→ 보는게 하는거 보다 재밌을 수가 없지

<br>


<aside>
✅ Before 코드처럼 상속으로 끝낼려고 했을때는 또 다른 상속 클래스를 만들었어야 했다.

이제는 그럴 필요가 없다.
왜나햐면, CommentDecorator가 또 다른 Decorator 구현체를 Component Interface로
감쌀수 있기 때문이다. 

→ 3개의 구체적인 Decorator가 최상위로는 **CommentService Interface Type**이기 때문이다.
추가적으로 기본 **ConcreteDecorator를 상속** 했기 때문에 **행동을 원하는 목적에 맞게금 재정의**가 가능하다.

</aside>

<br>

### 전체 Dirgram 보기

---

![Untitled3](https://user-images.githubusercontent.com/91618389/198057793-6e06892e-c9af-4642-83b8-8f1210d6056d.png)

<br>
 
### 장단점

---

기존 코드를 변경하지 않고 부가 기능을 추가하는 패턴

- 장점
    - 새로운 클래스를 만들지 않고 기존 기능을 조합할 수 있다.
    - 컴파일 타임이 아닌 런타임에 동적으로 기능을 변경할 수 있다.

<br>
 
<aside>
✅ OCP와 DIP 원칙을 준수할 수 있다.

</aside>

<br>
 

- 단점
    - 데코레이터를 조합하는 코드가 복잡할 수 있다.
