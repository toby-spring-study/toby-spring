# 1장 재사용성과 다이나믹 디스패치, 더블 디스패치

1. 스프링이란 Dependency + injection + framework이다.
    - dependency란? = 컴파일 타임이 아닌 런타임시에 결정/구성되는 오브젝트간의 의존관계
    - dependency가 걸려있다면 재사용성이 어렵다.
        - dependency를 interface로 걸어야 재사용성이 나중에 확장성을 고려했을때 좋다.

2. 스태틱 디스패치란? = 런타임이 아닌 컴파일 시점에도 메소드가 실행될지 아는 경우.
    - 오버로딩이 스태틱 디스패치의 한종류.

3. 다이나믹 디스패치란? = 런타임시점에 어떤 메소드를 실행할지 결정되는 것.
    - List list = new ArrayList<>(); 같이 collection 클래스가 대표적이다.
    - receiver parameter(=class의 this)를 가지고 어떤 object의 메소드를 실행시킬지 결정.

4. Method Signature(=name, parameter types)
    - Method Signature가 같은 메소드는 한 클래스내에 정의할수 없다.
    - return type은 Method Signature가 아니다.

5. Method type(= return type, method type parameter, method argument types, exception)
    - Method type이 일치하면 Method Reference가 사용가능하다.

6. 더블 디스패치란? = 다이나믹 디스패치가 이중으로 들어간 경우
    - 이 경우 파라미터로 다이나믹 디스패치 object를 사용하면, 컴파일시 타입 명세가 맞지 않아 error 표시.
    - 파라미터가 아닌 object 자체의 다이나믹 디스패치 메소드를 사용하도록 변경하여 해결. => 객체간 주객전도를 시키는 디자인 패턴을 활용.
    - 이런 패턴을 적용하는 경우는 드물음(사실상 2차원적인 다이나믹 디스패치를 적용하기에 고려하는 자체가 비용이 크게 들어서 인듯... 객체지향을 정말 잘한다면 당연히 적용하는게 좋을듯!!! <-> 하지만 코드의 재파악시 어려움과 코드를 명시적으로만 봐서 알기가 어렵다는 점이 있어 1차원적으로 개발을 하는것도 나쁘다고는 할수 없는것 같다.)

7. 더블 디스패치 예시 코드
```java
public class Dispatch {

    interface Post {
        void postOn(Sns sns);
    }

    static class Text implements Post {
        @Override public void postOn(Sns sns) { sns.post(this); }}

    static class Picture implements Post {
        @Override public void postOn(Sns sns) { sns.post(this);}}

    interface Sns {
        void post(Text text);
        void post(Picture picture);
    }

    static class Facebook implements Sns {
        @Override
        public void post(Text text) { System.out.println("facebook text post"); }
        @Override
        public void post(Picture picture) { System.out.println("facebook picture post");}
    }

    static class Twitter implements Sns {
        @Override
        public void post(Text text) { System.out.println("twitter text post");}
        @Override
        public void post(Picture picture) {System.out.println("twitter picture post"); }
    }

    public static void main(String[] args) {
        List<Post> postList = Arrays.asList(new Text(), new Picture());
        List<Sns> snsList = Arrays.asList(new Facebook(), new Twitter());

        postList.forEach(p -> snsList.forEach(s -> p.postOn(s)));
    }
}
```