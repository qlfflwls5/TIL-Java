# Array, Scanner

## 1. Array 기본

+ 동일한 type의 변수를 여러 개 사용할 경우
  + 변수의 수 증가
  + 코드의 길이 증가
  + 반복문 적용 불가
  + 변수의 수가 동적으로 결정될 경우, 사용 불가
+ Array로 동일 Type 변수 대신하기
  + 변수, 코드 길이 감소
  + 반복문 적용 가능, 변수의 수 동적으로 가능
+ 최초 Memory Allocation 이후 변경할 수 없음
+ 개별 요소는 다른 값으로 변경이 가능하나, 삭제할 수는 없음
+ 크기를 늘리거나 줄일 수 없음
+ 변경이 필요한 경우, 새로 작성하는 것이 일반적으로 유리함

### 1.1 int Type 기준 배열 만들기

| 선언             | 생성                    | 개별 요소 값 할당 |
| ---------------- | ----------------------- | ----------------- |
| int [] intArray; | intArray = new int[5];  | intArray[0] = 3;  |
| int intArray []; | intArray = new int[10]; | intArray[2] = 7;  |

+ Array는 Object -> `new` 키워드를 사용
  + **기본값** 0 (char는 '\u0000', boolean은 false)
+ Array는 index를 이용하여, 개별 값 access

```java
public static void main(String[] args) {
	
    int[] arr = new int[5];
    for (int i = 0; i < 5; i++) {
        arr[i] = (int) (Math.random()*6) + 1;
    }

    for (int i = 0; i < 5; i++) {
        System.out.println(arr[i]);
    }
}
```

#### 1.1.1 int Type 기준으로 배열과 값을 동시에 만들기

```java
int [] intArray = {0, 1, 2, 3, 4};
```

+ Array는 `{}`안에 `,`를 이용하여 선언과 동시에 값을 할당
+ **한 번 할당된 값을 변경할 수 없다. (Array Constant)** -> 이 방식으로 쓰려면 선언과 동시에 할당을 해야 한다. 두 라인으로 따로 쓸 수 없다. ex.`int [] intArray; intArray = {0, 1, 2, 3, 4};`. 또한, 다시 할당이 불가능하다.(이 뜻은 요소의 값을 바꿀 수 없다는 것은 아니다.)

<br/>

<br/>

### 1.2 char Array

+ String을 이용하여 char 배열을 만들고 이를 출력하는 코드

```java
public static void main(String[] args) {
	
    String s = "Monday";

    char[] day = new char[s.length()]; // 문자열은 .length()

    for (int i = 0; i < day.length; i++) { // 배열은 .length
        day[i] = s.charAt(i); // 문자열은 인덱스 접근이 안된다. .charAt(인덱스)를 사용. 혹은 str.substring(시작 위치, 끝 위치)
    }

    for (int i = 0; i < day.length; i++) {
        System.out.println(day[i]);
    }

}
```

+ String도 마찬가지로 `String [] s = {"M", "o", "o", "n"}`과 같이 배열 가능

<br/>

<br/>

### 1.3 Array의 출력을 우아하게 (Arrays.toString(배열))

+ for문을 통한 출력 대신에 `Arrays.toString(배열)`을 사용하여 Array를 쉽게 출력할 수 있다.

```java
import java.util.Arrays;

...
    
public static void main(String[] args) {

	String s = "Monday";

    char[] day = new char[s.length()];

    for (int i = 0; i < day.length; i++) {
        day[i] = s.charAt(i);
    }

    System.out.println(Arrays.toString(day))

}
```

+ `배열.toString()`을 하면 배열의 주소가 반환된다.

<br/>

<br/>

### 1.4 Local Variable vs Array

+ 로컬 변수는 stack이라는 공간에 '값' 자체가 저장된다. 하지만, Array는 stack이라는 공간에 '데이터의 주소'를 저장한다. 그리고 이 데이터는 heap이라는 공간에 저장한다.
  + ex. intArray는 stack에 '1000'이라는 값을 갖고 있다. heap의 1000번째 자리부터는 0, 1, 2, 3, 4가 존재한다.

<br/>

<br/>

### 1.5 for-each with Array

+ Java 5.0부터 지원하는 기능이다.
+ 가독성이 개선된 반복문으로, 배열 및 Collections에 사용한다.
+ index 대신 직접 요소에 접근하는 변수를 제공한다.
+ 값 카피의 개념이다. 원본 변화는 불가능하다.
+ `:`사용

```java
int[] intArray = {1, 3, 5, 7, 9};

for (int x: intArray) {
    System.out.println(x);
}

String[] fruits = {"사과", "바나나", "수박", "망고"};

for (String fruit: fruits) {
    System.out.println(fruit);
}
```

<br/>

<br/>

### 1.6 Swap

+ Java에서의 swap은 파이썬과는 다르게 `nums[0], nums[1] = nums[1], nums[0]`과 같이 할 수 없다.
+ temp라는 것이 필요하다.

```java
String[] fruits = {"사과", "바나나", "수박", "망고"};

String temp = fruits[0];
fruits[0] = fruits[1];
fruits[1] = temp;
```

<br/>

<br/>

### 1.7 Array 복사

+ `System.arraycopy(복사 대상, 시작 인덱스, 붙일 대상, 시작 인데스, 개수);`

```java
String[] fruits = {"사과", "바나나", "수박", "망고"};

String[] newFruits = new String[5];

System.arraycopy(fruits, 0, newFruits, 0, 4);
newFruits[4] = "파인애플";

System.out.println(Arrays.toString(newFruits)); // [사과, 바나나, 수박, 망고, 파인애플]
```

<br/>

