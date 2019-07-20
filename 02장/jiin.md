## 토비의 봄Tv 2회 - 슈퍼 타입 토큰
---
#### 스프링에서 사용되는 ParameterizedTypeReference의 작동 원리인 Super Type Token의 동작 원리와 활용법을 알아봅니다.



## * Super Type Token?  Type Token?

## Type Token
 - 타입(Generic)에 따라서 "컴파일시" 동적으로 변경
 특정 타입들만 변경이 필요할 때 사용(다형성 중 한 가지 방식) 

 ex) 
```java
public class TypeToken{
    static class Generic<T>{
        T values;
        void set(T t){}
        T get() {return null;}
    }
    public static void main(String[] args) throws Exception{
        Generic<String> s = new Generic<String>();
        s.value = "String";
    
        Generic<Integer> i = new Generic<Integer>();
        i.value = 1;
        i.set(10);
    }
}
```

- MAP을 사용한 TypeToken
안전하지만 제한적,
특정 타입의 Map 요소 하나씩
(강제 casting이 없음)

ex) 
```java
public class TypeToken{
    static class TypesafeMap{
        Map<Class<?>, Object> map = new HashMap<>();

        <T> void put(Class<T> clazz, T value){
            map.put(clazz, value);
        }

        <T> T get(Class<T> clazz){
            return (T)map.get(clazz); >> 이 방식은 typesafe하지 않음
            return clazz.cast(map.get(clazz)); >> 논리적이긴 하지만 typesafe함
        }
    }

    public static void main(String[] args) throws Exception{
        TypesafeMap m = new TypesafeMap();
        
        m.put(Integer.class, 1);
        m.put(String.class, "String");
        m.put(List.class, Arrays.asList(1,2,3));

        System.out.println(m.get(Integer.class));
        System.out.println(m.get(String.class));
        System.out.println(m.get(List.class));
    }
}
```
- 한계
 map은 key가 같으면 덮어씌워짐
 ex) 
 
```java
    {
        m.put(List.class, Arrays.asList(1,2,3));
        m.put(List.class, Arrays.asList("a","b","c"));
    }
        return >> [a, b, c]

```

그렇다고 
m.put(List<Integer>.class), Arrays.asList(1,2,3));
m.put(List<String>.class, Arrays.asList("a","b","c"));
이런식으로 타입 파라미터를 정의해서 활용하려고 해도 가져오지를 못 한다.
Generic Type 정보까지 있는 Type Token은 사용할 수 없다
그래서 ... Super Type Token !

### type token 정의 링크  >>  gafter.blogspot.kr/2006/12/super-type-tokens.html
--- 
##### 깊게.. 
##### (20:26) Type Erasure / Generic으로 선언된 클래스를 ParameterizedType...? / .value라는 type은 런타임시 String타입이 아니다. 그런데 에러가 나지 않는 이유는? / 컴파일러가 미리 check하고 실제 코드에 casting 해준다 / Object는 최상위 class니까 가능 ...

---
(42:50)<br>
Generic으로 선언된 것은 Type Erasure을 통해서 몽땅 지워진다고 생각하지만 방법이 있음

```java
public class SuperTypeToken{
    static class Sup<T>{
        T values;
    }

    static class Sub extends Sup<List<String>>{

    }

    public static void main(String[] args) torws NoSuchFieldException{
        SUb b = new Sub();
        Type t= b.getClass().getGenericSuperclass();
        ParameterizedType pType = (ParameterizedType) t;
        System.out.println(ptype.getActualTypeArguments()[0]);
    }
}
```
return >> java.util.List<java.lang.String>

리플렉션을 통해서 런타임시 접근할 수 있도록 bytecode에 남아있음(런타임시의 정보를 가져옴)

---

## Super Type Token

TypeReference라는 class를 통해
Generic을 typeparameter가 있는 체로 상속한 
class 정보를 넘겨서
Generic 정보를 full 로 받는 .... Super Type Token

Spring에서는 구현되어있음
ParameterizedTypeReference

```java
public class SpringTypeReference{
    public static void main(String[] args){
        ParameterizedTypeReference<?> typeRef = new ParameterizedTypeReference<List<Map<Set<Integer>, String>>>() {};
        // 여기서 {}를 안 쓰면 에러가 난다(body를 꼭 줘야함)
        // {} = 익명 클래스 인스턴스를 만들어서 익명 클래스가 상속하고 있는 슈퍼 클래스의 Generic type parameter 정보를 전달하기 위한 용도
        System.out.println(typeRef.getType();
    }
}
```
 return >> java.util.List<java.util.Map<java.util.Set<java.lang.Integer>, java.lang.String>>

- 사용하는 이유 및 용도


```java
```