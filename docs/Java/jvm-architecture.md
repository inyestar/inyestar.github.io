---
layout: default
title: JVM Architecture
parent: Java
---
# JVM

| 날짜 | 2021년 3월 03일 |
|:----------|:-------------------------------------|
| 출처 | https://www.freecodecamp.org/news/jvm-tutorial-java-virtual-machine-architecture-explained-for-beginners/ |

## * Hypervisor

- 가상 머신을 생성하고 구동하는 소프트웨어로 Virtual Machine Monitor라고도 불린다.
- 물리 하드웨어를 호스트 머신, VM을 게스트 머신이라고 칭한다.
- CPU, 메모리, 스토리지 등의 호스트 머신의 리소스를 관리한다.
- 메모리 관리 프로그램, 프로세스 스케줄러, I/O 스택, 기기 드라이버, 보안 관리 프로그램, 네트워크 스택 등을 기본적으로 제공 해야 한다.
- 호스트 머신의 리소스를 분배하는 방식에 따라 아래 유형으로 나뉜다.
    - 유형1
    네이티브 하이퍼바이저라고 불리며 호스트의 하드웨어에서 직접 구동되어 게스트 운영체제를 관리하는 방식으로 주로 데이터 센터에서 사용된다. (vSphere, Xen)
    - 유형2
    호스트 하이퍼바이저라고 불리며 기존의 운영체제에서 스프트웨어나 애플리케이션 레이어로 구동되며 VM에 필요한 리소스는 호스트 운영 체제에 따라 예약된 후 실행된다.(Workstation, VirtualBox)
```
자바는 컴파일 언어인가 인터프린터 언어인가?
자바는 작성된 코드를 바이트 코드로 컴파일하고, JVM이 바이트코드를 인터프리터한다.
```
## Class Loader

- 컴파일 된 클래스가 사용될 때 메인 메모리에 적재 시키는 역할
- 첫 번째로 메모리에 적재되는 클래스는 main() 메소드를 포함하고 있는 클래스
- Loading : 특정 이름을 가진 인터페이스나 클래스의 바이트 코드를 가져온다.
    - Boostrap 클래스 로더
    루트 클래스 로더로 rt.jar 파일에 있는 java.lang/net/util/io와 같은 스탠다드 패키지나 $JAVA_HOME/jre/lib 디렉토리에 있는 핵심 라이브러리를 로딩한다.

        ```java
        System.out.println(ArrayList.class.getClassLoader());
        >> null 
        // (!) Bootstrap 클래스 로더는 native 코드로 작성되어서 null로 나옴
        ```

    - Extension 클래스 로더
    Bootstrap 클래스로더의 하위 클래스로 $JAVA_HOME/jre/lib/ext 디렉토리에 있는 스탠다드 라이브러리를 로딩한다.

        ```java
        System.out.println(MyClass.class.getClassLoader());
        >> sun.misc.Launcher$ExtClassLoader@주소
        ```

    - Application 클래스 로더
    지막 클래스 로더이자 Extension 클래스 로더의 하위 클래스로 classpath에 위치한 파일을 로딩한다. classpath는 기본적으로 애플리케이션의 현재 디렉토리로 설정되지만 -classpath나 -cp 옵션으로 수정할 수 있다.

        ```java
        System.out.println(Logging.class.getClassLoader());
        >> sun.misc.Launcher$AppClassLoader@주소
        ```

    - JVM은 클래스를 메모리에 적재시킬 때 ClassLoader.loadClass()란 메소드를 사용한다.
    - 부모 클래스 로더가 요청된 클래스를 찾지 못한 경우 자식 클래스 로더에게 행위를 위임하고, 마지막 자식 클래스 로더마저 클래스를 로딩하지 못했을 경우 NoClassDefFoundError나 ClassNotFoundException을 던진다.
