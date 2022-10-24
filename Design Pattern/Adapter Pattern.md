# 어댑터 패턴 (Adapter Pattern)

유형: 정리 필요
작성자: 수찬 이

### 목차

- 어댑터 (Adapter) 패턴
- Before
- After
- 장단점

<br>
 
### 어댑터 (Adapter) 패턴

---

- 기존 코드를 클라이언트가 사용하는 인터페이스의 구현체로 바꿔주는 패턴
- 클라이언트가 사용하는 인터페이스를 따르지 않는 기존 코드를 재사용할 수 있게 해준다.

![Untitled1](https://user-images.githubusercontent.com/91618389/197550860-163ce0b3-7502-435c-8dc2-daf07f225695.png)

- Target인 미국의 돼지코이며, Client는 어쩔 수 없이 우선 미국 돼지코를 사용해야 한다.
- 한국에서 가져온 Adaptee는 미국의 전압과 맞지 않는다.
- 미국의 돼지코 Target과 Adaptee 사이를 이어주는 Adapter가 필요하다.

<br>
 

### Before

---

![Untitled](https://user-images.githubusercontent.com/91618389/197551030-81819a4d-1e96-4fcc-8238-6e0c5a2e62a7.png)

- Security에서 제공하는 실제 기능
    - Target에 해당한다.
- Account, AccountService
    - 우리의 서비스에 맞는 필요한 로직으로 구성되어야 한다.
    - 위의 다이어그램에서 Adaptee에 해당한다.

- loginHandler
    
    ```java
    public class LoginHandler {
    
        UserDetailsService userDetailsService;
    
        public LoginHandler(UserDetailsService userDetailsService) {
            this.userDetailsService = userDetailsService;
        }
    
        public String login(String username, String password) {
            UserDetails userDetails = userDetailsService.loadUser(username);
            if (userDetails.getPassword().equals(password)) {
                return userDetails.getUsername();
            } else {
                throw new IllegalArgumentException();
            }
        }
    }
    ```
    
- UserDetails
    
    ```java
    public interface UserDetails {
    
        String getUsername();
    
        String getPassword();
    
    }
    ```
    
- UserDetailService
    
    ```java
    public interface UserDetailsService {
    
        UserDetails loadUser(String username);
    
    }
    ```
    
- Account
    
    ```java
    public class Account {
    
        private String name;
    
        private String password;
    
        private String email;
    }
    ```
    
- AccountService
    
    ```java
    public class AccountService {
    
        public Account findAccountByUsername(String username) {
            Account account = new Account();
            account.setName(username);
            account.setPassword(username);
            account.setEmail(username);
            return account;
        }
    
        public void createNewAccount(Account account) {
    
        }
    
        public void updateAccount(Account account) {
    
        }
    
    }
    ```
    
<br>
 

### After

---

<aside>
✅ 미국 돼지코인 Target과 내가 가지고 있는 Adaptee가 서로 상호작용하기 위해서 Adapter 생성

</aside>

![Untitled-1](https://user-images.githubusercontent.com/91618389/197551027-5d418fd3-e02d-48f8-a349-71e8244afe46.png)

**AccountUserDetailsService**

```java
public class AccountUserDetailsService implements UserDetailsService {

    private AccountService accountService; // Adaptee에 해당

    public AccountUserDetailsService(AccountService accountService) {
        this.accountService = accountService;
    }

    @Override
    public UserDetails loadUser(String username) {
        return new AccountUserDetails(accountService.findAccountByUsername(username));
    }
}
```

**AccountUserDetails**

```java
public class AccountUserDetails implements UserDetails {

    private Account account;

    public AccountUserDetails(Account account) {
        this.account = account;
    }

    @Override
    public String getUsername() {
        return account.getName();
    }

    @Override
    public String getPassword() {
        return account.getPassword();
    }
}
```

App

```java
public class App {

    public static void main(String[] args) {
        AccountService accountService = new AccountService();
        UserDetailsService userDetailsService = new AccountUserDetailsService(accountService);
        LoginHandler loginHandler = new LoginHandler(userDetailsService);
        String login = loginHandler.login("keesun", "keesun");
        System.out.println(login);
    }
}
```

<aside>
✅ Adapter에 대한 별도의 코드를 만들었고 기존의 Target(Security)에 대한 코드는 건드리지 않았다.

</aside>

<aside>
✅ 물론 각각의 Account와 AccountService가  UserDetails, UserDetailsService를  implement하게 해도 괜찮지만 , SRP 원칙에 위배된다.

</aside>

<br>

 
### 장단점

---

- 장점
    - 기존 코드를 변경하지 않고 원하는 인터페이스 구현체를 만들어 재사용할 수 있다.
        - OCP 원칙을 잘 준수하는 디자인 패턴이라고 볼 수 있다.
    - 기존 코드가 하던 일과 특정 인터페이스 구현체로 변환하는 작업을 각기 다른 클래스로
    분리하여 관리할 수 있다.

- 단점
    - 새 클래스가 생겨 복잡도가 증가할 수 있다. 경우에 따라서는 기존 코드가 해당 인터페이스를 구현하도록 수정하는 것이 좋은 선택이 될 수도 있다.

