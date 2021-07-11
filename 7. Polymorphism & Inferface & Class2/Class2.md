# Class2

## 1. 추상 Class

+ 추상 Class는 Class이지만 추상 메서드를 가져 스스로 객체를 만들지 못한다.
+ abstract keyword를 class 앞에 붙여 추상 Class임을 표시한다. 추상 메서드 앞에도 abstract keyword를 붙인다.
  + 일반 메서드는 기본 그대로 쓴다.
+ 추상 Class를 상속받는 Class는 interface를 구현하는 Class처럼 추상 메서드의 구현 의무를 가진다. 단일 상속 기준에 의해 추상 Class를 상속하면 다른 Class를 상속할 수 없다.

```java
public abstract class AbstractFolder {
	public abstract void fold();
	public abstract void open();
	
	public void start() {
		System.out.println("start!!");
	}
}
```

+ 추상 메서드가 많은 interface를 사용할 경우, Class가 직접 interface를 implements하면서 사용하지 않는 메서드까지 모두 구현해야 하는 불편함이 있는데, 이를 해소하는 용도로 많이 사용되었다. Java API의 많은 추상 Class 대부분이 ~Adapter라는 이름을 가지고 있는데, 전술한 용도로 사용되는 것들이다.

```java
// interface
interface IF {
    void m1();
    void m2();
    void m3();
    void m4();
    void m5();
}
```

```java
// interface를 단순 implement한 Class
class example implements IF {
    @Override
    public void m1() {}
    
    @Override
    public void m2() {}
    
    @Override
    public void m3() {}
    
    @Override
    public void m4() {}
    
    @Override
    public void m5() {}
}
```

```java
// 추상화 Class(Adapter)
abstract class IFAdapter implements IF {
    public void m1() {};
    public abstract void m2();
    public void m3() {};
    public void m4() {};
    public abstract void m5();
}
```

```java
// Adapter를 상속받은 Class
class example extends IFAdapter {
    @Override
    public void m2() {}
    
    @Override
    public void m5() {}
}
```

+ 추가)
  + interface에서는 메서드의 access modifier를 생략할 경우 기본적으로 default가 아니라 public이 된다.
  + 메서드를 override 할 경우, 상위 Class의 access modifier보다 visibility를 낮게 줄 수 없다.

<br/>

<br/>

<br/>

## 2. instanceof

+ 현재 Reference 되고 있는 객체가 상속 관계에서 어떤 객체인지를 확인할 필요가 있다.
+ `instanceof` 연산자는 참조될 수 있는 type인지 확인하고 그 결과를 boolean으로 반환한다.

### 2.1 예시

1. FolderblePhone은 Phone을 상속받고 있는 클래스다.

```java
Folder f = new FolderblePhone();

if (f instanceof Folder) {
    System.out.println("instanceof Folder"); // 출력
}

if (f instanceof Object) {
    System.out.println("instanceof Object"); // 출력
}

if (f instanceof Phone) {
    System.out.println("instanceof Phone"); // 출력
}

Phone p = new Phone();

if (p instanceof ForderblePhone) {
    System.out.println("instanceof ForderblePhone"); // 미출력
}
```

2. A - B - C로 이어진 상속 관계에 있는 클래스들이다. 어떤 문제가 있을까?

```java
C c = new C();

if (c instance of A) {
    System.out.println("A");
}else if (c instance of B) {
    System.out.println("B");
}else if (c instance of C) {
    System.out.println("C");
}
```

+ if의 체계를 볼 때 가장 범위가 작은 것을 먼저 체크해야 한다. 즉, 조건이 역순으로 되어 하위 Class부터 검사해야 한다.

<br/>

<br/>

<br/>

## 3. inner class

### 3.1 inner Class - local

+ Class 안에서 다시 정의되는 Class를 말한다. 
+ 바깥 Class의 일부처럼 사용되며, 별도의 객체가 만들어진다.

