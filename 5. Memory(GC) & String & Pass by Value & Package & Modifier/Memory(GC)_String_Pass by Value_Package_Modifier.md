# Memory(GC)_String_Pass by Value_Package_Modifier

## 1. JVM Memory

### 1.1 개요

+ Java는 고급 언어로 메모리 관리로부터 자유롭다. 하지만, 알아두어야 할 몇 가지 내용이 있다.
+ JVM은 주요하게 3개의 메모리 영역을 가지고 있다.
  + **Class Area(Method Area)**
    + 클래스의 기본 정보, 그 안의 메서드의 정보, Static
  + **Heap**
    + 객체가 실제로 만들어지는 공간(new를 통해 만들어지면 전부 이 곳)
  + **Stack**
    + Call을 위한(메서드 호출)
    + Primitive Type의 변수는 Stack영역에서 직접 값을 가진다.
      + 논리형(boolean), 정수형(byte, short, int, long), 실수형(float, double), 문자형(char)
    + Reference Type의 변수는 Stack영역에서 힙 영역이나 메소드 영역의 객체 주소를 가진다.

<br/>

<br/>

### 1.2 예시

```java
public static void main(String[] args) {
		
    int i = 10;
    String s1 = "Hello";
    String s2 = new String("World");
    Phone phone = new Phone("S20");

    System.out.println(i); // 10
    System.out.println(s1); // Hello
    System.out.println(s2); // World
    System.out.println(phone); // 객체의 주소
}
```

+ Stack
  + i - 10
  + s1 - 1000(예시 주소)
  + s2 - 2000
  + phone - 3000
+ Heap
  + 1000 - "Hello"
  + 2000 - "World"
  + 3000 - "S20"
+ Class Area
  + 프로젝트 Class
  + String(s1 부분에서 생김)
  + Phone

<br/>

<br/>

### 1.3 Garbage Collection

> https://mangkyu.tistory.com/118

+ C나 C++은 개발자가 메모리 관리를 해야하기 때문에 매우 힘들다.
+ 하지만, Java에서는 new 연산자를 통해서 메모리 할당을 하고 나면 메모리 해제는 JVM이 알아서 처리해준다.
+ JVM은 Heap에 만들어진 객체 중 더 이상 참조되지 않은 것들을 대상으로 적절한 시점에 Garbage Collection 작업을 수행한다.
  + 우리는 Garbage Collection에 직접 관여할 수 없다.
  + 자동으로 처리된다는 점은 코딩 관점에서는 장점이지만 운영 관점에서는 단점이 된다.
  + 불필요한 객체 생성을 지양한다.

#### 1.3.1 GC 개요

+ Java의 GC는 두 가지 가정하에 만들어졌다.(Weak Generational Hypothesis)

  + 대부분의 객체는 금방 접근 불가능 상태(unreachable)가 된다.
  + 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.

+ 즉, 객체는 대부분 일회성되며, 메모리에 오랫동안 남아있는 경우는 드물다는 것이다. 그렇기 때문에 객체의 생존 기간에 따라 물리적인 Heap 영역을 나누게 되었는데, 이에 따라 Young, Old 총 2가지 영역으로 설계되었다.

  + Young 영역(Young Generation)
    + 새롭게 생성된 객체가 할당(Allocation)되는 영역
    + 대부분의 객체가 금방 Unreachable 상태가 되기 때문에, 많은 객체가 Young 영역에 생성되었다가 사라진다.
    + Young 영역에 대한 가비지 컬렉션(Garbage Collection)을 Minor GC라고 부른다.

  + Old 영역(Old Generation)
    + Young영역에서 Reachable 상태를 유지하여 살아남은 객체가 복사되는 영역
    + 복사되는 과정에서 대부분 Young 영역보다 크게 할당되며, 크기가 큰 만큼 가비지는 적게 발생한다.
    + Old 영역에 대한 가비지 컬렉션(Garbage Collection)을 Major GC 또는 Full GC라고 부른다.

+ 예외적인 상황으로 Old 영역에 있는 객체가 Young 영역의 객체를 참조하는 경우도 존재할 것이다. 이러한 경우를 대비하여 Old 영역에는 512 bytes의 덩어리(Chunk)로 되어 있는 카드 테이블(Card Table)이 존재한다.

+ 카드 테이블에는 Old 영역에 있는 객체가 Young 영역의 객체를 참조할 때 마다 그에 대한 정보가 표시된다. 카드 테이블이 도입된 이유는 간단한다. Young 영역에서 가비지 컬렉션(Minor GC)가 실행될 때 모든 Old 영역에 존재하는 객체를 검사하여 참조되지 않는 Young 영역의 객체를 식별하는 것이 비효율적이기 때문이다. 그렇기 때문에 Young 영역에서 가비지 컬렉션이 진행될 때 카드 테이블만 조회하여 GC의 대상인지 식별할 수 있도록 하고 있다.

