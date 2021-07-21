# Collection

## 1. <> Generic

+ 객체를 여러 개 저장해야 할 경우가 많다. 이때, Array를 생각할 수 있는데, 속도가 빠르더라도 index 제어 등 불편한 부분이 많다.
+ Collections API를 이용하면, 편한 방법으로 객체의 집합을 관리할 수 있다.
+ 이러한 Collection을 포함하여, Java에서는 무언가를 담는 Container를 만들 때, **담을 객체의 Type을 다이나믹하게 지정**할 수 있는 방법을 제공한다.
+ Java 1.5부터 Generic `<>`을 도입해서, Class Code 작성 시점에 임의의 타입(ex. \<T>)을 사용하도록 하고, 그 Class를 사용하는 Code에서 \<T> 대신 실제 사용하는 타입 (ex. \<String>)을 사용할 수 있도록 했다.

```java
public class Generic<T> {
	private T obj;
	
	public Generic() {}
	
	public T getObj() { return obj; }
	
	public void setObj(T t) { obj = t; }
}
```

```java
public class Main {
	public static void main(String[] args) {
		Generic<Integer> g1 = new Generic<>();
		g1.setObj(3);
		
		Generic<String> g2 = new Generic<>();
		g2.setObj("Hello");
		
		System.out.println(g1.getObj()); // 3
		System.out.println(g2.getObj()); // Hello
	}
}
```

<br/>

<br/>

<br/>

## 2. Collections API

>  https://docs.oracle.com/javase/8/docs/api/
>
>  java.util > Collection 참고

Collection에는 List, Set, Queue라는 interface가 있다. (Key-Value 구조의 Map이라는 구조도 따로 있다.)

List에는 Stack, ArrayList, LinkedList가 있고, Set에는 HashSet, TreeSet이 있고, Queue에는 LinkedList(중복), PriorityQueue가 있다.

| interface | 구현 Class                         | 설명                                                  |
| --------- | ---------------------------------- | ----------------------------------------------------- |
| List      | LinkedList<br/>Stack<br/>ArrayList | 순서(o)<br/>중복(o)                                   |
| Set       | HashSet<br/>TreeSet                | 순서(x)<br/>중복(x)                                   |
| Queue     | LinkedList<br/>PriorityQueue       | 순서(o)<br/>중복(o)                                   |
| Map       | Hash Table<br/>HashMap<br/>TreeMap | Key, Value 쌍, 순서(x)<br/>Key 중복(x), Value 중복(o) |

+ 주요 method

``` java
boolean add(Object o)
boolean remove(Object o)
boolean isEmpty()
void clear()
int size()
boolean contains(Object o)
Object[] toArray()
Iterator<E> iterator()
```

### 2.1 List

+ Organization.java

```java
public class Organization {
	private String name;
	private int employeeCount;
	
	public Organization() {};
	
	public Organization(String name, int employeeCount) {
		this.name = name;
		this.employeeCount = employeeCount;
	}
	
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getEmployeeCount() {
		return employeeCount;
	}
	public void setEmployeeCount(int employeeCount) {
		this.employeeCount = employeeCount;
	}
	
	public void about() {
		System.out.println("Organization : " + name );
	}
}
```

+ Hospital.java

```java
public class Hospital extends Organization implements MedicalAction{
	private CDC cdc;
	
	private String hospitalId;
	private int roomTotalCount;
	private int roomEmptyCount;

	public Hospital() {}
	public Hospital(String name, int employeeCount, String hospitalId, int roomTotalCount, int roomEmptyCount) {
		super(name, employeeCount);
		this.hospitalId = hospitalId;
		this.roomTotalCount = roomTotalCount;
		this.roomEmptyCount = roomEmptyCount;
	}
	
	public CDC getCdc() {
		return cdc;
	}
	public void setCdc(CDC cdc) {
		this.cdc = cdc;
	}
	public String getHospitalId() {
		return hospitalId;
	}
	public void setHospitalId(String hospitalId) {
		this.hospitalId = hospitalId;
	}
	public int getRoomTotalCount() {
		return roomTotalCount;
	}
	public void setRoomTotalCount(int roomTotalCount) {
		this.roomTotalCount = roomTotalCount;
	}
	public int getRoomEmptyCount() {
		return roomEmptyCount;
	}
	public void setRoomEmptyCount(int roomEmptyCount) {
		this.roomEmptyCount = roomEmptyCount;
	}
	
	public void about() {
		System.out.println("We are Hospital : " + super.getName());
	}
	@Override
	public void addPatient(Patient p) {
        // Collection API - add
		cdc.getPatientList().add(p);
	}
	@Override
	public void removePatient(Patient p) {
        // COllection API - remove
		cdc.getPatientList().remove(p);
		
	}	
}
```