- Linking : 클래스가 메모리에 적재된 후 링킹 프로세스를 거치게 된다. 클래스나 인터페이스를 연결하는 작업에는 프로그램의 다른 요소들과 종송석을 함께 결합하는 작업도 포함되어 있다.
    - Verification
    일련의 제약이나 규칙을 통해 클래스 파일의 구조적인 정확도를 확인한다. verification이 실패할 경우 VerifyException을 던진다.
         ```
         Java 8이 설치된 시스템에서  Java 11로 컴파일된 코드를 구동할 경우 verification은 실패한다.
         ```
    - Preparation
    클래스나 인터페이스의 static 필드을 위한 메모리 공간을 할당하고 기본 값으로 초기화한다.
        ```java
        private static final boolean enabled = true; 
        ```
        위 와 같은 선언문이 클래스에 있을 경우, JVM은 enabled 변수를 위한 메모리를 할당하고 변수의 기본값으로 boolean의 기본값인 false를 세팅한다. 
        
    - Resolution
    Symbolic 참조를 Runtime Constant Pool에 있는 직접 참조로 대체한다.
        ```
        다른 클래스 혹은 다른 클래스에 있는 상수 변수에 대한 참조가 있을 경우 이 단계에서 그들의 실제 참조로 변경된다.
        ```
- Initialization : 클래스나 인터페이스의 초기화 메소드를 실행한다. 클래스의 생성자를 호출하거나 static 블락을 실행하거나 모든 상수 변수에 값을 할당하는 방식으로 작업이 수행될 수 있다. 클래스 로딩의 가장 마지막 단계이다.
    ```java
    private static final boolean enabled = true; 
    ```
    Preparation 단계에서 enabled 변수는 false로 값이 세팅되었다. 이후 Initialization 단계에서 enabled 변수에 실제 값인 true가 세팅된다.
    JVM은 멀티 쓰레딩을 지원하기 때문에 여러 쓰레드가 동시에 같은 클래스를 초기화하려고 할 수도 있으므로 개발자는 이 부분을 염두해야 한다.
    
## Runtime Data Area

- Method Area
    - Runtime constant pool, 필드, 메소드 데이터, 메소드와 생성자에 대한 코드와 같은 각 클래스 레벨의 데이터가 저장되는 곳
    - 저장된 데이터는 클래스 혹은 객체 초기화에 사용됨
    - 프로그램 구동 시 메소드 영역에 사용 가능한 메모리가 충분하지 않을 경우 JVM은 OutOfMemoryError를 던진다.
    - 아래 코드에서 필드 레벨의 데이터인 name, score, 생성자는 메소드 영역에 저장된다.

        ```java
        public class Student {

        	private String name;
        	private int score;

        	public Student(String name, int score) {
        		this.name = name;
        		this.score = score;
        	}
        }
        ```

    - 가상 머신이 시작할 때 메소드 영역은 생성되며 JVM 당 하나의 메소드 영역만이 존재한다.
- Heap Area
    - 모든 객체와 그들이 인스턴스 변수들은 힙 영역에 저장된다.
    - 모든 클래스 인스턴스 및 배열에 대한 메모리가 할당되는 런타임 데이터 영역
    - 아래 코드는 Student 인스턴스가 생성되어 힙 영역에 적재되는 것을 나타내고 있다.

        ```java
        Student student = new Student('sam', 100);
        ```

    - 가상 머신이 시작될 때 힙 영역은 생성되며 JVM 당 하나의 힙 영역만이 존재한다. 메소드 영역와 힙 영역은 다중 쓰레드에서 공유하는 메모리 공간이므로 이곳에 저장되는 데이터는 thread safe가 보장되지 않는다.

