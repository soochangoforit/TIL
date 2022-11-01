# 퍼사드 (Facade) 패턴


<br>
 
### 목차

- 퍼사드 패턴
- 적용 가능성
- 왜 필요할까?
- 문제점은?
- Before
- After
- 장단점
- 다른 패턴과의 관계


<br>
 

### 퍼사드 패턴

---

- 복잡한 서브 시스템 의존성을 최소화 하는 방법
- 클라이언트가 사용해야 하는 복잡한 서브 시스템 의존성을 간단한 인터페이스로 추상화 할 수 있다.

![a](https://user-images.githubusercontent.com/91618389/199482036-28e4a24a-57e8-4284-ac36-4fc27b6e62e2.png)

복잡한 의존성을 Client가 직접 의존하는게 아니라, 
Subsystem : Library , Framework

중간에 복잡한 서브 시스템에 대해서 특정한 Facade Class가 제공하는 Operation을 통해서 압축하고자 한다.

복잡한 것들을 Facade 뒤로 숨긴다.

Facade는 많이 움직이는 부분을 포함하는 복잡한 하위 시스템에 대한 간단한 인터페이스를 제공하는 클래스이다.

Facade는 하위 시스템으로 직접 작업하는 것과 비교하여 제한된 기능을 제공할 수 있다.

그러니 여기에는 클라이언트가 정말로 중요하게 생각하는 기능만 포함된다.

Facade가 갖는 것은 수십 가지 기능이 있는 정교한 라이브러리와 앱을 통합해야 할 때 편리하지만

기능은 필요한 기능만 만든다.

![b](https://user-images.githubusercontent.com/91618389/199482033-e4faa113-13a0-403e-a0e0-1c3a61e028b7.png)

1. Facade
    - 하위 시스템의 기능의 특정 부분에 대한 편리한 엑세스를 제공한다.
    - 클라이언트의 요청을 어디로 지시해야 하는지, 움직이는 모든 부품을 어떻게 작동해야 하는지 알고 있다.

1. Additional Facade
    - 관련 없는 기능으로 단일 기존 Class를 오염시켜 또 다른 복잡한 구조를 만들 수 있는 것을 방지하기 위해 생성할 수 있다.
    - Additional Facade는 클라이언트와 다른 Facade 모두에서 사용할 수 있다.

1. Complex Subsystem
    - 수십개의 다양한 객체로 구성된다. 모두 의미 있는 작업을 수행하려면 올바른 순서로 객체를 초기화하고 적절한 형식으로 데이터를 제공하는 거과 같은 하위 시스템의 구현 세부 정보를 알아야 한다.
    - 하위 시스템 클래스는 Facade의 존재를 인식하지 못 한다. 각 하위 클래스는 시스탬 내에서 맡은 행동을 하게 된다.

1. Client
    - 하위 시스템을 직접 호출하는 대신, Facade를 바라보게 된다.

<br>
 

### 적용 가능성

---

**복잡한 하위 시스템에 대한 제한적이지만 간단한 인터페이스가 필요한 경우 Facade 패턴을 사용해라**

- 종종 하위 시스템은 시간이 지남에 따라 더 복잡해진다.
- 디자인 패턴을 적용하더라도 일반적으로 더 많은 클래스가 생성된다.
- 하위 시스템은 다양한 컨텍스트에서 더 유연해지고 쉽게 재사용할 수 있지만 클라이언트에서 요구하는 구성 및 상용구 코드의 양은 점점 더 커진다.
- Facade 는 대부분의 클라이언트 요구 사항에 맞는 하위 시스템의 가장 많이 사용되는 기능에 대한 바로가기를 제공하여 이 문제를 해결하려고 시도한다.

**하위 시스템을 계충으로 구성하려는 경우 Facade를 사용해라**

- 서브 시스템의 각 레벨에 대한 진입점을 정의하기 위해 Facade를 작성하라.
- Facade를 통해서만 통신 하도록 요구함으로써 여러 하위 시스템 간의 결합을 줄일 수 있다.
- 이러한 방법은 “중재자 패턴”과 유사하다.

<br>
 

### 왜 필요할까?

---

- 특정 목적을 위해 여러 서브 시스템들을 오케스트레이션하여 하나의 큰 서비스를 제공한다고 가정해보자
- 문제는 이 커다란 서비스를 여러 클라이언트에게 사용하는 경우다.
- 여러 클라이언트마다 모든 서브시스템을 알고 그 서브 시스템을 오케스트레이션하는 구현 코드를 중복해서 기입하는 것은 비효율적이며, DRY 원칙에도 위배된다.
- DRY (Don’t Repeat Yourself) = 반복 금지의 원리, 복붙 금지 원칙!
- 이때 Facade 패턴으로 아주 손쉽게 구현을 인터페이스 뒤로 숨길 수 있다.
- 인터페이스를 사용하는 입장에서 이해가 쉬워지며, 개발의 유연성이 증가된다.

<br>
 

### 문제점은?

---

- 객체지행적인 관점에서 보면 책임을 적절하게 할당하는 것이 좋은 설계이며, 하나의 객체에 의존 관계가 집중되는 것을 지양한다.
- 하지만 퍼사드 패턴은 여러 의존관계를 하나의 인터페이스로 다시 모으기 때문에, 관점에 따라서는 절차지향적인 코드 스타일로 볼 수도 있으며, `God Object (너무 많은걸 알고 있고, 너무 많은걸 하는)` 로 취급될 수도 있다.
- 객체지향적 관점에서 퍼사드 패턴은 안티패턴으로 볼 여지도 충분히 있다고 판단한다.
- 실용적인 설계와 OOP간 트레이드 오프로 생각하자.

<br>
 

### Before

---

```java
import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;
import java.util.Properties;

public class Client {

    public static void main(String[] args) {
        String to = "keesun@whiteship.me";
        String from = "whiteship@whiteship.me";
        String host = "127.0.0.1";

        Properties properties = System.getProperties();
        properties.setProperty("mail.smtp.host", host);

        Session session = Session.getDefaultInstance(properties);

        try {
            MimeMessage message = new MimeMessage(session);
            message.setFrom(new InternetAddress(from));
            message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));
            message.setSubject("Test Mail from Java Program");
            message.setText("message");

            Transport.send(message);
        } catch (MessagingException e) {
            e.printStackTrace();
        }
    }
}
```

<br>
 

<aside>
💡 클라이언트 코드를 보면 알겠지만, 너무 많은 의존성을 가지고 있다.

</aside>

<br>
 

<br>
 

### After

---

![c](https://user-images.githubusercontent.com/91618389/199482030-ddca2ea4-e307-41aa-8e27-5bbaf202ae40.png)

<br>
 

<aside>
✅ 1. 메일 보내기 담당
2. 메일 설정 담당
3. 메일 메시지 내용 담당 으로 구분하고자 한다.

</aside>

<br>
 

**EmailSettings (**메일 설정 담당)

```java
public class EmailSettings {

    private String host;

    public String getHost() {
        return host;
    }

    public void setHost(String host) {
        this.host = host;
    }
}
```

**EmailMessage (**메일 메시지 내용 담당)

```java
public class EmailMessage {

    private String from;

    private String to;
    private String cc;
    private String bcc;

    private String subject;

    private String text;
}
```

**EmailSender (**메일 보내기 담당)

```java
public class EmailSender {

    private EmailSettings emailSettings;

    public EmailSender(EmailSettings emailSettings) {
        this.emailSettings = emailSettings;
    }

    /**
     * 이메일 보내는 메소드
     * @param emailMessage
     */
    public void sendEmail(EmailMessage emailMessage) {
        Properties properties = System.getProperties();
        properties.setProperty("mail.smtp.host", emailSettings.getHost());

        Session session = Session.getDefaultInstance(properties);

        try {
            MimeMessage message = new MimeMessage(session);
            message.setFrom(new InternetAddress(emailMessage.getFrom()));
            message.addRecipient(Message.RecipientType.TO, new InternetAddress(emailMessage.getTo()));
            message.addRecipient(Message.RecipientType.CC, new InternetAddress(emailMessage.getCc()));
            message.setSubject(emailMessage.getSubject());
            message.setText(emailMessage.getText());

            Transport.send(message);
        } catch (MessagingException e) {
            e.printStackTrace();
        }
    }

}
```

**Client** 

```java
public class Client {

    public static void main(String[] args) {
        EmailSettings emailSettings = new EmailSettings();
        emailSettings.setHost("127.0.0.1");

        EmailSender emailSender = new EmailSender(emailSettings);

        EmailMessage emailMessage = new EmailMessage();
        emailMessage.setFrom("keesun");
        emailMessage.setTo("whiteship");
        emailMessage.setCc("일남");
        emailMessage.setSubject("오징어게임");
        emailMessage.setText("밖은 더 지옥이더라고..");

        emailSender.sendEmail(emailMessage);
    }
}
```

<br>
 

<aside>
✅ 앞전과 달리 Client가 가지는 구체적인 의존성이 사라진다.

대신 Class의 의존성이 생긴건 어쩔 수 없다.
문법 또한 굉장히 간단해진다.

</aside>

<br>
 

<br>
 

### 장단점

---

- 장점
    - 서브 시스템에 대한 의존성을 한곳으로 모을 수 있다.

- 단점
    - 퍼사드 클래스가 서브 시스템에 대한 모든 의존성을 가지게 된다

<br>
 

### 다른 패턴과의 관계

---

- Facade는 기존 객체에 대한 새 인터페이스를 정의하는 반면, Adapter는 기존 인터페이스를 사용 가능하게 만들려고 한다. Adapter는 일반적으로 하나의 객체만을 래핑 하는 반면, Facadeㄴ느 객체의 하위 시스템과 함께 자공한다.
- Abstract Factory는 클라이언트 코드에서 서브 시스템 객체가 생성되는 방식마 숨기고 싶을 때 Facade의 대안으로 사용할 수 있다.
- FlyWeight는 많은 작은 객체들을 만드는 방법을 보여주는 반면, Facade는 전체 하위 시스템을 나타내는 단일 객체를 만드는 방법을 보여준다.
- Facade와 Mediator는 비슷한 역학을 한다. 밀접하게 연결된 많은 클래스 간의 협업을 구성하려고 한다.
    - Facade는 객체의 하위 시스템에 대한 단순화 된 인터페이스를 정의하지만 새로운 기능을 도입 하지는 않는다. 하위 시스템 자체는 Facadeㄹ르 인식하지 못 한다. 하위 시스템 내의 객체들은 직접 통신할 수 있다.
    - Mediator는 시스템 구성 요소 간의 통신을 중앙 집중화 한다. 구성 요소는 중재자 객체에 대해서만 알고 직접 하위 시스템 끼리는 통신하지 않는다.
- 대부분의 경우 단일 Facade 객체로 충분하기 때문에 Facade 클래스는 종종 싱글톤으로 변환될 수 있다.
- Facade는 복잡한 엔티티를 버퍼링하고 자체적으로 초기화한다는 점에서 Proxy와 유사하다. Facade와 달리 Proxy는 서비스 객체와 동일한 인터페이스를 가지고 있어 상호 교환이 가능하다.

