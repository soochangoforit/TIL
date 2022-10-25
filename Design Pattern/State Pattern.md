# 상태 (State) 패턴

### 목차

- 상태 (State) 패턴
- Before
- After
- 장단점

<br>
 
### 상태 (State) 패턴

---

![Untitled0](https://user-images.githubusercontent.com/91618389/197791724-51b0828c-08fc-44b7-9172-e841d025130f.png)


- 객체 내부 상태 변경에 따라 객체의 행동이 달라지는 패턴
- 상태에 특화된 행동들을 분리해 낼 수 있으며, 새로운 행동을 추가하더라도 다른 행동에 영향을 주지 않는다.

- **State Interface**
    - Context가 변경될 수 있는, 여러 상태들에 대한 공통된 인터페이스를 만들어 놓았다.
        - 디자인 패턴 적용 전에는 주로, Context 로직에서 뭔가, if - else로 객체의 상태에 따라서 다르게 행동이 필요한 Method를 가지고 있다.
    - 해당 State interface ( 혹은 abstract class)는 반드시 필요하다.

- **Context**
    - 밑에 예시에서는 Online Course가 대표적인 예시이다.
    - 상태 변경을 위한 Method (changeState)를 갖는다. 또한 정상 범위에서의 행동을 정의한다.
    - Context가 가지고 있어야 할 고유한 Field 정보들을 가지고 있는 Class
    - Context 상태에 따른 각기 다른 행동들을 **State Interface 구체적인 구현체(ConcreteState)**에 위임한다.

- **ConcreteState**
    - Context의 상태 ( State)에 따라 달라지는 Context Operation() 들을 실질적으로
    ConcreteState에서 구현을 한다.
    - Context의 특정 상태일때 ~로 행동한다가 정의되어 진다.

<br>
 

### Before

---

**Client** 

```java
public class Client {

    public static void main(String[] args) {
        Student student = new Student("whiteship");
        OnlineCourse onlineCourse = new OnlineCourse();

        Student keesun = new Student("keesun");
        keesun.addPrivateCourse(onlineCourse);

        onlineCourse.addStudent(student);
        onlineCourse.changeState(OnlineCourse.State.PRIVATE);

        onlineCourse.addStudent(keesun);

        onlineCourse.addReview("hello", student);

        System.out.println(onlineCourse.getState());
        System.out.println(onlineCourse.getStudents());
        System.out.println(onlineCourse.getReviews());
    }
}
```

**OnlineCourse** 

```java
public class OnlineCourse {

    public enum State {
        DRAFT, PUBLISHED, PRIVATE
    }

    private State state = State.DRAFT;

    private List<String> reviews = new ArrayList<>();

    private List<Student> students = new ArrayList<>();

		/**
		* PUBLISHED 상태인 경우는 review 누구나 작성 가능
		*
		* PRIVATE 인 경우, 해당 Online Course를 등록한 사람만 review 작성 가능
		*
		* DRAFT 상태일때는 아무도 리뷰를 작성하지 못 한다.
		*/
    public void addReview(String review, Student student) {
        if (this.state == State.PUBLISHED) {
            this.reviews.add(review);
        } else if (this.state == State.PRIVATE && this.students.contains(student)) {
            this.reviews.add(review);
        } else {
            throw new UnsupportedOperationException("리뷰를 작성할 수 없습니다.");
        }
    }

		/**
		* PUBLISHED , DRAFT 상태인 경우는 학생 등록 가
		*
		* PRIVATE 인 경우, 해당 학생이 강의에 대해 avaiable 해야지만 추가 가능(구매시)
		*
		* DRAFT 상태일때는 아무도 수업에 참여하지 못 한다.
		*
		* 학생이 2명일 경우는 PRIVATE으로 전환한다.
		*/
    public void addStudent(Student student) {
        if (this.state == State.DRAFT || this.state == State.PUBLISHED) {
            this.students.add(student);
        } else if (this.state == State.PRIVATE && availableTo(student)) {
            this.students.add(student);
        } else {
            throw new UnsupportedOperationException("학생을 해당 수업에 추가할 수 없습니다.");
        }

        if (this.students.size() > 1) {
            this.state = State.PRIVATE;
        }
    }

		/**
		* 학생이 해당 Online Course를 구매한 경우, True 반
		*/
    private boolean availableTo(Student student) {
        return student.isEnabledForPrivateClass(this);
    }

}
```

<aside>
💡 addReview() 혹은 addStudent() 보면 알겠지만, 특정 상태에서 따라 다른게
행동해야 하는 굉장히 복잡한 상황에 있다..

→ State Pattern으로 해결 가능

</aside>

<br>
 

### After

---

![Untitled1](https://user-images.githubusercontent.com/91618389/197791711-d07fb7ac-877c-4437-87e9-ddee33ded2f9.png)

**Student** 

```java
public class Student {

    private String name;

    public Student(String name) {
        this.name = name;
    }

    private Set<OnlineCourse> onlineCourses = new HashSet<>();

    public boolean isAvailable(OnlineCourse onlineCourse) {
        return onlineCourses.contains(onlineCourse);
    }

    public void addPrivate(OnlineCourse onlineCourse) {
        this.onlineCourses.add(onlineCourse);
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

<aside>
✅ addPrivate()으로 개인 수강 가능한 목록에 강의를 추가한다. (마치 강의를 결제한 경우)
onlineCourses 에 넣어주고, isAvailable()를 통해서 실제 수강 가능한 목록에 존재하는지
스스로 확인할 수 있다.

</aside>

<br>
 
**State** 

```java
public interface State {

    void addReview(String review, Student student);

    void addStudent(Student student);
}
```

<aside>
✅ Context (OnlineCourse)에서 상태에 따라 많은 행동이 달라져야 하는 특정 행동을
State Interface로 선언 한다.

State Interface를 구현한 구체적인 클래스가 Context 상태에 따라서 다르게 행동하는것을
정의하기 위해서

</aside>
<br>
 
**OnlineCourse** 

```java
public class OnlineCourse {

    private State state = new Draft(this);

    private List<Student> students = new ArrayList<>();

    private List<String> reviews = new ArrayList<>();

    public void addStudent(Student student) {
        this.state.addStudent(student);
    }

    public void addReview(String review, Student student) {
        this.state.addReview(review, student);
    }

    public void changeState(State state) {
        this.state = state;
    }
}
```

<aside>
✅ Context의 State에 따라서 달라지는 행동은 모두 State Interface 구현체에게 
그 행동을 **위임** 한다. 그래서 addStudent(), addReview() 모두 내부적으로 
**State에 따른 행동을 수행하도록 한다.**

또한, 자신의 Context (Online Course)의 상태를 알기 위해 State Interface를 바라보게 한다.

다만 자신의 Context의 State를 변경하기 위해서 특정 **changeState**는 필요하다

</aside>
<br>
 
<aside>
✅ 추가적으로 Online Course라는 Context 역할을 하는 객체는 
**우리가 정의한 State 중에서 기본적으로 특정 State를 Default로 바라보고 있어야 한다.**

</aside>
<br>
 
**Client** 

```java
public class Client {

    public static void main(String[] args) {
        OnlineCourse onlineCourse = new OnlineCourse();
        Student student = new Student("whiteship");
        Student keesun = new Student("keesun");
        keesun.addPrivate(onlineCourse);

        onlineCourse.addStudent(student);

        onlineCourse.changeState(new Private(onlineCourse));

        onlineCourse.addReview("hello", student);

        onlineCourse.addStudent(keesun);

        System.out.println(onlineCourse.getState());
        System.out.println(onlineCourse.getReviews());
        System.out.println(onlineCourse.getStudents());
    }
}
```
<br>
 
**Draft State**

```java
public class Draft implements State {

    private OnlineCourse onlineCourse;

    public Draft(OnlineCourse onlineCourse) {
        this.onlineCourse = onlineCourse;
    }

    @Override
    public void addReview(String review, Student student) {
        throw new UnsupportedOperationException("드래프트 상태에서는 리뷰를 남길 수 없습니다.");
    }

    @Override
    public void addStudent(Student student) {
        this.onlineCourse.getStudents().add(student);
        if (this.onlineCourse.getStudents().size() > 1) {
            this.onlineCourse.changeState(new Private(this.onlineCourse));
        }
    }
}
```
<br>
 
<aside>
✅ State Interface를 구체적으로 구현한 Class 같은 경우는 
자신 Class의 상태에 따라서 다르게 행동한 결과를 Online Class에 가지고 있는 Field에 데이터를 반영 해야하기 때문에

**Field로 Online Course (Context)를 반드시 바라보아야 한다. (이번 예제 경우)**

그리고 자신(State)의 행동에 의해서 Context의 상태가 바뀌어야 할 필요가 있을 수 있음으로 Context가 가지고 있는 State 상태를 변경할 수 있는 Method를 적시적소에 호출함으로써
Context의 State를 변경해야 한다.

</aside>
<br>
 
**Private State**

```java
public class Private implements State {

    private OnlineCourse onlineCourse;

    public Private(OnlineCourse onlineCourse) {
        this.onlineCourse = onlineCourse;
    }

    @Override
    public void addReview(String review, Student student) {
        if (this.onlineCourse.getStudents().contains(student)) {
            this.onlineCourse.getReviews().add(review);
        } else {
            throw new UnsupportedOperationException("프라이빗 코스를 수강하는 학생만 리뷰를 남길 수 있습니다.");
        }
    }

    @Override
    public void addStudent(Student student) {
        if (student.isAvailable(this.onlineCourse)) {
            this.onlineCourse.getStudents().add(student);
        } else {
            throw new UnsupportedOperationException("프라이빛 코스를 수강할 수 없습니다.");
        }
    }
}
```
<br>
 
**Published State**

```java
public class Published implements State {

    private OnlineCourse onlineCourse;

    public Published(OnlineCourse onlineCourse) {
        this.onlineCourse = onlineCourse;
    }

    @Override
    public void addReview(String review, Student student) {
        this.onlineCourse.getReviews().add(review);
    }

    @Override
    public void addStudent(Student student) {
        this.onlineCourse.getStudents().add(student);
    }
}
```

<br>
 

### 장단점

---

- 장점
    - 상태에 따른 동작을 개별 클래스(ConcreteState)로 옮겨서 관리할 수 있다.
    - 기존의 특정 상태에 따른 동작을 변경하지 않고 새로운 상태에 다른 동작을 추가할 수 있다.
    - 코드 복잡도를 줄일 수 있다.
    
     
    
- 단점
    - 전체적인 클래스 복잡도가 증가한다
