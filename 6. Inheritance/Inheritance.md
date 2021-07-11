# Inheritance

## 1. 상속

+ 대표적인 OOP의 특징으로, 어떤 Class B가 Class A의 멤버 변수와 메서드를 그대로 받을 때 'B가 A를 상속받는다'라고 한다.
  + A와 B의 관계를 부모 - 자식 또는 상위 - 하위 관계라고 한다.

+ 하나의 부모는 여러 자식을 가질 수 있고, 부모는 또 다른 자식이 될 수도 있다.
+ 다른 Class의 상속을 받지 않는 Class는 기본적으로 java.lang.Object Class가 그의 부모가 된다.

### 1.1 extends

+ Java에서 실질적인 상속 기능은 `extends`를 통해서 이루어진다.

  + ```java
    public class B extends A{
        ...
    }
    ```

+ Java에서는 단일 상속만 가능하다. 즉, 오직 한 개의 Class만 extends할 수 있다. 다중 상속 대신 Java는 Interface를 활용한다.

<br/>

<br/>

### 1.2 예시

+ Phone.java

```java
package com.inheritance;

public class Phone {
	private String name;
	private char color;
	private int price;
    
    public Phone(String name, char color, int price) {
        this.name = name;
        this.color = color;
        this.price = price;
    }
	
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public char getColor() {
		return color;
	}
	public void setColor(char color) {
		this.color = color;
	}
	public int getPrice() {
		return price;
	}
	public void setPrice(int price) {
		this.price = price;
	}
	@Override
	public String toString() {
		return "Phone [name=" + name + ", color=" + color + ", price=" + price + "]";
	}
	
}
```

+ FolderblePhone.java

```java
package com.inheritance;

public class FolderblePhone extends Phone{
	
}
```

+ InheritanceTest.java

```java
package com.inheritance;

public class InheritanceTest {
	public static void main(String[] args) {
		Phone phone = new Phone();
		phone.setName("Note");
		phone.setColor('B');
		phone.setPrice(10000);
		
		System.out.println(phone); // Phone [name=Note, color=B, price=10000]
		
		FolderblePhone fPhone = new FolderblePhone();
		fPhone.setName("Folder2");
		fPhone.setColor('A');
		fPhone.setPrice(20000);
		
		System.out.println(fPhone); // Phone [name=Folder2, color=A, price=20000]
	}
}
```

<br/>

<br/>

## 2. 하위 Class

### 2.1 하위 Class 자신의 멤버 변수, 메서드

+ 하위 Class는 상속을 받는 동시에 자기 자신만의 멤버 변수와 메서드를 가질 수 있다.
+ 위 코드에서 FolderblePhone 클래스를 수정해보자.

```java
package com.inheritance;

public class FolderblePhone extends Phone{
    // 클래스 고유의 멤버 변수
	private int width;
	private int height;
	
    // 클래스 고유의 메서드
	public int getScreen() {
		return this.width * this.height;
	}
	
	public int getWidth() {
		return width;
	}
	public void setWidth(int width) {
		this.width = width;
	}
	public int getHeight() {
		return height;
	}
	public void setHeight(int height) {
		this.height = height;
	}
}
```

+ FolderblePhone 클래스의 객체에서 해당 멤버 변수와 메서드를 사용할 수 있다.

```java
public static void main(String[] args) {
    FolderblePhone fPhone = new FolderblePhone();
    
    fPhone.setWidth(500);
    fPhone.setHeight(1000);
    
    System.out.println(fPhone.getScreen()); // 500000
    System.out.println(fPhone); // Phone [name=null, color=, price=0] -> 객체의 기본값은 null, char의 기본값은 \u0000(널 문자), int의 기본값은 0
}
```

<br/>

<br/>

### 2.2 (default) vs protected

+ 하위 Class에서 상위 Class를 바라볼 때, 상위 Class의 멤버 변수 혹은 메서드의 Modifier가 protected나 public인 경우에만 바라볼 수 있다고 했었다. 이런 상황에 대한 예시는 다음과 같다.

```java
package com.inheritance;

public class FolderblePhone extends Phone{
	private int width;
	private int height;
	
	...
        
    public void showInfo() {
        String info = this.name + " " + this.color + " " + this.price + " " + this.width;
        System.out.println(info);
    }
}
```

