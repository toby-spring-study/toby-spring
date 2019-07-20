# 2장 슈퍼타입 토큰

1. 제네릭이란? = 타입정보를 넘겨 동적으로 타입을 정의 및 대체하는것을 의미.
    - 컴파일 시점에는 제네릭으로 지정한 클래스타입을 모름
    - 제네릭 정보는 컴파일시 다 지워진다. 
    - 제네릭 정보를 컴파일 시점에 사용하고 싶다면 body를 넣어 상속한 것처럼 만들것. -> 오버라이딩 할게 있으면 body에 구현하면됨.
    - 혹은 진짜로 상속 받은 클래스를 사용한다.
    

2. local class = 메소드 안에 클래스를 정의하여 메소드에서만으로 scope를 지정.


3. type token 이란? = 간단히 말해서 타입을 클래스
    - 예시 
    ```java
    public static <T> T typeToken(T value, Class<T> clazz) {
        return clazz.cast(value);
    }
    ```

4. super type token이란? = token type을 자식 클래스로 만들어 제네릭의 정보를 컴파일시에도 가져올수 있도록 하는 클래스 혹은 기법.
    - 이와 같은 경우는 json 파싱할 때 많이 사용 된다.(= restTemplate 를 사용할때도 역시 결국 body의 object를 파싱하려고 사용하는 케이스)
    - 내 개인 프로젝트에서 사용한 예시(여기선 gson의 TypeToken 사용.)
    ```java
    private final Type listType = new TypeToken<ArrayList<LectureDay>>() {}.getType();
    ```


5. ParameterizedTypeReference란? = super type token은 자바에서 제공하는 해법이다. 하지만 코드가 난잡해지고 실수할수 있는 관리 point가 증가하여
spring 에서는 간편하게 ParameterizedTypeReference 클래스를 제공한다.
    - 아래는 사용 예시이다.
```java
RestTemplate restTemplate = new RestTemplate();
restTemplate.exchange("http://localhost:8080",  HttpMethod.GET,
    null, new ParameterizedTypeReference<List<Account>>() {});
```