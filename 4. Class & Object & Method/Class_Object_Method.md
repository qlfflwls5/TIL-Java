# Class, Object, Method

## 1. OOP

+ OOP의 특징

| 특징                  | 설명                                                         |
| --------------------- | ------------------------------------------------------------ |
| 캡슐화(Encapsulation) | 하나의 클래스 안에 데이터와 기능을 담아 정의하여, 중요한 데이터나 복잡한 기능 등은 숨기고 외부에서 사용에 필요한 기능만을 공개하는 것 |
| 상속(Inheritance)     | 객체 정의 시 기존에 존재하는 객체의 속성과 기능을 상속받아 정의하는 것 |
| 다형성(Polymorphism)  | 같은 타입 또는 같은 기능의 호출로 다양한 효과를 가져오는 것<br/>하나의 객체가 여러 가지 타입을 가질 수 있는 것 |
| 추상화(Abstraction)   | 현실 세계에 존재하는 객체의 주요 특징을 추출하는 과정        |

<br/>

<br/>

<br/>

## 2. Class & Object

+ Object는 시스템의 대상이 되는 모든 것이다. 
  + 학생 관리 시스템이라면 학생, 교사, 출결, 성적 등이 된다.
  + Object는 구체적인 표현 대상이 있다.
    + A학생, B학생 등을 표현한다.
+ Class는 구체적인 Object들을 분석해 공통적인 내용들을 **추상화**하여 프로그래밍 언어로 표현한 것
  + Class를 만들기 전 생각해야 하는 것 2가지
    + 정적인 특성(attribute)
    + 동적인 특성(behavior)

### 2.1 Java에서 Class만들기

+ `public` -> Modifier

```java
public class Phone {
    // attributes (member variables)
    public String name;
    public char color;
    public int price;
    
    // behvior (methods)
    public String getNumber() {
        return "010-1234-5678";
    }
}
```

<br/>

<br/>

### 2.2 Class 사용하기

+ 객체 변수는 주소를 가지고 있다. 즉, stack에 객체의 주소에 대한 데이터가 있다. 그리고 heap 영역에 name이라는 변수가 있고 **이 변수는 다시 heap 영역 어딘가 "아이폰"이라는 데이터의 주소를 갖고 있다(String은 객체이기 때문에).** heap 영역에 있는 color와 price는 각각 'w'와 1000000 값을 갖고 있다.
+ `new` 키워드는 뒤의 생성자(Constructor)를 보고 그에 맞게 memory allocation을 수행한다.
  + 생성자는 Class 이름 + ()의 구조를 갖고 있다.

```java
public static void main(String[] args) {
    
    Phone phone = new Phone(); // Class Type 변수명 = new 생성자(Constructor);
    
    phone.name = "아이폰";
    phone.color = 'w';
    phone.price = 1000000;
    
    System.out.println(phone.getNumber());
}
```

<br/>

<br/>

### 2.3 생성자

+ 만약 **Class가 제공하는 생성자가 없을 경우, 컴파일러가 기본 생성자를 자동으로 만들어 준다.** 기본 생성자는 Parameter가 없는, 특별한 작업을 수행하지 않는 가장 단순한 생성자
+ 만약 **우리가 생성자를 만들게 된다면 컴파일러는 당연히 아무런 생성자를 만들어 주지 않는다.**
  + 즉, 만약 Parameter를 요구하는 생성자를 하나라도 만들게 된다면 우리는 앞으로 코드에서 그냥 `Phone phone = new Phone()`과 같이 만들 수 없다.  **따라서 `public Phone() {}`이라는 코드를 만들어줘야 Parameter가 없는 기본 생성자를 사용할 수 있다.**
+ **생성자는 Parameter를 가질 수 있다. 그리고, Paramater를 달리하는 여러 개의 생성자를 만들 수 있다.** 생성자가 여러 개 제공되면, 외부에서는 다양한 방법으로 객체를 만들 수 있게 된다. 보통 이 Parameter들을 이용해서 member variable(멤버 변수)들의 값을 설정한다.
+ 생성자는 메서드의 형태로, 클래스 명과 같은 이름을 갖게 된다.

```java
public class Phone {

    public String name;
    public char color;
    public int price;
    
    public String getNumber() {
        return "010-1234-5678";
    }
    // 이 부분이 생성자
    public Phone(String name) { this.name = name; }
    public Phone() {}
}
```

<br/>

<br/>

### 2.4 생성자 앞 public?

+ 위의 예에서 Phone Class의 생성자 앞에는 모두 public modifier가 있다. 이는 외부의 접근을 제한하지 않겠다는 의미다.
+ 특별한 경우, modifier를 이용하여 생성자 호출을 제한할 수도 있다. 이는 Singleton Design Pattern에서 그 예를 볼 수 있다.

#### 2.4.1 Singleton Design Pattern

+ 전역 변수를 사용하지 않고 객체를 하나만 생성 하도록 하며, 생성된 객체를 어디에서든지 참조할 수 있도록 하는 패턴

