# Thread & Network

## 1. 동시성(Concurrent vs Parallel)

Concurrent는 여러 작업이 동시에 처리된다는 개념이다. 정확히는 한 작업을 일정량 처리하고 다른 작업을 일정량 처리하고를 반복하며 두 작업이 동시에 진행되는 것처럼 보이게 한다. 브라우저 여러 개를 띄워서 동시에 여러 사이트를 접속할 수 있거나, 엑셀 문서를 여러 개 동시에 편집하는 것 등을 예로 들 수 있다.

이에 비해 Parallel은 어떤 하나의 작업을 쪼개서 여러 서브 작업으로 나누고, 이를 물리적으로 분리된 구조에서 실제로 동시에 처리해서 완성하는 개념이다. CPU의 다중 코어를 표현하는 것 등이 이에 해당한다.

### 1.1 관련 용어

| 용어          | 설명                                              |
| ------------- | ------------------------------------------------- |
| 프로세스      | 개별적으로 동작하는 프로그램                      |
| 스레드        | 프로세스를 구성하는 독립적인 세부 실행 단위(Unit) |
| 멀티 프로세스 | 여러 개의 프로세스를 동시에 수행                  |
| 멀티 스레드   | 한 프로세스에서 여러 개의 스레드를 동시에 수행    |

<br/>

<br/>

<br/>

## 2. Thread 사용하기

Java에서 동시에 어떤 작업을 여러 개 실행하기 위해서는 그 작업을 Thread로 만들어서 실행해야 한다. 만약 A라는 작업을 여러 개 만들어서 동시에 실행한다면 A라는 작업을 Thread로 만들어서 실행하면 된다. 5개의 작업을 Thread로 만들어서 Cocurrent하게 실행하는 것과 그렇지 않은 경우를 비교하자면 다음과 같다.

```
Thread를 사용하지 않는 경우
A -> A -> A -> A -> A

Thread를 사용하는 경우
A ->
A ->
A ->
A ->
A ->
```

### 2.1 Thread 없이 실행하기

+ 코드의 흐름에 따라 순서대로 출력된다.

```java
public class ThreadTest {
	public static void main(String[] args) {
		for(int i = 0; i < 5; i++) {
			System.out.println("#" + i + " is started.");
			for(int j = 0; j < 10000; j++) {
				int k = j * 100;
			}
            
			System.out.println(i);
		}
	}
}


// #0 is started.
// 0
// #1 is started.
// 1
// #2 is started.
// 2
// #3 is started.
// 3
// #4 is started.
// 4
```

<br/>

<br/>

### 2.2 Thread로 실행하기

Java에서 Thread를 만드는 방법은 2가지가 있다.

#### 2.2.1 첫 번째 방법

1. Runnable Interface를 implements하는 Class를 만들어 `run()` 메서드를 구현하고, 그 안에 작업 코드를 넣는다.
2. 위에서 만든 Class를 이용하여 Thread를 만들 수 있다. Thread 객체를 만들 때, Thread 객체의 생성자 인자로 위에서 만든 Class의 객체를 전달한다.
3. Thread 객체를 실행할 때는 `start()`를 호출한다.

```java
// Runnable Interface를 implements하는 Class
public class ThreadRunnable implements Runnable{
	int num;
	
	public ThreadRunnable(int num) {
		this.num = num;
	}

	@Override
	public void run() {
		System.out.println("#" + num + " is started.");
		for(int i = 0; i < 10000; i++) {
			int j = i*100;
		}
		
		System.out.println(num);
	}
}
```

```java
// Thread를 만들 Class
public class ThreadTest {
	public static void main(String[] args) {
		// Thread 첫 번째 방법
		for(int i = 0 ; i < 5; i++) {
			ThreadRunnable tr = new ThreadRunnable(i);
			Thread thread = new Thread(tr);
			thread.start();
		}
	}
}


// #0 is started.
// #1 is started.
// #2 is started.
// #3 is started.
// 1
// #4 is started.
// 3
// 0
// 2
// 4
```

+ 이 출력은 매번 실행할 때 마다 다르게 나온다.

<br/>

#### 2.2.2 두 번째 방법

Thread Class를 extends 하는 방법이다.

1. Thread를 상속받는 Class를 만들고 `run()` 메서드를 overriding 한다.
2. `run()` 안에 작업 내용을 넣는다.
3. 이 경우 Class 자체가 Thread이다.

```java
// Thread를 상속받는 Class
public class ThreadExtends extends Thread{
	int num;
	
	public ThreadExtends(int num) {
		this.num = num;
	}
	
	@Override
	public void run() {
		System.out.println("#" + num + " is started.");
		for(int i = 0; i < 10000; i++) {
			int j = i*100;
		}
		
		System.out.println(num);
	}
}
```

```java
// Thread를 만들 Class
public class ThreadTest {
	public static void main(String[] args) {
		// Thread 두 번째 방법
		for(int i = 0 ; i < 5; i++) {
			ThreadExtends thread = new ThreadExtends(i);
			thread.start();
		}
	}
}


// #3 is started.
// #0 is started.
// #4 is started.
// #2 is started.
// #1 is started.
// 3
// 0
// 4
// 2
// 1
```

<br/>

<br/>

### 2.3 Thread 실행 관련

`main()`이 실행되면 Main Thread가 동작한다. 다른 Thread가 실행되지 않으면 Main Thread 하나로 프로그램이 실행된다.

여기서, 또 다른 Thread가 실행되면 Main Thread와 다른 Thread가 동시에 실행되게 된다.

+ `mian()`은 `start()`시킨 모든 Thread들이 종료된 후에야 종료될까?
  + 아니다. `main()`이 종료되어도 실행중인 다른 Thread가 모두 종료되어야만 JVM의 실행이 종료된다.