<br/>

#### 1.3.2 GC 동작 방식

+ Young 영역과 Old 영역은 서로 다른 메모리 구조로 되어 있기 때문에, 세부적인 동작 방식은 다르다. 하지만 기본적으로 가비지 컬렉션이 실행된다고 하면 다음의 2가지 공통적인 단계를 따르게 된다.

  1. Stop The World

  2. Mark and Sweep

**1. Stop The World**

+ Stop The World는 가비지 컬렉션을 실행하기 위해 JVM이 애플리케이션의 실행을 멈추는 작업이다.
+ GC가 실행될 때는 GC를 실행하는 쓰레드를 제외한 모든 쓰레드들의 작업이 중단되고, GC가 완료되면 작업이 재개된다. 
+ 당연히 모든 쓰레드들의 작업이 중단되면 애플리케이션이 멈추기 때문에, GC의 성능 개선을 위해 튜닝을 한다고 하면 보통 stop-the-world의 시간을 줄이는 작업을 하는 것이다. 또한 JVM에서도 이러한 문제를 해결하기 위해 다양한 실행 옵션을 제공하고 있다.

**2. Mark and Sweep**

- Mark: 사용되는 메모리와 사용되지 않는 메모리를 식별하는 작업
- Sweep: Mark 단계에서 사용되지 않음으로 식별된 메모리를 해제하는 작업

+ Stop The World를 통해 모든 작업을 중단시키면, GC는 스택의 모든 변수 또는 Reachable 객체를 스캔하면서 각각이 어떤 객체를 참고하고 있는지를 탐색하게 된다. 그리고 사용되고 있는 메모리를 식별하는데, 이러한 과정을 Mark라고 한다. 이후에 Mark가 되지 않은 객체들을 메모리에서 제거하는데, 이러한 과정을 Sweep라고 한다.

<br/>
<br/>

<br/>

## 2. String

### 2.1 선언 및 할당

+ 숫자나 문자 한 글자는 Primitive Type으로 선언과 동시에 값의 범위가 정해진다.

+ 문자열은 본질적으로 **가변적**이기 때문에 Primitive Type으로 표현하기 어려워 Java에서는 **Class**로 정의하고 기본으로 제공한다.

+ 특징은 Primitive Type처럼 `new`없이 바로 문자열을 값으로 줄 수도 있고 Reference Type처럼 `new`연산자를 이용해 값을 줄 수도 있다는 것

  + 다만 전자는 객체 상수(일종의 리터럴)으로서 String Constant Pool(Heap에 있음)에 관리되고, 같은 문자열을 값으로 할당하는 경우 이 객체 상수가 재사용된다.
  + 후자는 각각 `new`에 의해 Heap에 서로 다른 객체를 생성하고 그 주소값을 저장한다.

  + 아래 코드를 예시로 보자.

```java
public static void main(String[] args) {
    
    String s1 = "Hello";
    String s2 = "Hello";
    String s3 = new String("Hello");
    String s4 = new String("Hello");
    
    if (s1 == s2) { System.out.println("s1 s2 Same"); } // 출력됨
    if (s3 == s4) { System.out.println("s3 s4 Same"); } // 출력되지 않음
}
```

+ s1과 s2는 String Constant Pool에 생성된 "Hello"를 참조한다. 따라서, 주소값 자체가 같다.
+ s3와 s4는 각각 Heap영역에 생성된 서로 다른 "Hello"를 참조한다. 따라서, 주소값이 다르다.
+ 이때, 문자열의 내용이 같은지만을 확인하려면 `문자열.equals(문자열)`를 이용하면 된다.
  + `s3.equals(s4)` -> true

<br/>

<br/>

### 2.2 +, SrtingBuilder

+ 여러개의 String을 이어붙여서 새로운 String을 만드는 경우
  + `+`연산자를 사용하는 방법
    + String 객체가 새로 만들어진다. 예를 들어 s1 + s2를 통해 s3를 만들면 총 3개의 객체가 존재하게 된다.
    + 불필요한 객체가 많이 만들어져서 성능에 영향을 미칠 수 있다.
  + StringBuilder를 사용하는 방법
    + 문자열을 결합하는 경우 하나의 메모리 주소만을 갖게 된다.
+ 간단한 +의 나열은 컴파일러가 내부적으로 StringBuilder를 사용해 처리한다. 하지만, 반복 등의 구조 안에서는 그렇지 않다.

