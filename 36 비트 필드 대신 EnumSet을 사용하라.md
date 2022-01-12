## ITEM 36. 비트 필드 대신 EnumSet을 사용하라

열거 값들이 집합인 경우, 서로 다른 2의 거듭제곱 값을 할당한 **정수 열거 패턴** 사용.

이러한 필드를 **비트 필드**라 한다.

```java
private class Text {
  public static final int STYLE_BOLD              = 1 << 0; // 1
  public static final int STYLE_ITALIC            = 1 << 1; // 2
  public static final int STYLE_UNDERLINE         = 1 << 2; // 4
  public static final int STYLE_STRIKETHROUGH     = 1 << 3; // 8
  
  public void applyStyles(int styles) { ... }
}
```



`text.applyStyles(STYLE_BOLD | STYLE_ITALIC);`

와 같이 비트 연산자를 통하여 비트 필드는 집합 연산을 빠르게 처리하지만 단점이 있다.



**비트 필드의 단점**

1. 비트 필드 값이 그대로 출력되면 단순한 정수 열거 상수를 출력할 때보다 해석하기가 훨씬 어렵다.
2. 비트 필드 하나에 포함된 모든 원소를 순회하기 까다롭다.
3. 최대 몇 비트가 필요한지를 API 작성 시 미리 예측하여 타입을 선택해야한다. (long or int)



#### EnumSet 클래스

<img src="/img/item36_EnumSet_Abstract_Structure.png" style="zoom:33%;" />

EnumSet은 추상 클래스로 생성자 대신 인스턴스 생성을 위한 정적 팩터리 메소드가 정의되어 있다.



<img src="/img/item36_EnumSet_noneOf.png" style="zoom:50%;" />

**RegularEnumSet**, **JumboEnumSet** 2가지의 EnumSet 구현체를 제공

RegularEnumSet는 단일 long 자료형을 사용하고, JumboEnumSet은 long 배열을 사용한다. 

long 은 64 비트이기 때문에 EnumSet 원소의 수에 따른 구현체를 선택하게 된다.



EnumSet은 비트 필드의 단점을 커버할뿐만 아니라 내부 메소드들이 비트 연산으로 이루어져있어 연산이 매우 빠르다는 장점이 있다.



------

비트 필드 대신 **EnumSet** 를 사용

```java
public class Text {
  public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }
  
  // 어떤 Set을 넘겨도 되나, EnumSet이 가장 좋다.
  // 그래도 EnumSet<Style>이 아닌 인터페이스 Set<>으로 받는 것이 좋은 습관이다.
  public void applyStyles(Set<Style> styles) { ... }
}
```

`text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));`