+ CDC.java

```java
import java.util.List;

public class CDC extends Organization {
    // Collection - List
	private List<Hospital> hospitalList;
	private List<Patient> patientList;
	
	public CDC() {}
	public CDC(String name, int employeeCount, List<Hospital> hospitalList, List<Patient> patientList) {
		super(name, employeeCount);
		this.hospitalList = hospitalList;
		this.patientList = patientList;
	}
	
	public List<Hospital> getHospitalList() {
		return hospitalList;
	}
	public void setHospitalList(List<Hospital> hospitalList) {
		this.hospitalList = hospitalList;
	}
	public List<Patient> getPatientList() {
		return patientList;
	}
	public void setPatientList(List<Patient> patientList) {
		this.patientList = patientList;
	}
	
	public void about() {
		System.out.println("Organization - CDC " + super.getName() );
	}
}
```

+ MedicalAction.java

```java
public interface MedicalAction {
	void addPatient(Patient p);
	void removePatient(Patient p);
}
```

+ MainTest.java

```java
import java.util.ArrayList;
import java.util.List;

public class MainTest {

	public static void main(String[] args) {
		Hospital hanyangHospital = new Hospital("한양대병원", 15, "001", 50, 10);
		Hospital seoulHospital = new Hospital("서울대병원", 25, "002", 100, 20);
		
		Patient p1 = new Patient("김환자", 25);
		Patient p2 = new Patient("박환자", 21);
		
		List<Hospital> hospitalList = new ArrayList<Hospital>();
		hospitalList.add(hanyangHospital);
		hospitalList.add(seoulHospital);
		
		List<Patient> patientList = new ArrayList<Patient>();
		patientList.add(p1);
		patientList.add(p2);
		
		CDC cdc = new CDC("질병관리본부", 200, hospitalList, patientList);
		cdc.about(); // Organization - CDC 질병관리본부
	}

}
```

<br/><br/>

### 2.2 iterator

> https://docs.oracle.com/javase/8/docs/api/ java.util > iterator

대부분의 Collection Class들은 iterator interface를 구현하고 있다. iterator의 메서드들을 통해서 Collection의 각 객체에 접근할 수 있다.

+ for문이나 for each문을 통한 Collection 순회도 가능하지만, iterator를 사용하자.

+ iterator의 메서드 `hasNext()`와 `next()`
  + `hasNext()`: 이후 요소가 있다면 true를 리턴
  + `next()`: 다음 요소를 리턴

```java
// CDC.java에 추가
public void printPatienList() {
    Iterator<Patient> itr = patientList.iterator();
    while(itr.hasNext()) {
        Patient p = itr.next();
        System.out.println(p);
    }
}
```

```java
// Patient.java에 추가
@Override
public String toString() {
    StringBuilder sb = new StringBuilder();
    sb.append(this.getName() + ' ').append(this.getAge());
    return sb.toString();
}
```

```java
// MainTest.java에 추가
cdc.printPatienList(); // 김환자 25(개행) 박환자 21
```

<br/>

<br/>

### 2.3 에러 사항

+ 새로운 환자를 등록해본다.

```java
import java.util.ArrayList;
import java.util.List;

public class MainTest {

	public static void main(String[] args) {
		Hospital hanyangHospital = new Hospital("한양대병원", 15, "001", 50, 10);
		Hospital seoulHospital = new Hospital("서울대병원", 25, "002", 100, 20);
		
		Patient p1 = new Patient("김환자", 25);
		Patient p2 = new Patient("박환자", 21);
		
		List<Hospital> hospitalList = new ArrayList<Hospital>();
		hospitalList.add(hanyangHospital);
		hospitalList.add(seoulHospital);
		
		List<Patient> patientList = new ArrayList<Patient>();
		patientList.add(p1);
		patientList.add(p2);
		
		CDC cdc = new CDC("질병관리본부", 200, hospitalList, patientList);
		cdc.about();
		
		// 새로운 환자 등록 -> NullPointerException 에러
		Patient p3 = new Patient("이환자", 27);
		hanyangHospital.addPatient(p3);
		
		cdc.printPatienList();
	}

}
```