```java
public static void main(String[] args) {
		
    String s1 = "Hello";
    String s2 = "World";
    String s3 = s1 + ", " + s2;

    System.out.println(s3);

    StringBuilder sb = new StringBuilder("");
    sb.append(s1).append(", ").append(s2);
	
    // 사실 StringBuilder는 그냥 출력해도 .toString()이 기본적으로 적용된다.
    System.out.println(sb.toString());
	
    // 문제가 생기는 예시
    String[] strArray = {"Hello", ", ", "World" };
    String str = "";
    for( String s : strArray ) {
        str += s;
    }
    System.out.println(str);

    sb.setLength(0);
    for( String s : strArray ) {
        sb.append(s);
    }
    System.out.println(sb);

}
```

<br/>

<br/>

### 2.3 toString()

+ 어떤 객체의 상태를 표현하는 가장 간단한 방법은 `toString()` 메서드를 사용하는 것이다.

+ `toString()` 메서드는 객체라면 생성과 동시에 자동으로 만들어진다(Object Class로부터 상속 받는다). default로 객체의 주소 정보를 String Type으로 반환한다.

+ `System.out.println(객체)`로 멤버 변수의 값을 출력하고 싶다면, `toString()` 메서드를 재정의하면 된다.

  + 상속과 관련된 이야기로 나중에 자세히 알아보겠다.

  + 예시 코드는 다음과 같다.

  + ```java
    public String toString() {
        return this.name + " " + this.color + " " + this.price;
    }
    ```

<br/>

<br/>

<br/>

## 3. Pass By Value

+ Java에서 생성자 또는 메서드를 호출할 때, 전달되는 인자의 값이 어떤 방식으로 전달될까?

```java
public class PassByValueTest {
	public static void main(String[] args) {
		int i = 10;
		setVal(i);
		System.out.println(i); // 10 출력
		
		Pass p = new Pass();
		p.val = 10;
		setVal(p);
		System.out.println(p.val); // 5 출력
	}
	
	public static void setVal(int x) { x = 5; }
	
	public static void setVal(Pass p) { p.val = 5; }
}

class Pass{
	public int val = 3;
}
```

+ 기본적으로 매개변수는 함수내에서 **새로 생성되는 변수**이다.
+ `setVal(i)`에서는 i가 stack에서 가지고 있는 값 10을 전달한다. 이를 x라는 새로 생성된 변수에 할당한다. x가 5로 변하고 끝난다.
+ `setVal(p)`에서는 p가 stack에서 가지고 있는 값, p의 주소를 전달한다. 이를 p라는 새로 생성된 객체의 주소로 한다. 해당 객체의 주소가 가리키는 heap영역의 val이 갖는 값 10을 찾아 5로 바꾸므로, 해당 객체의 주소를 통해 val의 값을 찾으면 앞으로 5를 반환한다.

<br/>

<br/>

<br/>

## 4. package

+ 한 개의 Java 파일에 모든 코드를 다 담을 수는 없다. Module 별로 나누어서 관리하는 게 일반적이다.
+ 파일을 계층적 구조로 관리하기 위해 폴더를 사용하듯이, Java에서는 Class를 package라는 구조를 통해 계층적으로 관리한다.

+ 보통 www를 제외한 도메인의 역순 구조를 많이 사용한다.

  + www.test.com이 도메인이라면 com.test가 기본 package가 된다. 그 하위 package는 업무 구분 등으로 구성한다.
  + 후에 컴파일된 .class 파일도 package의 구분자에 맞게 폴더를 구성하여 생성된다.

  + C:\Users\계정명\eclipse-workspace\Test\src\com\test\Test.java

### 4.1 import

+ 다른 package에 정의된 Java 모듈을 사용하고자 할 때는 import를 사용한다.
  + `import 패키지명.모듈`
    + `import com.test.*;`
  + 혹은, import 없이 사용하고자 하는 Java 모듈 앞에 package명을 붙여서 사용한다.
    + `com.test.Test`

<br/>

<br/>

### 4.2 java.lang package

+ Java에서 기본적으로 제공하는 package
+ System이나 String같이 자주 쓰고 필수적인 것들이 들어있다.
+ JAVA API Document에서 확인 가능

<br/>

<br/>

<br/>

## 5. Access Modifier

+ 앞서 사용해보았던 public, private과 같은 것들이 Class의 멤버 변수 혹은 메서드 앞에 위치해 외부의 접근을 제한하는 Access Modifer이다.

|   구분    | 같은 클래스 | 같은 패키지 | 하위 클래스에서 상위 클래스를 볼 때<br />(상위 클래스의 Modifier) | 전체(다른 패키지도) |
| :-------: | :---------: | :---------: | :----------------------------------------------------------: | :-----------------: |
|  private  |      O      |      X      |                              X                               |          X          |
| (default) |      O      |      O      |                              X                               |          X          |
| protected |      O      |      O      |                              O                               |          X          |
|  public   |      O      |      O      |                              O                               |          O          |
