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