+ 에러가 나는 이유? -> Hospital.java에 가보면 CDC 인스턴스 cdc를 만들어놓고 그대로 cdc의 변경이 일어나지 않음. 즉, cdc는 모든 Hospital의 인스턴스에서 null인 상태. 이 상태에서 addPatient 메서드를 호출하니 null에서 참조하게 되는 것
+ 위 코드에서 생성한 cdc를 hanyangHospital의 cdc에 연결해줘야 한다.

```java
import java.util.ArrayList;
import java.util.List;

public class MainTest {

	public static void main(String[] args) {
		Hospital hanyangHospital = new Hospital("한양대병원", 15, "001", 50, 10);
		Hospital seoulHospital = new Hospital("서울대병원", 25, "002", 100, 20);
		
		Patient p1 = new Patient("김환자", 25);
		Patient p2 = new Patient("박환자", 21);
		
		List<Hospital> hospitalList = new ArrayList<Hospital>();
		hospitalList.add(hanyangHospital);
		hospitalList.add(seoulHospital);
		
		List<Patient> patientList = new ArrayList<Patient>();
		patientList.add(p1);
		patientList.add(p2);
		
		CDC cdc = new CDC("질병관리본부", 200, hospitalList, patientList);
		cdc.about();
		
		// 병원에 cdc 연결 -> 이 부분 없으면 아래 새로운 환자 등록에서 NullPointerException 에러
		hanyangHospital.setCdc(cdc);
		seoulHospital.setCdc(cdc);
		
		// 새로운 환자 등록
		Patient p3 = new Patient("이환자", 27);
		hanyangHospital.addPatient(p3);
		
		cdc.printPatienList(); // 김환자 25(개행) 박환자 21(개행) 이환자 27
	}

}
```

<br/>

<br/>

### 2.4 중복 객체 삭제

동일한 객체가 여러번 리스트에 포함이 되는 경우 동일한 객체들을 한 번에 삭제하고 싶을 수도 있다. Collection에서는 객체를 삭제하는 경우에는 전달되는 객체와 같은 객체가 있는지를 비교해서 처리한다. 이때, Object Class를 상속받았기 때문에 `equals()`라는 메서드를 사용해 `==`의 비교를 수행하고 주소값을 비교하여 동일한 객체인지를 찾는다. 즉, 다른 정보 중복 객체가 한 번에 삭제되지 않는다(당연히 다른 객체로 본다). 그렇다면, 비교의 수행에 사용되는 `equals()`를 재정의해야 할 것이다.

+ 단순히 정보가 같은 새로운 객체를 만들어 그 객체로 기존의 객체 삭제 시도해보기 -> 안됨

```java
// MainTest.java

...
// 새로운 환자 등록
Patient p3 = new Patient("이환자", 27);
hanyangHospital.addPatient(p3);

// p3과 정보가 같은 환자를 만들어 p3 삭제 시도
Patient p4 = new Patient("이환자", 27);
hanyangHospital.removePatient(p4);

cdc.printPatienList(); // 김환자 25(개행), 박환자 21(개행), 이환자 27
...
```

+ Patient.java의 `equals()` 재정의

```java
@Override
// 객체를 받아서
public boolean equals(Object o) {
    // 그 객체를 Patient로 바꾼 것이 p
    Patient p = (Patient) o;
    // 이름과 나이가 모두 같으면 true 반환(삭제하게 됨)
    if (this.getName().equals(p.getName()) && this.getAge() == p.getAge()) {
        return true;
    }
    return false;
}
```

```java
// MainTest.java
cdc.printPatienList(); // 김환자 25(개행) 박환자 21
```

<br/>

<br/>

### 2.5 중복 객체 등록 차단(제거)

Collection 중에 중복을 허용하지 않는 것은 Set이다. 순서가 상관없다면 List, ArrayList를 Set, HashSet으로 바꾼다면 자동으로 중복 객체를 없앨 수 있다.

+ CDC.java 변경

