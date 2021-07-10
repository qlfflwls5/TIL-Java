# Class, Object, Method 2

## 1. Method

+ 동적인 특징은 '뭔가 움직인다'는 직접적인 의미가 아니라, 수행 또는 처리 되는 코드 블록을 가질 수 있다는 의미이다.
+ Class 내부, 혹은 외부에서 호출(Call) 할 수 있다.

```java
// ex
public int getNumber() {
    return 10;
}

public void setNumber(int number) {
    this.number = number;
}

public int getSalePrice() {
    if( this.color == 'A' ) {
        return this.price;
    }else {
        return this.price + 1000;
    }
}
```

+ 구조
  + **return type**
    + int, char, ..., String, Class타입, Array, void
    + 리턴값은 없거나, 하나
      + 없다면 void를 사용한다.
  + **parameter type**
    + int, char, ..., String, Class타입, Array, ()
    + 없거나, 하나, 여러개
  + **{} block**
    + 메서드를 실행하여 수행 또는 처리할 코드 블록으로, 작업의 내용이 들어있다.
    + return 하거나, 안하거나

### 1.1 호출

+ `object.메서드명(인자)`

```java
Phone phone = new Phone();

phone.setNumber(123);
```

<br/>

<br/>

<br/>

## 2. Array of Object

+ `int[] intArray = new int[5];`처럼 객체(Reference Type)도 배열로 만들 수 있다.

```java
public static void main(String[] args) {
    
    Phone[] phoneArray = new Phone[5];
    
    for (int i = 0; i < phoneArray.length; i++) {
        phoneArray[i].setPrice(i*2000);
    }
    
    // 현재 각 phone object의 type은 Phone(클래스명)
    for (Phone phone: phoneArray) {
        System.out.println(phone.getPrice());
    }
}
```

+ 위 코드의 경우, `NullPointerException` 에러가 난다. 왜?
  + Reference Type 배열을 만들고, 배열 개별 요소에 객체를 할당하지 않았기 때문이다.  객체의 기본값은 `null`이다. 따라서, 이를 고쳐보자면 아래와 같다.

```java
...
    
for (int i = 0; i < phoneArray.length; i++) {
    phoneArray[i] = new Phone();
    phoneArray[i].setPrice(i*2000);
}

...
```

