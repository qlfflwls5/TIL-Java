# Exception_File IO

## 1. Exception

Java는 JVM 실행 시에 발생할 수 있는 다양한 예외 상황을 미리 정의해놓았다. **Error**(오류)와 **Excpetion**(예외)으로 나누어서 생각해볼 수 있다.

+ Error
  + System Level
  +  ex. StackOverFlowError
  + 발생시키는 코드, 환경을 해결해야 함
+ Exception
  + App Level
  + ex. IOException
  + 해결하거나 처리 방식 결정해야 함

### 1.1 구분

Exception은 **RuntimeException**과 **Other Exceptions**로 구분할 수 있다.

+ RuntimeException
  + Runtime시 발생되므로 예측할 수 없음
  + NullPointerException
  + ArrayIndexOutOfBoundsException
  + 이 예외는 '버그'이므로 반드시 해결해야 한다.

+ Other Exceptions
  + 예측 가능하므로(컴파일러가 잡아주므로) Code 작성 시 처리를 결정
  + IOException
  + SQLException
  + 처리 방식을 결정해준다.

<br/>

<br/>

### 1.2 처리

Exception의 처리는 3가지로 분류해 볼 수 있다.

+ try - catch - finally
  + 예외 발생 시 처리 코드
  + try - finally
  + try - catch
  + try - catch - finally
  + try 내에 적은 코드를 실행하다가 예외가 발생하면 이하 코드를 실행하지 않고 catch로 이동한다. catch에서 해당 예외를 인자로 받을 수 있다.
  + finally는 예외가 발생해도 항상 실행한다. 예외가 발생해도 자원을 반납해야 하는 부분의 코드를 주로 작성한다.

+ throws
  + 예외 발생 시 호출자에게 throw
  + 예) Main에서 C라는 클래스의 메서드 m()을 실행하고 해당 m에서 발생한 예외를 m에서 처리하지 않고, Main으로 전달하고 싶을 때, 메서드 옆에 throws를 붙인다. `m() throws Exception {}`
    + interface를 구현한 메서드라면 interface의 함수 선언부에도 throws Exception을 붙여줘야 한다.
  + 그럼 호출자가 다시 try - catch를 하던지 throws로 다시 위로 올리던지 하면 된다.
+ try - catch - throw
  + 예외 발생 시 처리 코드를 작성하되, 호출자에게도 새로운 예외를 전달

<br>

<br/>

<br/>

## 2. Exception - IO

Java의 많은 IO 관련 Class는 IO 관련 예외가 많이 발생하기 때문에 try-catch 구문을 자주 사용한다. 그리고 IO 작업은 반드시 작업이 끝날 때 close를 해주어야 하는데, 이를 예외가 발생해도 닫히도록 finally를 통해서 해주는 것이다. 하지만, finally 내에서도 바로 close()를 사용할 수는 없고 다시 try-catch 구문을 사용해야 한다.



이러한 문제를 해결하기 위해 Java 7에서 **AutoCloseable Interface**가 추가되었다. 이 interface를 implements한 Class에서는 자동으로 close되기 때문에 불편한 finally 구문을 쓰지 않아도 된다. `try( 코드 ) {}` 표현식이 추가되었다. java.lang.AutoCloseable