- Stack Area
    - JVM에서 새로운 쓰레드가 생성되면 새로운 런타임 스택도 동시에 생성된다. 지역 변수나 메소드 호출과 부분적인 리턴 값들이 스택 영역에 저장된다.
    - 쓰레드안에서 처리해야할 작업들이 스택의 크기보다 클 경우 JVM은 StackOverflowError를 던진다.
    - 스택 프레임 : 각 메소드 호출마다 스택 메모리에 만들어지는 entry로 메소드 호출이 완료되면 스택 프레임은 제거된다.
        - Local Variables :  각 프레임은 지역 변수로 불리우는 변수들의 배열을 포함하고 있다. 모든 지역 변수와 그들의 값이 여기에 저장된다. 컴파일 타임에 이 배열의 길이가 결정된다.
        - Operand Stack : 각 프레임은 LIFO 스택을 갖고 있다. 중간 작업을 수행하기 위한 런타임 작업공간 역할을 한다. 이 스택의 최대 깊이는 컴파일 타임에 결정된다.
        - Frame Data : 메소드에 해당하는 모든 부호는 이곳에 저장된다. 또한 익셉션에 대한 catch 블락도 이곳에 저장된다.
        - 아래 코드의 students나 score과 같은 지역 변수들은 Local variables 배열에 위치한다. Operand Stack은 뺄셈에 필요한 연산자와 변수들을 갖고 있다.
        localVariables = [student, score]
        OperandStack = [push _sub, push 100]

        ```java
        int calcuate(Student student) {
        	...
        	int score = student.getScore();
        	return minus(score);
        }

        int minus(int score) {
        	return score - 100;
        }
        ```

## Program Counter (PC) Registers

- JVM은 동시에 여러 쓰레드를 지원한다. 각 쓰레드는 현재 JVM의 insturction을 수행하는 주소를 갖고 있는 PC 레지스터를 보유한다.
- Instruction이 한 번 수행되고 나면 PC 레지스터는 다음 instruction으로 업데이트 된다.

## Native Method Stacks

- JVM은 Native 메소드를 지원하는 스택을 포함하고 있다.
- Native 메소드는 자바가 아닌 C와 C++로 작성되었다.
- 새로운 쓰레드가 생성될 때 마다 Native 메소드 스택 또한 따로 할당된다.

## Execute Engine

- 바이트코드가 메인 메모리에 적재되고 상세 정보 등이 런타임 데이터 영역에서 사용 가능해지면 다음 단계인 프로그램 구동 순서로 넘어간다.
- Excecute 엔진은 각 클래스에 존재하는 코드를 실행하는 역할을 담당한다.
- 프로그램 실행 전 바이트코드는 기계 명령어로 변환되어야 한다.
- Interpreter
    - 바이트코드 명령어를 라인별로 읽고 실행한다.
    - 라인 단위로 실행하기 때문에 상대적으로 느린 속도를 가지고 있다.
    - 메소드가 여러 번 호출될 때마다 새로운 해석(읽고 실행)이 요구된다.
- JIT Compiler
    - 인터프리터의 단점을 극복하기위해 사용된다.
    - Execution 엔진은 처음에는 바이트코드를 실행하기 위해 인터프리터를 사용하는데 이 때 반복적인 코드가 발견될 경우 JIT 컴파일러를 사용한다.
    - JIT 컴파일러는 전체 바이트코드를 컴파일하여 Native 머신 코드로 변환한다. 반복적인 메소드 호출에 변환된 코드를 사용하여 시스템의 퍼포먼스를 높힌다.
    - Intermediate Code Generator : 중간 코드를 생성한다.
    - Code Optimizer : 더 나은 퍼포먼스를 위해 중간 코드를 최적화 한다.
    - Target Code Generator : 중간 코드를 Native 머신 코드로 변환한다.
    - Profiler : 반복적으로 수행되는 코드(HotSpot)를 찾는다.
