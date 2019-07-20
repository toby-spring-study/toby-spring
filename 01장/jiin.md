## 토비의 봄Tv 1회 - 재사용성과 다이나믹 디스패치, 더블 디스패치

---
### 스프링이란? 
Dependency + Injection + Framework

---

Dependency? <br>
= Client가 Supplier에 의존하는 관계
 - 의존 관계(dependency relationship)<br>
    Supplier의 변화가 Client에 영향을 주는 경우
- 의존 관계 발생
  - Supplier가 Client의 필드
  - Supplier가 Client메소드의 파라미터
  - Supplie가 Client 로컬 변수
  - Supplier로 메시지를 보냄   
- Client는 재사용(객체지향)이 어렵다.<br>
  (컴포넌트/서비스가 어렵다)<br>
  Supplier가 바뀌면 Client도 바뀌기 때문

- 오브젝트 패턴<br>
  = 런타임시 관계가 바뀔 수 있다 (동적으로!!)
- 클래스 패턴<br>
  = 상속을 기본으로 하고 있다

스프링에서 말하는 Dependency는 컴파일이 아닌 런타임시 결정되는 오브젝트 의존 관계(동적)!!
1. 구현 대신 언터페이스 사용
    - 클래스(구현) 의존 관계 제거
    - 클래스에 대한 이존성은 생성 패턴처럼 3자에게 위임
2. 오브젝트 합성(composition) 사용
    - 재사용성을 확보하기 위한 방법의 한 가지(상속의 대안)
    - 인터페이스 사용이 전제(블랙박스 재사용)
    - 각 클래스가 캡슐화되고 자신의 역할에 충실하게 도와줌
(Client 재사용)

---

IoC
- Class -> Class 는 재사용이 떨어지니
- Class -> Interface<br>
    프로그래밍을 인터페이스를 통해서 해라

- Object Pattern 사용
    
    Class ->   Interface <br>
    (Framework)          |
                (Class, Class)

이 위에 것을 Dynamic Method Dispatch이라 부름

Dispatch에는 Static과 Dynamic 두 가지가 있다.<br>
Static > 런타임 시점 전(컴파일 시점)에 어느 메소드로 호출될 지 알고 있다.<br>
Dynamic > 런타임 시점에서 할당된 오브젝트에 따라서 결정됨.<br>
(receiver parameter)

---
Method Signature vs Method Type<br>
Method Signature (name, parameter types)<br>
이 두 가지가 같으면 override를 할 수 있음<br>
Method Type (return type, method type parameter, method argument types, exception) -> Method Reference<br>
.....??

---

Double Dispatch<br>
= Dynamic Dispatch를 두 번!

ex) 
```java
public class Dispatch{
    interface Post {void postOn(SNS sns);}
    static class Text implents Post{
        public void postOn(SNS sns){
            sns.post(this);
        }
    }
    static class Picture implents Post{
        public void postOn(SNS sns){
            sns.post(this);
        }
    }

    interface SNS {
        void post(Text post);
        void post(Picture post);
    }
    static class Facebook implents SNS{
        public void post(Text post) {System.out.println("text-facebook");}
        public void post(Picture post) {System.out.println("picture-facebook");}
    };
    static class Twitter implents SNS{
        public void post(Text post) {System.out.println("text-Twitter");}
        public void post(Picture post) {System.out.println("picture-Twitter");}
    };

    public static void main(String[] args){
        List<Post> posts = Arrays.asList(new Text(), new Picutre());
        List<SNS> sns = Arrays.asList(new Facebook(), new Twitter());

        posts.forEach(p -> sns.forEach((SNS s) -> p.postOn(s)));
    } 
}
```
###### (class안에 static class를 하면 새로운 단독의 class...라고 할까나)

Dynamic Dispatch<br>
파라미터는 Dynamic Dispatch조건이 되지 않음<br>
컴파일 시점에 결정이 되어 있지 않으면 컴파일 되지 않음


Visitor Pattern의 원조<br><br><br><br><br><br><br><br>

---
Toby

Dynabic Method Dispatch, Double Dispatch, Visitor Pattern, Visitor Proxy Pattern(Hibernate)

---
Jiin Shin

이펙티브자바,
supplier,
function,
consumer,
리플렉션,
프록시