```java
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

public class ExceptionTest1{
	public static void main(String[] args) {
		// 기본 코드
		// FileInputStream fis = new FileInputStream("hello.txt");
		
		// 1. try - catch - finally
		FileInputStream fis1 = null;
		try {
			fis1 = new FileInputStream("hello.txt");
		}catch(FileNotFoundException e) {
			System.out.println("Exception : " + e.getMessage());
		}finally {
			try {
				fis1.close();
			}catch(Exception e) {
				e.printStackTrace();
			}
		}
		
		// 2. throws
//		public static void main(String[] args) throws FileNotFoundException {
//			FileInputStream fis = new FileInputStream("hello.txt")
//		}
		
		// 3. throw e
//		public static void main(String[] args) throws IOException {
//			FileInputStream fis = null;
//			try {
//				fis = new FileInputStream("hello.txt");
//			}catch(FileNotFoundException e) {
//				System.out.println("Exception : " + e.getMessage());
//				e.printStackTrace();
//				
//				throw new IOException(); // throw IOException 객체 to main()
//				
//			}finally {
//				try {
//					fis.close();
//				}catch(Exception e) {
//					e.printStackTrace();
//				}
//			}
//		}
		
		// 4. AutoCloseable
		try(FileInputStream fis2 = new FileInputStream("hello.txt");) {
			// fis2 code
		}catch(IOException e) {
			System.out.println("Exception : " + e.getMessage());
			e.printStackTrace();
		}
	}
}
```

+ Class 간 Exception 처리

```java
import java.io.IOException;

public class ExceptionTest2 {
	public static void main(String[] args) {
		
		ControllerEx ec = new ControllerEx();
		ec.m();
	}
}

class ControllerEx{
	public void m() {
		ServiceEx se = new ServiceEx();
        // 여기서 처리하기
		try {
			se.m();
		}catch (IOException e){
			e.printStackTrace();
		}
	}
}

class ServiceEx{
    // 위로 올리기
	public void m() throws IOException {
		DaoEx de = new DaoEx();
		de.m();
	}
}

class DaoEx{
    // 위로 올리기
	public void m() throws IOException {} 
}
```

<br/>

<br/>

<br/>

## 3. RuntimeException

RuntimeException 계열의 예외들은 프로그램 실행 시에 파악할 수 있기 때문에 컴파일 시에는 확인할 수 없으므로 예외처리를 하지 않아도 된다. 만약 이 예외들을 위의 try - catch 등으로 예외처리하면 물론 예외처리는 되겠지만 이 에러들은 반드시 처리해야 하는 예외들이기 때문에 예외처리를 하면 안되는 에러들이다.

```java
public class ExceptionTest3 {
	public static void main(String[] args) {
		
		ControllerRunEx ec = new ControllerRunEx();
		ec.m1();

		try {
			ec.m1();
		}catch(NullPointerException e) {
			;;
		}
	}

}

class ControllerRunEx{
	public void m1() throws NullPointerException {
		String s = null;
		s.length();		
	}
}
```

<br/>

<br/>

<br/>

## 4. Exception 상속 관계의 예외처리

Exception Class를 최상위 Class로 하여 다양한 하위 Class Exception이 존재한다. 만약 어떤 코드 블록 안에서 상위 Class의 예외와 하위 Class의 예외가 동시에 발생한다고 가정했을 때 처리 방법을 어떻게 해야 할까.

+ 하위 Class의 예외를 먼저 예외처리 해주어야 한다. 상위 Class의 예외를 먼저 처리하면 하위 Class의 예외처리에 도달할 수 없기 때문에 컴파일러에서 에러를 발생시킨다.
+ if문에서 가장 깊은 경우부터 처리를 해주는 것과 비슷한 로직이다.

```java
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

public class ExceptionTest4 {

	public static void main(String[] args) {
		
		// Not Runtime Exception
		FileInputStream fis = null;
		try {
			fis = new FileInputStream("hello.txt");			
		}catch(Exception e) {
			System.out.println("Handing Exception : " + e.getMessage());
		}catch(IOException e1) { // 이미 위에서 다뤄져 도달할 수 없다는 에러 발생
			System.out.println("Handing Exception : " + e1.getMessage());
		}catch(FileNotFoundException e2) { // 이미 위에서 다뤄져 도달할 수 없다는 에러 발생
			System.out.println("Handing Exception : " + e2.getMessage());
		}
		
		// Runtime Exception
		try {
			String s = null;
			s.length();
		}catch(RuntimeException e) {
			System.out.println("Handing Exception : " + e.getMessage());
		}catch(NullPointerException e) { // 이미 위에서 다뤄져 도달할 수 없다는 에러 발생
			System.out.println("Handing Exception : " + e.getMessage());
		}
	}

}
```

