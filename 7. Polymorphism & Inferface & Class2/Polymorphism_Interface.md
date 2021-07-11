# Polymorphism, Interface

## 1. toString()

+ `System.out.println( argument )`는 전달되는 argument.toString()을 호출하고, 그 결과를 출력한다. 하지만, 일반적인 객체를 넣고 객체.toString()을 해보면 이상한 값(사실은 주소)이 출력된다.

+ 지난 Class의 내용에서, 내가 원하는 결과물이 출력되게 하기 위해서는 Class 내에서 toString이라는 이름의 메서드를 재정의하여 원하는 값을 return하게 하면 됐었다.

```java
// Phone.java
public String toString() {
    return this.getName() + " " + this.getColor() + " " + this.getPrice();
}
```

+ 그렇다면 하위 Class인 ForderblePhone Class에서는 이것을 상속받기 때문에 그대로 나올까?
  + -> 아니다. 왜? ForderblePhone Class의 객체를 출력해보면 name, color, price까지는 잘 나온다. 하지만 우리가 원하는 것은 폴더블폰이 가지고 있는 추가 멤버 변수, width와 height까지도 출력되는 것이다.
  + 따라서, ForderblePhone Class 내에도 toString메서드를 재정의 해 본인 Class에 맞도록 만들어 줄 필요가 있다.

```java
// ForderblePhone.java
public String toString() {
    return super.getName() + " " + super.getColor() + " " + super.getPrice() + " " + this.getWidth() + " " + this.getHeight(); // 꼭 Getters로 안가져와도 괜찮다.
}
```

<br/>

<br/>

<br/>

## 2. 다형성 - methods

+ 이처럼 **상위 Class와 하위 Class가 같은 메서드를 가지고 서로 다른 일을 하는 것**(이전에 생성자를 인자의 개수에 따라 여러 개로 만들었던 것도)이 다형성의 모습이다.
+ **하나의 이름으로 여러 개의 형태를 구성할 수 있는 OOP의 특징을 다형성**이라고 한다. **타입**과 **메서드**로 크게 나누어서 생각할 수 있는데, 위의 예는 메서드의 overriding이다.
+ 메서드는 **overloading**과 **overriding**의 2가지로 다형성을 구현한다.

| 구분        | 설명                                                         |
| ----------- | ------------------------------------------------------------ |
| overloading | name이 같아도, parameter가 다르면 별개의 method로 간주한다. 생성자도 동일하다. |
| overriding  | 상속 관계에서 상위 Class의 method를 하위 Class에서 재정의 할 수 있다. |

```java
// overloading
public void Print(String s) {
    System.out.println(s)
}
public void Print(String s, int n) {
    System.out.println(s + n)
}

// overriding
// 상위 클래스 
public void Print(String s) {
    System.out.println(s)
}
// 하위 클래스
public void Print(String s) {
    System.out.println(s + "입니다.")
}
```

### 2.1 @Override Annotation

+ 메서드를 재정의 할 때 `@Override` Annotation을 사용하는 것을 권장한다. 이는 상위 Class의 특정 메서드를 재정의 한다고 컴파일러에게 명시하는 것이다. 만약 상위 Class에 해당 메서드가 없는 경우 컴파일 에러가 발생한다.
+ 왜 쓰는 것이 좋을까?
  + 재정의를 했다는 것에는 '재정의'를 한 목적이 분명히 있을 것이다. 하지만, `@Override`가 없다면 상위 Class의 재정의 대상이 된 메서드가 삭제되었을 때 하위 Class의 재정의 메서드는 그저 하나의 단독 메서드가 되어버리며 아무런 에러가 나지 않게 된다. 이 경우 프로그래밍 과정에서 의도하지 않았던 결과가 발생할 수 있으며 그 이유를 찾기가 어렵기 때문에 `@Override` Annotation을 쓰는 것을 권장하는 것이다.

```java
@Override
public void Print(String s) {
    System.out.println(s + "입니다.")
}
```

<br/>

<br/>

<br/>

## 3. 다형성 - type

+ 상위 type으로 하위 type의 객체를 reference 할 수 있다.
+ 즉, 한 개의 type으로 여러 하위 type의 객체를 할당 받을 수 있다.

```java
// ForderblePhone Class는 Phone Class로부터 상속을 받는 Class다.
Phone phone = new ForderblePhone();
```

+ 자신보다 하위에 있는 모든 type의 객체를 할당 할 수 있다.

+ 여태까지의 코드를 예로 할 때, `phone.toString()`의 결과는 무엇일까?(Phone의 toString 메서드가 실행될까, ForderblePhone의 overriding된 toString 메서드가 실행될까)

  + ```java
    Phone polyPhone = new FolderblePhone("polyPhone", 'r', 40000, 100, 150);
    System.out.println(polyPhone); // polyPhone r 40000 100 150
    ```

  + -> ForderblePhone의 toString에 정의된 형태로 값이 반환된다.

+ 그렇다면, type을 통한 다형성을 구현한 경우 하위 type의 모든 멤버 변수나 메서드에도 접근이 가능한 것일까?

  + ```java
    polyPhone.width; // can not be resolved
    polyPhone.showInfo(); // undefined
    ```

  + ForderblePhone Class에는 Phone Class에는 없는 width, height라는 멤버 변수와 showInfo라는 메서드가 있다. 이를 각각 `phone.`을 통해서 찍어보면 찾을 수 없다는 에러가 나온다. 왜일까?