+ Thread 객체를 한 개만 만들고 이 Thread를 두 번 `start()`하면 어떻게 될까?
  + 오류가 발생한다. Thread는 내부적으로 자신의 상태를 관리하는데, 이 상태의 오류로 `IllegalThreadStartException`을 발생시키고 Thread가 종료된다.

+ Thread 안에서 또 다른 Thread를 실행시킬 수 있을까?
  + 가능하다. 단, 공유 자원(변수, 자료구조) 등에 대한 주의가 필요하다.

<br/>

<br/>

<br/>

## 3. Thread 일시정지(sleep(), join())

### 3.1 Thread.sleep()

Thread.sleep() 메서드는 static 메서드이다. 호출할 때 ms 단위의 시간을 인자로 전달하면 Thread.sleep()을 호출한 Thread는 해당 시간만큼 실행을 중지했다가 다시 실행이 된다.

```java
// Thread를 상속받는 작업 Class
public class ThreadSleep extends Thread{
	int num;
	
	public ThreadSleep(int num) {
		this.num = num;
	}
	
	@Override
	public void run() {
		System.out.println("#" + num + " is started.");
		for(int i = 0; i < 10000; i++) {
            // 짝수인 경우 Thread를 0.1초 sleep
			if (i == 5000 && num % 2 == 0 ) {
				try {
					Thread.sleep(100);
				} catch(InterruptedException e) {
					e.printStackTrace();
				}
			}
		}
		System.out.println(num);
	}
}
```

```java
// Thread를 만들 Class
public class ThreadTest {
	public static void main(String[] args) {
		// Thread 일시정지
		for(int i = 0 ; i < 5; i++) {
			ThreadSleep thread = new ThreadSleep(i);
			thread.start();
		}
	}
}


// 시작은 뒤죽박죽이지만 결과는 홀수들이 먼저 나오는 것을 볼 수 있다. 물론 이것도 짝수 Thread가 잠들다가 다른 홀수 Thread 실행 이전에 깨어나면 중간에 섞일 수 있다.
// #0 is started.
// #4 is started.
// #3 is started.
// #1 is started.
// #2 is started.
// 3
// 1
// 2
// 0
// 4
```

<br/>

<br/>

### 3.2 Thread.join()

Thread 객체가 특정 Thread 객체의 종료까지 수행하던 작업을 멈추고 특정 Thread 객체가 종료되면 다시 작업을 수행하도록 할 수 있다.

만약 A  Thread 객체 a의 종료 시까지 대기하려면 자신 Thread의 실행 코드에 `a.join()`형태로 메서드를 호출하면 된다.

```java
public class ThreadTest {
	public static void main(String[] args) {
		System.out.println("Main Thread Start");

		// Thread 일시정지 join
		ThreadExtends thread = new ThreadExtends(2021);
		thread.start();
		try {
			thread.join();
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
        
		System.out.println("Main Thread End");
	}
}


// Main Thread Start
// #2021 is started.
// 2021
// Main Thread End
```

<br/>

<br/>

### 3.3 일시정지 중간에 깨우기(Interrupt)

이 내용은 Thread의 '상태'를 알고 나서 진행한다.

<br/>

<br/>

<br/>

## 4. Thread - 상태

Thread 객체는 `start()` 메서드가 호출되면 **바로 실행되는 것이 아니다**. 모든 Thread는 기본적으로 서로 경쟁한다. 

전체 Thread는 실행에서 종료까지 다양한 상태로 존재하게 되고, JVM은 이 상태를 이용해 전체 Thread의 실행을 제어한다.

+ Thread의 상태 그래프

![JAVA 쓰레드란(Thread) ? - JAVA에서 멀티쓰레드 사용하기 | 기록하는 개발자](https://honbabzone.com/assets/images/post/java/thread-status.png)

+ 취소선이 그어져 있는 메서드는 Thread의 안정성을 해칠 위험이 있어 사용이 권장되지 않는 메서드들이다.
+ 실행 대기(Runnable) : 경쟁 대기실
+ 일시 정지 : 실행 중인 Thread가 `sleep()`이나 `join()`을 통해 일시 정지 상태로 들어간다. 이 상태가 끝나면(시간 종료, 메서드) 바로 다시 실행이 되는 것이 아니라 실행 대기로 들어간다.
+ 따라서, `sleep()`에 지정한 시간만큼 정확히 멈추고 다시 실행하지는 않는다.

### 4.1 interrupt()

일시 정지 상태에 있는 특정 Thread 객체를 방해하여 다시 실행 대기 상태로 이동시킬 수 있다. 특정 Thread 객체의 `interrupt()`를 호출하면 된다.

```java
// Thread Class
public class ThreadInterrupt extends Thread{
	int num;
	
	public ThreadInterrupt(int num) {
		this.num = num;
	}
	
	@Override
	public void run() {
		try{
			System.out.println("Thread try start");
			Thread.sleep(5000);
			System.out.println("Thread try end");
		} catch(InterruptedException e) {
			System.out.println("Thread interrupted raised");
		}
		System.out.println(num);
	}
}
```

```java
// Main Class
public class ThreadInterruptTest {
	public static void main(String[] args) {
		ThreadInterrupt thread = new ThreadInterrupt(2021);
		thread.start();
		thread.interrupt();
		
		try {
			Thread.sleep(3000);
		} catch(InterruptedException e) {
			e.printStackTrace();
		}
		
		System.out.println("Main Thread end");
	}
}


// Thread try start
// Thread interrupted raised
// 2021

// 3초

// Main Thread end

// interrupt가 안되었다면
// Thread try start

// 3초

// Main Thread end

// 2초

// Thread try end
// 2021
```