```java
// CDC.java
import java.util.Iterator;
import java.util.List;
import java.util.Set;

public class CDC extends Organization {
	private List<Hospital> hospitalList;
    // 여기서부터 이하 List를 Set으로 변경
	private Set<Patient> patientList;
	
	public CDC() {}
	public CDC(String name, int employeeCount, List<Hospital> hospitalList, Set<Patient> patientList) {
		super(name, employeeCount);
		this.hospitalList = hospitalList;
		this.patientList = patientList;
	}
	
	public List<Hospital> getHospitalList() {
		return hospitalList;
	}
	public void setHospitalList(List<Hospital> hospitalList) {
		this.hospitalList = hospitalList;
	}
	public Set<Patient> getPatientList() {
		return patientList;
	}
	public void setPatientList(Set<Patient> patientList) {
		this.patientList = patientList;
	}
	
	public void about() {
		System.out.println("Organization - CDC " + super.getName() );
	}
	
	public void printPatienList() {
		Iterator<Patient> itr = patientList.iterator();
		while(itr.hasNext()) {
			Patient p = itr.next();
			System.out.println(p);
		}
	}
}
```

```java
// MainTest.java
// 새로운 환자 2번 등록
Patient p3 = new Patient("이환자", 27);
hanyangHospital.addPatient(p3);
hanyangHospital.addPatient(p3);

cdc.printPatienList(); // 김환자 25(개행) 박환자 21(개행) 이환자 27
```

근데, 과연 정말 중복된 정보를 갖는 객체를 차단할 수 있게 된 것일까? 이번엔 같은 정보를 갖는 새로운 환자 인스턴스를 만들어보자.

```java
// 새로운 환자 2번 등록
Patient p3 = new Patient("이환자", 27);
hanyangHospital.addPatient(p3);
hanyangHospital.addPatient(p3);

// p3과 정보가 같은 환자 p4를 만들어 등록
Patient p4 = new Patient("이환자", 27);
hanyangHospital.addPatient(p4);

cdc.printPatienList(); // 이환자 27(개행) 김환자 25(개행) 박환자 21(개행) 이환자 27
```

안된다. HashSet은 두 객체의 중복 여부를 체크할 때 List 계열보다 엄격하다. Object Class의 `hashCode()`를 이용하여 그 값이 같은 경우에 같은 객체로 인식한다. 객체의 정보가 다르면 다른 `hashCode()`를 리턴하도록 overriding 한다.

+ Patient.java에 `hashCode()` overriding

```java
@Override
public int hashCode() {
    int hash = 7;
    hash = 31 * hash + this.getName().hashCode();
    hash = 31 * hash + this.getAge();
    return hash;
}
```

```java
// 새로운 환자 2번 등록
Patient p3 = new Patient("이환자", 27);
hanyangHospital.addPatient(p3);
hanyangHospital.addPatient(p3);

// p3과 정보가 같은 환자 p4를 만들어 등록
Patient p4 = new Patient("이환자", 27);
hanyangHospital.addPatient(p4);

cdc.printPatienList(); // 이환자 27(개행) 김환자 25(개행) 박환자 21
```

<br/>

<br/>

### 2.6 PriorityQueue

> https://docs.oracle.com/javase/8/docs/api/ java.lang > Comparable

넣고 꺼내는 과정에서 순서가 있다면 Stack이나 Queue를 쓰면 되는데, 어떠한 기준을 토대로 순서대로 꺼내고 싶다면 Queue의 PriorityQueue를 사용하면 된다.

+ PriorityQueue를 이용해서 객체를 정렬할 때, 그 객체는 Comparable Interface를 구현해야 한다.
+ Comparable Interface에는 `compareTo()`라는 우선순위 비교 메서드가 있다.
+ 만약, A라는 멤버 변수를 기준으로 정렬하고자 한다면, `compareTo()`에서 두 객체의 A를 비교하도록 작성한다.
+ 즉, 정렬할 객체에서 implements Comparable하고 `compareTo()`를 오버라이딩

```java
// Patient.java
public class Patient implements Comparable<Patient>{
    ...
        
    @Override
	public int compareTo(Patient o) {
        // 오름차순. 내림차순은 반대로 뺄셈
		return this.age - o.getAge();
	}
}
```

```java
// MainTest.java
PriorityQueue<Patient> pq = new PriorityQueue<>();
pq.add(new Patient("김환자", 24));
pq.add(new Patient("나환자", 21));
pq.add(new Patient("당환자", 23));
pq.add(new Patient("박환자", 22));
pq.add(new Patient("이환자", 27));
pq.add(new Patient("정환자", 26));
pq.add(new Patient("장환자", 25));

Iterator<Patient> itr = pq.iterator();
while(itr.hasNext()) {
    System.out.println(itr.next());
    // 나환자 21
    // 박환자 22
    // 당환자 23
    // 김환자 24
    // 이환자 27
    // 정환자 26
    // 장환자 25
}
```