+ 그 이유는, type을 통한 다형성을 구현한 경우 **상위 Class에 정의된(상위 Class가 상속 받은 것을 포함) 이름만 사용할 수 있으며, 이 이름들이 하위 Class에서 overriding된 경우 하위 Class의 것을 따르기 때문**이다.

  + 조심해야 할 것은, A -> B -> C -> D(a -> b라면 b가 a를 상속받은 것)의 관계가 있다고 했을 때, A의 toString 메서드를 C에서 overriding하고 `B x = new D();`와 같이 객체를 생성한다면 `D.toString()`의 결과는 C의 toString() 반환값이 된다는 것이다. D에서 직접적으로 overriding하지 않아도, 상위 Class(C)로부터 overriding된 것을 상속받기 때문에 이 구조를 잘 파악하고 사용해야 할 것이다.
  + 또, **overloading을 overriding과 명백하게 구분**해야 한다. 상위 Class의 메서드를 overloading한 하위 Class의 메서드를 호출할 경우 이는 다른 함수인 것이며, 당연히 에러가 발생하게 된다.

<br/>

<br/>

<br/>

## 4. Interface - 추상화의 꽃

+ 단일 상속(extends)을 선택한 Java는, 메서드의 추상화를 Class와 독립적인 개념으로 Interface라고 이름 짓고, 한 Class가 여러개의 interface를 구현(**implements**)할 수 있도록 했다.

+ interface에는 관련된 메서드들을 기술하는데, 선언부만 기술하고 구현부는 없다.
  + implement해 온 후, 구현부는 내가 직접 작성하게 된다.
+ 상속이 본질적으로 '재사용'이라면, 인터페이스는 본질적으로 '규약, 약속'이다.
  + Class적으로 단일 상속을 하고, 기능적인 부분으로 원하는만큼 interface로부터 implement해 온다.

+ Eclipse의 Package Explorer 상에서 우클릭 -> New -> Interface로 생성한다.

```java
package com.inheritance;

public interface Folder {
	public void fold();
	public void open();
}
```

### 4.1 implement

```java
public class FolderblePhone extends Phone implements Folder { // 여러개를 하고 싶다면 ,로 연결
    ...
}
```

+ 클래스명 부분에 빨간 밑줄이 그어질 것이다. 이는, Folder interface 안에 있는 두 메서드를 완성시키라는 것이다.
+ Interface를 구현하는 Class는 interface에 있는 모든 추상 메서드를 구현할 의무를 가진다.
+ 클래스명에 마우스를 오버시키면 나오는 팝업에서 'Add unimplemented methods'를 클릭하면 추상 메서드들이 override되어 삽입된다.
  + interface에 있어서도 같은 로직이 적용된다. interface에서도 상속(?)을 받는 것이기 때문에 overriding으로 재정의하는 것

<br/>

<br>

### 4.2 구현은 왜 Class에서?

+ 그렇다면, 왜 interface에서는 빈 메서드를 만들고 Class가 이를 implement해서 작성하도록 하는 것일까? 그럴 거라면 그냥 Class에서 메서드를 작성하면 되는 것이 아닐까?
+ 이때, **type을 통한 다형성**이 적극 사용된다. 
  + Interface Type으로 변수를 선언하고 `new`로 Class 객체를 생성한다고 생각해보자.
    + 애초에 interface는 자기 자신의 객체를 만들 수 없다. `Folder f = new Foler();` 불가
  + `Folder f = new FolderblePhone();`과 같이 객체가 생성되었을 때, type을 통한 다형성 때문에 f에서는 Folder 내에 정의된 fold와 open 메서드만을 사용할 수 있을 것이고 이 내용은 우리가 ForderblePhone Class에 정의한 내용이 될 것이다.
  + 그렇다면, Folder에서 선언한 메서드를 A Class에 구현해놓고 B Class에서 `Folder f = new A();`와 같이 f를 생성한다면, A를 상속 받을 필요 없이, A가 그것을 어떻게 구현했는지도 알 필요 없이, **필요한 메서드만**을 가져올 수 있게 될 것이다.



```java
// A Class
public class A implements Folder {
    ...
    public void fold() {
        System.out.println("fold");
    }
}
```

```java
// B Class
public class B {
    Folder f = new A();
    f.fold(); // fold 출력
}
```

+ OOP는 많은 Class들이 다양한 의존 관계를 통해 문제를 해결한다. B Class에서 A Class의 어떤 메서드의 수행만을 필요로 할 경우, B는 A의 다른 부분은 필요도 없으며 A에서 그 메서드가 어떻게 구성되어있는지도 관심이 없다. 마찬가지로, A 입장에서는 B가 필요로 하는 메서드에 대해서 수행만 해주면 될 뿐, 그 내용에 대한 것을 노출시키고 싶지 않을 것이다. 그래서 **Interface**(연결하는 경계면)라는 이름이 붙은 것이 아닐까.
  + .jar를 통해 필요한 기능들을 외부에서 가져올 때, API Document를 참고하여 기능을 사용하는데, 이 과정에서 사용하는 Type들이 대부분 interface로 선언되어 있다.

<br/>

<br/>

### 4.3 default method

+ Java 8 버전부터 추상 메서드가 아닌 body를 가진 default method가 interface에 추가되었다.
+ 이로 인해 본래의 interface의 의미가 조금 퇴색되었다는 의견이 많지만, 개발자의 편의는 증대되었다.
+ 추상 메서드가 아니라는 것을 밝히기 위해 modifer 앞에 `default`를 붙이고 구현부를 작성한다.
  + 즉, 구현부를 만들지 않아도 되는 interface 메서드다. 물론, 재정의가 가능하다.

```java
public interface Folder {
    public void fold();
    public void open();
    
    default public void start() {
        System.out.println("start!");
    }
}
```

```java
public static void main(String[] args) {
    Folder f = new FolderblePhone();
    
    folder.start(); // start!
}
```