- Interpreter와 JIT 컴파일러 예제
    1. 인터프리터는 루프문안에서 각 반복마다 sum 값을 메모리에서 가져다가 i의 값을 더하고 다시 메모리에 쓴다. 루프에 진입할 때마다 메모리에 접근하고 있어서 상당히 비싼 작업이다.
    2. JIT 컴파일러가 해당 코드를 HotSpot으로 인식하여 최적화를 수행한다. 해당 쓰레드의 PC 레지스터에 sum의 로컬 복사본을 저장한다. 그리고 루프안에서 i의 값을 sum에 더한다. 루프문이 종료되면, 메모리에 최종 값을 다시 쓴다.

    ```java
    int sum = 1;
    for(int i=1; i<= 10; i++) {
    	sum += i;
    }
    System.out.println(sum);
    ```

    JIT 컴파일러는 코드를 컴파일 할 때 인터프리터가 라인 단위로 해석하는 것 보다 더 많은 시간을 필요로 한다. 한 번만 실행하는 프로그램을 위해서는 인터프리터를 사용하는 게 더 나을 수도 있다.

- Garbage Collector
    - 힙 영역의 더이상 참조되지 않은 객체들을 모아서 제거한다.
    - 사용되지 않는 런타임 메모리를 자동으로 회수하여 여유 공간을 만드는 프로세스
    - Mark : GC가 메모리에서 참조되지 않는 객체를 식별한다.
    - Sweep : GC가 Mark 단계에서 식별된 객체들을 제거한다.
    - Garbage Collection은 JVM에 의해 주기적으로 수행된다.
    - System.gc()를 통해 호출할 수 있지만 반드시 실행된다는 보장은 없다.
    - Serial GC : 가장 간단한 GC로 단일 스레드로 운영되는 작은 애플리케이션 용으로 제작되었다. Garbage collection 용으로 한 개의 쓰레드를 사용한다. 해당 GC가 수행되면 "stop the world" 이벤트가 발생하는데 이 이벤트로 인해 애플리케이션이 잠시 정지하게 된다. Serial GC를 쓰기위한 JVM의 옵션은 -XX:+UseSerialGC 이다.
    - Parallel GC : JVM의 기본 GC로 Throughput Collector라고도 알려져 있다. Gargabe collection 용으로 여러 개의 쓰레드를 사용하지만 마찬가지로 수행 시 애플리케이션을 멈춘다. 해당 GC용 JVM 옵션은 XX:+UseParallelGC 이다.
    - Garbage Frist (G1) GC : G1 GC는 큰 힙 메모리를 사용하는 (4GB이상) 멀티 쓰레드를 사용하는 애플리케이션을 위해 제작되었다. Heap 영역을 동일한 사이즈로 나누고 해당 구역을 감시하기위한 여러개의 쓰레드를 사용한다. G1 GC는 가장 gargabe한 구역을 파악하여 해당 구역부터 Gargabe collection을 수행한다. JVM 옵션은 -XX:+UseG1GC
    - CMS GC : 자바 9부터 deprecated 되었고 14부터 완전히 제거된 GC로 더 짧은 stop the world 를 선호할 경우 사용되도록 고안되었다.

## Java Native Interface (JNI)

- 하드위에어 상호작용하거나 메모리 관리를 극대화하기위해 Native 코드 사용이 필요할 때 JNI를 통해서 Native 코드를 실행할 수 있다.
- C와 C++과 같은 다른 언어의 패키지를 지원하는 다리 역할을 한다.
- native 키워드를 사용하여 특정 메소드의 실제 구현부분은 native 라이브러리에서 제공되고 있음을 나타낼 수 있다.
- 사용을 위해서는 System.loadLibrary()를 호출하여 공유된 native 라이브러리를 메모리에 적재하고 해당 함수가 자바에서 사용가능하도록 만들어 주어야 한다.

## Native Method Libraries

- C, C++, 어셈블리같은 다른 프로그램 언어로 쓰여진 라이브러리다.
- 일반적으로 *.dll나 *.so 같은 확장자로 표현된다.
- JNI을 통해 로딩된다.