```java
public class Phone {
    class UsimChip {
        String serialNo;
    }
    
    public Phone(String serialNo) {
        UsimChip uc = new UsimChip();
        uc.serialNo = serialNo;
    }
}
```

```java
public static void main(String[] args) {
    Phone p = new Phone("1234");
    System.out.println(p.uc.serialNo); // 1234
}
```

<br/>

<br/>

### 3.2 inner Class - anonymous

+ Class 안에서 이름이 없이 만들어지는 inner Class다.

  + ```java
    new InterfaceName/ClassName() {...}
    ```

  + 아래 코드에서 new 부분부터가 anonymous Class

+ 이름이 없으므로 재사용되지 않고 한 번만 사용되고, 객체를 생성하는 코드에 바로 Class의 내용을 전달한다.

+ 주로 이벤트 핸들링처럼 interface에 정의된 메서드의 구현부를 객체 생성 시점에 인자로 바로 전달한다.

```java
public class AnonymousFolder {
	private Folder folder;
	
	public void setFolder(Folder folder) {
		this.folder = folder;
	}
	
	public Folder getFolder() {
		return this.folder;
	}
}
```

```java
public static void main(String[] args) {
    AnonymousFolder af = new AnonymousFolder();
    // 이 부분에 주목하면 된다. Folder를 넘겨야 하는데, 바로 객체를 생성하면서 전달한다.
    // 즉, Folder anonymous = new MyAnonymousFolder(); af.setFolder(anonymous);를 줄인 것
    af.setFolder(new Folder() {

        @Override
        public void fold() {
            System.out.println("Anonymous-fold");
        }

        @Override
        public void open() {
            System.out.println("Anonymous-open");
        }

    });

    af.getFolder().fold();
    af.getFolder().open();
}
```

<br/>

<br/>

### 3.3 inner Class - static

+ Class 안에서 다시 정의되는 Class를 말한다.
+ 다른 Class에서 사용하지 않고 바깥 Class에서만 의미가 있을 경우 많이 사용하며, 별도의 객체를 만들지 않고 사용할 수 있다.

```java
public class Phone {
    static class UsimChip {
        public static String serialNo = "1234";
    }
}
```

```java
public static void main(String[] args) {
    System.out.println(Phone.UsimChip.serialNo);
}
```

<br/>

<br/>

<br/>

## 4. final Class

+ 더 이상 자식 Class를 만들지 않으려면 final keyword를 class 앞에 사용하면 된다.

```java
public final class Phone {
    ...
}
```

```java
public class ChildPhone extends Phone {
    // Phone 부분에 빨간 밑줄이 그어진다. The type ChildPhone cannot subclass the final class Phone.
    ...
}
```

<br/>

<br/>

<br/>

## 5. class 외 Usage Modifier

| 구분     | 설명                                                         | 예시             |
| -------- | ------------------------------------------------------------ | ---------------- |
| static   | Class의 경우 별도의 객체를 생성하지 않고 바로 사용할 수 있다.<br/>static method 안에서 `this.`, `super.`을 사용할 수 없으며, 재정의 할 수 없다. | Math.random()    |
| final    | Class의 경우 상속이 되지 않는다.<br/>상수가 되어, 더 이상 값을 변경할 수 없다.<br/>final method는 재정의 할 수 없다. | java.lang.String |
| abstract | abstract Class는 객체를 만들 수 없다.<br/>abstract method는 body를 가지고 있지 않다. |                  |

<br/>

<br/>

<br/>

## 6. static {}

+ Class가 Memory에 로드된 직후에 한 번만 수행되는 부분으로, 수행 코드를 넣을 수 있다.
+ Class 내부에서만 사용되어야 하는, 초기화 코드 용도로 많이 사용된다.

```java
public class Phone {
    static {
        System.out.println("폰 생성");
    }
    
    static final int MAX_SIZE = 10;
}
```

+ `new Phone()`을 통해서 로드가 될 때 실행된다. 이후의 `new Phone()`에서는 실행되지 않는다.