<br/>

<br/>

## 2. Array 응용

### 2.1 최대값, 최소값

+ `Integer.MIN_VALUE`와 `Integer.MAX_VALUE`를 이용해서 최대, 최소값의 초기값을 설정할 수 있다.

```java
public static void main(String[] args) {
	
    int[] array = {3, 27, 13, 8, 235, 7, 22, 9, 435, 31, 54};

    int min = Integer.MAX_VALUE;
    int max = Integer.MIN_VALUE;

    for (int x: array) {
        if (x > max) {
            max = x;
        }
        if (x < min) {
            min = x;
        }
    }

    System.out.println("Min : " + min + " Max : " + max); // 파이썬과 다르게 자바에서는 int를 문자열에 +로 붙여서 출력할 수 있다.
}
```

+ Math Class의 메서드를 사용해서 구현할 수도 있다.
+ 두 개의 int 요소의 대소를 비교한다.  `Math.min(요소, 요소)`, `Math.max(요소, 요소)`

```java
public static void main(String[] args) {
	
    int[] array = {3, 27, 13, 8, 235, 7, 22, 9, 435, 31, 54};

    int min = Integer.MAX_VALUE;
    int max = Integer.MIN_VALUE;

    for (int x: array) {
        min = Math.min(x, min);
        max = Math.max(x, max);
    }

    System.out.println("Min : " + min + " Max : " + max);
}
```

<br/>

<br/>

### 2.2 각 숫자별 사용 횟수

+ 배열이 주어졌을 때 배열의 각 숫자가 몇 번 나왔는지 구하기

```java
public static void main(String[] args) {
	
    int[] array = {3, 7, 2, 5, 7, 7, 9, 2, 8, 1, 1, 5, 3};
    int[] used = new int[10];

    for (int i = 0; i < array.length; i++) {
        used[array[i]]++;
    }

    System.out.println(Arrays.toString(used));
}
```

#### 2.2.1 1~20까지 숫자를 사용한 배열에서 사용되지 않은 숫자를 출력

```java
public static void main(String[] args) {
	
    int[] intArray = {1, 3, 4, 7, 8, 10, 12, 15, 16, 17, 18};

    int[] used = new int[21];

    for (int i = 0; i < intArray.length; i++) {
        used[intArray[i]]++;
    }

    for (int i = 1; i < used.length; i++) {
        if (used[i] == 0) {
            System.out.print(i + " "); // print는 개행이 끝에 붙지 않는다. println의 ln은 line의 약자
        }
    }
}
```

<br/>

<br/>

<br/>

## 3. 2차원 Array

+ `int[][] grid = new int[4][3]`과 같이 만들 수 있다.

+ 이 구조에서 grid가 1000번째 주소를 갖는다고 하면 heap 공간의 1000번째에 2000, 3000, 4000, 5000의 주소 값을 갖는 데이터가 있고, heap안에 다시 각각의 주소에 3개의 값의 데이터가 있는 형태가 된다.

+ 첫 번째가 행, 두 번째가 열이라 할 때, 각 열의 길이는 달라도 상관없다.

```java
public static void main(String[] args) {
    
    int[][] grid = new int[4][3];
    int[] intArray[] = new int[4][3];
    int[][] intArray2 = new int[4]{1, 2, 3}; // 얘만 불가능
    int[][] intArray3 = new int[][] {{1, 2, 3}, {1, 2, 3}, {1, 2, 3}, {1, 2, 3}};
    int[][] intArray4 = {{1, 2, 3}, {1, 2, 3}, {1, 2, 3}};
}
```

+ 1차원 배열만 크기를 지정해 생성한 후 이후에 2차원 배열을 지정할 수 있다.

```java
int[][] grid = new int[4][];

grid[1] = new int[2];
grid[0] = new int[4];
```

+ 2차원 배열 순회

```java
public static void main(String[] args) {
	
    int[][] grid = {
        {1, 2, 3, 4, 5},
        {6, 7, 8, 9, 10},
        {11, 12, 13, 14, 15},
        {16, 17, 18, 19, 20}, 
        {21, 22, 23, 24, 25}
    };

    int count = 0;
    int sum = 0;

    for (int i = 0; i < grid.length; i++) {
        for (int j = 0; j < grid[i].length; j++) {
            count++;
            sum += grid[i][j];
        }
    }

    System.out.println(count + ", " + sum);
}
```

<br/>

<br/>

<br/>

## 4. 입력 받기 java.util.Scanner

+ `Scanner sc = new Scanner(System.in);`
+ 토큰을 읽을 때는 공백을 기준으로 읽는다.
+ 사용을 끝냈다면 `sc.close();`로 스캐너를 닫아준다.

| 메서드             | 설명                                                         |
| ------------------ | ------------------------------------------------------------ |
| `next()`           | 다음 토큰을 문자열로 리턴                                    |
| `next타입명()`     | 다음 토큰을 해당 타입으로 리턴                               |
| `nextLine()`       | 개행 문자를 포함하는 한 라인을 읽어 `\n`을 제외하고 리턴     |
| `next().charAt(0)` | 다음 토큰을 char로 리턴(`nextChar()`같은 것이 없기 때문에 문자열로 받아와 0번째를 char로써 리턴하면 된다.) |

```java
public static void main(String[] args) {
		
    Scanner sc = new Scanner(System.in);

    char[][] grid = new char[4][4];

    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            grid[i][j] = sc.next().charAt(0);
        }
    }

    for (int i = 0; i < 4; i++) {
        for (int j = 0; j < 4; j++) {
            System.out.print(grid[i][j]);
        }

        System.out.println();
    }
    sc.close();
}
```