+ 왜 나이순대로 나오지 않는걸까?

  + Iterator는 객체를 순서대로 순회한다. 하지만, PriorityQueue는 그 구조 자체가 우선순위대로 오름차순이 되어있는 것이 아니고, Queue Interface의 메서드(`poll()`)를 사용해서 뽑아내야지만 순서대로 뽑혀나온다. 즉, Python의 heapq가 `heappop()` 시에는 차례대로 잘 나오지만 정작 heapq 자체를 출력해보면 순서가 맞지 않는 것과 같은 것이다.

  + `poll()`로 순서대로 꺼내보자면 다음과 같다.

    ```java
    PriorityQueue<Patient> pq = new PriorityQueue<>();
    pq.add(new Patient("김환자", 24));
    pq.add(new Patient("나환자", 21));
    pq.add(new Patient("당환자", 23));
    pq.add(new Patient("박환자", 22));
    pq.add(new Patient("이환자", 27));
    pq.add(new Patient("정환자", 26));
    pq.add(new Patient("장환자", 25));
    
    // 순서대로 뽑아오기 위해서는 Queue Interface의 poll()을 사용해야 함
    while(!pq.isEmpty()) {
        System.out.println(pq.poll());
        // 나환자 21
        // 박환자 22
        // 당환자 23
        // 김환자 24
        // 장환자 25
        // 정환자 26
        // 이환자 27
    }
    ```

<br/>

<br/>

### 2.7 Sort

> https://docs.oracle.com/javase/8/docs/api/ java.util > (Classes) Collections

PriorityQueue가 우선순위로 정렬을 하는 것이라면, 다른 Collection들은 기본적으로 들어온 순서대로 정렬이 되어 있을 것이다. 이 Collection들을 바로 정렬하는 방법은 `Collections.sort()`이다.

+ `static <T> void`

  + ```
    sort(List<T> list, Comparator<? super T> c)
    Sorts the specified list according to the order induced by the specified comparator.
    ```

+ `sort()`의 첫 번째 인자는 대상 Collection이고, 두 번째 인자는 Comparator Interface 구현체다. 이전의 Comparable처럼 비교를 처리하는 `compare()`가 있다.

+ ArrayList를 내 마음대로 정렬하려면 `Collections.sort()`를 사용하면 된다. 이때, 위에서 나온 Comparator Interface 구현체를 두 번째 인자로 넘겨줄 때 불편함이 생긴다. 따로 Comparator Interface를 implements하는 Class를 만들어야 하나? -> 아니다.
  + 우리에게는 **Anonymous Class**가 있다. 정렬하는 시점에 두 번째 인자에서 바로 정렬 기준을 준다.

```java
// MainTest.java
...
List<Patient> pList = new ArrayList<>();
pList.add(new Patient("김환자", 24));
pList.add(new Patient("나환자", 21));
pList.add(new Patient("당환자", 23));
pList.add(new Patient("박환자", 22));
pList.add(new Patient("이환자", 27));
pList.add(new Patient("정환자", 26));
pList.add(new Patient("장환자", 25));

// 두 번째 인자가 Anonymous Class
Collections.sort(pList, new Comparator<Patient>() {
    @Override
    public int compare(Patient o1, Patient o2) {
        // 오름차순
        return o1.getAge() - o2.getAge();
    }
});

System.out.println();
// for문을 통한 출력1 - 인덱싱 접근
// Collection의 길이는 .size()를 통해 구하고, ArrayList의 인덱싱 접근은 .get(index)를 통해 한다.
for (int i = 0; i < pList.size(); i++) {
    System.out.println(pList.get(i));
}

System.out.println();
// for문을 통한 출력2 - 요소 접근
for (Patient x: pList) {
    System.out.println(x);
}

System.out.println();
// iterator를 이용한 출력 - .hasNext()와 .next()를 사용
Iterator<Patient> itr = pList.iterator();
while (itr.hasNext()) {
    System.out.println(itr.next());
}
...
```

#### 2.7.1 Lambda

어떤 interface가 단 한 개만의 추상 메서드를 가졌을 때, 그 interface를 **functional interface**라고 부른다. Comparable이나 Coparator가 모두 이에 해당한다. Functional interface를 구현한 객체는 **lambda**식으로 표현할 수 있다.

```java
// lambda
Collections.sort(pList, (o1, o2) -> {
    return o1.getAge() - o2.getAge();
});
```