<br/>

<br/>

<br/>

## 5. Custom Exception

API에서 제공되는 Exception 외에 사용자가 직접 정의한 Exception을 만들 수 있다. Class를 상속하여 만들고, `super()`를 통해서 Exception Message를 지정할 수 있다.

+ Custom Exception같은 경우에는 throw 및 throws를 통해서 발생시킨다.

```java
public class CustomException extends Exception{
	public CustomException(String msg) {
		super(msg);
	}
}
```

```java
public class CustomExeptionTest {
	public static void main(String[] args) {
		MakeException me = new MakeException();
		try {
			me.customExceptionTest();
		} catch (CustomException e) {
			e.printStackTrace(); // CustomException: 커스텀 예외 발생 ...
		}
	}
}

class MakeException {
	public void customExceptionTest() throws CustomException{
	    throw new CustomException("커스텀 예외 발생");
	}
}
```

<br/>

<br/>

<br/>

## 6. Java IO

파일을 읽고 쓰는 것은 파일로부터 데이터를 JVM에 읽어들인 후에 다시 파일로 쓰는 과정으로, Java는 이를 **Stream**으로 이해하고 표현한다. 이렇게 데이터의 시작과 끝이 있고 양쪽 사이에서 데이터의 이동이 생기는데, 양쪽을 **Node**라고 부르며 이 데이터의 이동 흐름을 Stream이라고 한다. 키보드의 입력을 받는 것과 모니터에 출력하는 것도 Stream으로 간주하는 예다. 네트워크를 통한 데이터의 전송과 수신도 마찬가지다.



Stream은 다시 두 가지, 문자(Character)와 비문자(Binary)로 나뉜다. 문자를 처리하는 Stream은 데이터를 읽는 **Reader**와 데이터를 쓰는 **Writer**로 구분한다. 비문자는 데이터를 읽는 **InputStream**과 데이터를 쓰는 **OutputStream**으로 구분한다.



java.io 패키지에는 특별한 Class, **File** Class가 있다. 이는 물리적인 파일과 폴더를 모두 표현한다. OS마다 다른 경로 구분자로 `File.Separator`를 제공하여 OS 독립적인 코드를 구성할 수 있게 한다.

+ `exists()` : 파일이나 폴더가 존재하는가
+ `mkdir()` : 폴더를 만든다.
+ `createNewFile()` : 새 파일을 만든다.
+ `delete()` : 만들어진 파일을 삭제한다.

```java
import java.io.File;
import java.io.IOException;

public class FileTest {
	public static void main(String[] args) throws IOException {
		// c:\FileTest 폴더를 생성해준다.
		String dirName = "c:"+File.separator+"FileTest"+File.separator+"mydir";
		
		File file1 = new File(dirName);

		if( file1.exists() ) {
			System.out.println("Folder Exists!" );
			
		}else {
            // 이름은 file1이지만, 폴더를 생성하게 됨
			boolean success = file1.mkdir();
			if( success ) {
				System.out.println("Folder Created!" );
			}
		}
		
		File file2 = new File(dirName, "test2.txt");
		
        // 이미 같은 이름의 파일이 있다면 생성되지 않는다.
		file2.createNewFile();		
		//file2.delete();
	}
}

```

### 6.1 Buffered

BufferedInputStream, BufferedOutputStream, BufferedReader, BufferedWriter를 사용하면 Stream의 시간을 매우 단축할 수 있다.

+ `flush()`를 통해 Stream에 남아있는 데이터를 강제로 내보내고 버퍼를 비운다.

+ 시간 단축 확인