+ 위의 `showInfo()`에서 나는 현재 객체의 name, color, price 필드에 대한 정보를 출력해주고 싶다. 하지만, 이 메서드에서 `this.name`, `this.color`, `this.price`는 모두 `not visible`의 상태이다. 상위 Class인 Phone Class에서 name, color, price의 modifier가 기본(default)이거나 private이기 때문이다.

  + 단, 기본(default)의 경우에는 같은 패키지 내에 상위 Class와 하위 Class가 같이 있을 경우 접근이 가능하다.

+ 이를 해결하는 방법은 두 가지가 있다.

  + 상위 Class의 변수의 modifier를 protected로 바꾼다.(pulic은 캡슐화를 위해서 사용하지 않음)

    + ```java
      // Phone.java
      public class Phone {
      	protected String name;
      	protected char color;
      	protected int price;
          
          ...
      ```

  + 상위 Class의 public 메서드(Setters & Getters)를 통해서 private 멤버 변수에 접근한다.

    + ```java
      // FolderblePhone.java
      public void showInfo() {
          String info = this.getName() + " " + this.getColor() + " " + this.getPrice() + " " + this.getWidth();
          System.out.println(info);
      }
      ```

+ 하지만, 이는 아직 완벽히 해결되지 않은 것이다!

<br/>

<br/>

### 2.3 this vs super

+ 그렇다면, 위의 코드에서 protected로 바꾸고 `this.name`과 같이 가져오는 것이 맞는 방법일까?
+ 객체 간의 관계에서 `this`는 자기 자신을 의미하고 `super`는 부모 객체를 의미한다. 만약, 부모 객체에 중복된 멤버 변수가 없다면 `this`를 이용해서 `super`에 접근할 수 있다. 하지만 이는 당연히 권장되지 않는 방법이다. 모호한 코딩은 피하자.
+ 따라서, **상위 Class의 멤버 변수를 가리키고 싶은 것이라면 `super.name`을 쓰는 것이 맞다.** FolderblePhone Class에 name이라는 이름을 가진 멤버 변수가 생긴다면 위 코드의 `this.name`은 더 이상 Phone Class에 있는 name을 가리키지 않게 된다.

```java
public void showInfo() {
    String info = super.name + " " + super.color + " " + super.price + " " + this.width;
    System.out.println(info);
}
```

+ 당연히, 메서드의 경우도 똑같이 적용된다.

```java
public void showInfo() {
    String info = super.getName() + " " + super.getColor() + " " + super.getPrice() + " " + this.getWidth();
    System.out.println(info);
}
```

#### 2.3.1 super를 활용한 생성자 만들기

+ 매번 Setters를 사용해서 멤버 변수의 값을 지정해주는 것은 비효율적인 일이다. 따라서, 생성자를 통해서 클래스를 생성할 때 넘겨준 인자값으로 클래스의 멤버 변수 값을 초기화시켜주는 것이 좋은 방법일 것이다.
+ 이때, 우리가 배운 super를 활용하여 상속되는 상위 Class의 멤버 변수를 초기화해줘 보자.
  + 아래 코드는 상위 Class의 멤버 변수들이 protected일 때다. private이라면 Setters를 사용한다.

```java
public ForderblePhone(String name, char color, int price, int width, int height) {
    super.name = name;
    super.color = color;
    super.price = price;
    this.width = width;
    this.height = height;
}
```

+ 만약 상위 Class에 생성자가 만들어져 있다면, 상위 Class의 생성자를 이용할 수도 있다. `super()`를 사용한다.
  + 이 경우, 멤버 변수들이 private이어도 Setters없이 초기화가 가능하다.
  + 이렇게 상위 Class의 생성자를 호출할 때는 **항상 첫 번째 라인**에 와야 한다.

```java
public ForderblePhone(String name, char color, int price, int width, int height) {
 	super(name, color, price); // 첫 번째 라인이 아니면 오류 발생
    this.width = width;
    this.height = height;
}
```

+ 주의) **하위 Class의 기본 생성자는 상위 Class의 기본 생성자를 자동으로 호출한다.** 즉, 상위 Class에서 인자를 받는 생성자를 만들어 기본 생성자를 컴파일러가 직접 만들어주지 않으면서 별도의 기본 생성자를 지정해주지 않은 경우, 하위 Class에서도 기본 생성자를 사용할 수 없다.