+ 하나의 인스턴스만을 생성하는 책임이 있으며 getInstance 메서드를 통해 모든 클라이언트에게 동일한 인스턴스를 반환하는 작업을 수행한다.

+ ex. 프린터

  + 프린터 하나를 10명이서 공유해서 사용한다고 하자.

  + ```java
    public class Printer {
      public Printer() { }
      public void print(Resource r) { ... }
    }
    https://gmlwjd9405.github.io/2018/07/06/singleton-pattern.html
    ```

  + 그러나 Printer 클래스를 사용해 프린터를 이용하려면 Client 프로그램에서 new Printer()가 반드시 한 번만 호출되도록 주의해야 한다. (프린터는 하나이기 때문에)

  + 이를 해소하는 방법은 생성자를 외부해서 호출할 수 없도록 하는 것이다. 즉, 기본 생성자의 modifier를 private으로 바꾼다. 그 다음, static 메서드와 static 변수, 분기를 통해 여러 Printer 객체가 생성되지 않도록 할 수 있다. 

  + ```java
    public class Printer {
      // 외부에 제공할 자기 자신의 인스턴스
      private static Printer printer = null;
      private Printer() { }
      // 자기 자신의 인스턴스를 외부에 제공
      public static Printer getPrinter(){
        if (printer == null) {
          // Printer 인스턴스 생성
          printer = new Printer();
        }
        return printer;
      }
    }
    ```

<br/>

<br/>

### 2.5 member variable

+ Class를 통해 생성된 Object들은 member variable(멤버 변수)들을 통해 각각 자신의 **상태**를 표현한다.

+ member variables의 값을 부여하는 방법에는 여러가지가 있다.

  + Class를 정의하면서 기본값으로 넣어주기

  + Class 생성자의 parameter 값으로 전달하기

    + ```java
      public class Phone {
      
          public String name;
          public char color;
          public int price;
          
          public String getNumber() {
              return "010-1234-5678";
          }
          // 이 부분
          public Phone(String name) { this.name = name; }
          public Phone() {}
      }
      ```

    + ```java
      Phone phone = new Phone("Iphone");
      ```

+ 객체를 만든 후에 직접 값을 부여하기

+ 만약 member variables 값을 설정하지 않으면, Local variable과는 다르게 new를 통해 객체가 생성될 때 기본 값이 자동으로 부여된다. (**null**)
+ 하지만, OOP에서는 객체 중심인데 현재 이러한 구조를 봤을 때 외부에서 멤버 변수를 조작할 수 있다는 것은 이상해보인다. 따라서, Class 내부에서 제어할 수 있는 구조를 가지는 것이 일반적이다.
  + 따라서, 위에서 'Class 생성자의 parameter 값으로 전달하기' 부분의 코드같은 생성자 구조를 갖는 것이 보통이고, 이후 Setters를 통해서 값을 수정하거나 한다.

<br/>

<br/>

### 2.6 Encapsulation

+ OOP의 특징 중 하나로, Class를 구성할 때 기본적으로 멤버 변수와 메서드를 외부에 노출하지 않도록 가정하고, 필요한 경우에 한해 외부에 노출하는 것을 의미
+ 외부에 노출할 경우 Setters & Getters를 제공하여 외부와 소통한다.
+ 접근제한자(Access Modifier)를 이용하여 외부에 어느 정도 노출할 것인지를 결정한다.

#### 2.6.1 Setters

+ 멤버 변수 값을 외부에서 마음대로 접근할 수 없도록 하고 대신 Setters라는 메서드를 통해서 변경 요청을 보내 Class 내부에서 이 메서드를 통하여 요청에 대한 처리를 수행한다.

```java
public void setName(String name) {
    this.name = name;
}
```

<br/>

#### 2.6.2 Getters

+ 외부에서 객체의 멤버 변수 값을 읽고자 할 때, 직접 읽게 하지 않고 별도의 메서드를 제공하는데 이러한 메서드를 Getters라고 한다.

```java
public String getName() {
    return name;
}
```

<br/>

#### 2.6.3 private member variables

+ Setters와 Getters를 제공했다고 해도 여전히 외부에서는 직접적으로 멤버 변수에 접근이 가능하다.
+ 이제 이러한 직접적인 접근들을 막아야하므로 멤버 변수의 modifier를 public에서 **private**으로 변경한다.

```java
public class Phone {
    
    private String name;
    private char color;
    private int price;
}
```

+ 최종적 코드

```java
public class Phone {

    public String name;
    public char color;
    public int price;
    
    public String getNumber() {
        return "010-1234-5678";
    }
    
    public Phone(String name) { this.name = name; }
    public Phone() {}
    
    public String getName() { return this.name; }
    public void setName(String name) { this.name = name; }
    
    public char getColor() { return this.color; }
    public void setColor(char color) { this.color = color; }
    
    public int getPrice() { return this.price; }
    public void setPrice(int price) { this.price = price; }
}
```