```java
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

public class BufferedPerformanceTest {
	public static void main(String[] args) {
		
		File src = new File("c:/Windows/explorer.exe");
		File target = new File("c:/Temp/copied_explorer.exe");
		
		try (FileInputStream fi = new FileInputStream(src);
			 FileOutputStream fo = new FileOutputStream(target);
			 BufferedInputStream bi = new BufferedInputStream(fi);
			 BufferedOutputStream bo = new BufferedOutputStream(fo);) {
			
			 copy("노드", fi, fo);
			 copy("보조", bi, bo);
			
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	public static void copy(String type, InputStream input, OutputStream output) throws IOException {
		long start = System.nanoTime();
		byte[] cart = new byte[1024];
		int read = -1;
		while ((read = input.read(cart)) > 0) {
			output.write(cart, 0, read);
		}
		long end = System.nanoTime();
		System.out.println(type + ", 소요 시간: " + (end - start) + "ns");
        // 노드, 소요 시간: 76830600ns
		// 보조, 소요 시간: 96600ns
	}
}
```
+ csv파일 생성하기
  + 엑셀에서 이 csv를 외부 데이터 가져오기로 가져온 후, 구분자를 지정해준 문자로(아래 코드는 `^`) 설정하여 불러오면 데이터가 잘 가져와질 것이다.

```java
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;
import java.util.ArrayList;
import java.util.List;

public class FileTest2 {
	public static void main(String[] args) {
		List<Patient> pList = new ArrayList<>();
		pList.add(new Patient("김환자", 24));
		pList.add(new Patient("나환자", 21));
		pList.add(new Patient("당환자", 23));
		pList.add(new Patient("박환자", 22));
		pList.add(new Patient("이환자", 27));
		pList.add(new Patient("정환자", 26));
		pList.add(new Patient("장환자", 25));
		
		String filePath = "c:" + File.separator + "FileTest";
		String fileName = "Patient.csv";
		
		try (
			BufferedWriter writer = new BufferedWriter(
					new OutputStreamWriter(
							new FileOutputStream(filePath + File.separator + fileName), "MS949"));
		){
			for(Patient p: pList) {
				writer.write(p.getName() + "^" + p.getAge());
				writer.newLine();
			}
            // 버퍼 비우기
            writer.flush();
            
		}catch(IOException e) {
			e.printStackTrace();
		}
	}
}

class Patient {
	private String name;
	private int age;
	
	public Patient(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
}
```

<br/>

<br/>

### 6.2 Object - 객체 직렬화

ObjectOutputStream과 ObjectInputStream을 사용해 객체 직렬화를 할 수 있다.

+ 객체 직렬화를 위해서 직렬화할 대상이 되는 객체의 Class에 **Serializable** Interface를 implements한다.
  + 이 Interface는 추상 메서드가 없다.
  + serialVersionUID는 객체 직렬화 과정에서 사용되는 고유의 id 개념이다.
  + **transient** 항목은 객체 직렬화에서 제외된다.

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.Serializable;

public class ObjectStreamTest {
		public static void main(String[] args) {
			File target = new File("c:" + File.separator + "FileTest" + File.separator + "objPerson.dat");
			Person person = new Person("홍길동", 20, "111111-2222222", "hong", "1234");
			try {
				//객체 저장
				ObjectOutputStream oos=new ObjectOutputStream(new FileOutputStream(target));
				oos.writeObject(person);
				oos.close();
				
				// 객체 로딩
				ObjectInputStream ois = new ObjectInputStream(new FileInputStream(target));
				Object readed = ois.readObject();
				if (readed != null && readed instanceof Person) {
					Person casted = (Person) readed;
					System.out.println(casted); // Person [name=홍길동, age=20, ssn=null]
				}
				ois.close();
				
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
}

class Person implements Serializable {
	private static final long serialVersionUID = 1L;
	private String name;
	private int age;
	private transient String ssn;
	
	public Person(String name, int age, String ssn, String userId, String userPass) {
		this.name = name;
		this.age = age;
		this.ssn = ssn;
	}
	
	@Override
	public String toString() {
		return "Person [name=" + name + ", age=" + age + ", ssn=" + ssn	+ "]";
	}
}
```

