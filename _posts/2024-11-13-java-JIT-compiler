---
layout: post
title: Java JIT Compiler
categories: ["java", "jvm"]
---

### JIT(Just-In-Time) Compiler - Hotspot
이름 그대로 프로그램이 실행 중인 상태에서 컴파일을 진행한다. JVM 구동된 후 프로그램이 실행되면서 발생하는 동적 정보를 바탕으로 최적화된 컴파일을 수행한다.
1. JVM 구동 시 초기에는 interpreter 모드로 실행되며 바이트코드를 한 줄씩 해석하고 실행
2. 특정 메소드의 호출 빈도, 조건 분기 통계, 루프 실행 횟수 등의 실행 패턴을 수집하여 최적화를 적용할 필요가 있는지 판단
3. 최적화가 필요하다고 판단이 되면(임계치에 도달하면) JIT 컴파일러는 해당 코드를 최적화하여 컴파일 진행
4. 컴파일된 네이티브 코드는 JVM의 코드 캐시에 저장

### JIT internal
- JVM이 실행 중 특정 메소드나 루프가 일정 횟수 이상 호출되거나 반복될 경우 이를 hotspot으로 인식
- C1 컴파일러가 기본적인 최적화를 적용했음에도 여전히 자주 호출되고 있으면 C2 컴파일러로 넘겨 더 고도화된 최적화 진행
- C2 컴파일러는 최적화 정도는 높지만 컴파일 시간이 더 오래 걸리기 때문에 충분히 자주 호출되는 메소드에 대해서만 적용

### C1 Complier
초기 단계의 성능 향상을 위해 주로 사용되는 컴파일러로 자주 호출되는 메소드나 루프를 최적화한다.
- 인라인 최적화 : 자주 호출되는 메소드를 호출하는 대신 그 메소드의 실제 코드를 호출 위치에 복사해서 넣는 방식
    ```java
    // before optimization
    public int add(int a, int b) {
        return a + b;
    }

    public int calculate() {
        int result = add(5, 10);
        return result;
    }

    // after optimization
    public int calculate() {
        int result = 5 + 10;
        return result;
    }
    ```
- 간단한 루프 최적화 : 반복문 내에서 반복적으로 수행되는 연산을 최적화하여 성능을 향상시키는 여러 기법
    - Loop Invariant Code Motion : 반복문 안에서 매번 반복되지 않아도 되는 연산을 루프 밖으로 이동시키는 기법
        ```java
        // before optimization
        public int calucateSum(int[] arr) {
            int sum = 0;
            for(int i=0; i<arr.length; i++) {
                sum += arr[i];
            }
            return sum;
        }

        // after optimization
        public int calucateSum(int[] arr) {
            int length = arr.length;
            int sum = 0;
            for(int i=0; i<length; i++) {
                sum += arr[i];
            }
            return sum;
        }
        ```
    - Loop Fusion : 서로 다른 반복문에서 수행할 수 있는 연산을 하나의 반복문으로 합쳐 반복 횟수를 줄이는 기법
        ```java
        // before optimization
        for(int i=0; i<n; i++) {
            sum += arr1[i]
        }
        for(int i=0; i<n; i++) {
            sum += arr2[i]
        }

        // after optimization
        for(int i=0; i<n; i++) {
            sum += arr1[i]
            sum += arr2[i]
        }
        ```
- 초기 프로파일링 : 코드가 실행될 때 마다 특정 조건의 빈도나 메소드 호출 패턴을 수집
    ```java
    public int calculate(int x) {
        if (x > 0) {
            return positiveCalculation(x);
        }
        return negativeCalculation(x);
    }

    public int positiveCalculation(int x) {
        return x * 10;
    }

    public int negativeCalculation(int x) {
        return x * -10;
    }
    ```
    코드 실행 시 x가 대체로 양수라면 JIT 컴파일러는 calculate 메소드가 대체로 positiveCalculation 메소드를 호출하는 패턴을 알 수 있음

### C2 Complier
C1 컴파일러가 특정 코드 패턴을 수집하면, C2 컴파일러는 이를 기반으로 심층적인 최적화를 수행한다. 
- Loop Unrolling : 반복 횟수를 줄이기 위해 반복문 내의 연산을 여러 번 수행하는 형태로 바꾸는 기법
    ```java
    // before optimization
    for(int i=0; i<100; i++) {
        sum += arr[i];
    }

    // after optimization
    for(int i=0; i<100; i+=2) {
        sum += arr[i];
        sum += arr[i+1];
    }
    ```
- vectorization : 반복문내의 연산을 병렬로 처리할 수 있도록 변환하는 기법
    ```java
    // before optimization
    public void addArrays(int[] a, int[] b, int[] c) {
        for (int i=0; i<a.length; i++) {
            c[i] = a[i] + b[i];
        }
    }

    // after optimization
    public void addArrays(int[] a, int[] b, int[] c) {
        int i=0;

        for(; i<a.length - 4; i +=4) {
            c[i] = a[i] + b[i];
            c[i + 1] = a[i + 1] + b[i + 1];
            c[i + 2] = a[i + 2] + b[i + 2];
            c[i + 3] = a[i + 3] + b[i + 3];
        }

        for (; i<a.length; i++) {
            c[i] = a[i] + b[i];
        }
    }
    ```
- 범위 체크 제거 : 반복문내에서 인덱스를 반복해서 체크하는 부분을 제거하여 불필요한 연산 최소화
- 가상 메소드 호출 최적화 : 가상 메소드 호출을 최소화하여 동적 바인딩의 비용을 줄이기위한 최적화 기법
    - 가상 메소드 호출 : 메소드가 오버라이드 된 경우 호출할 메소드를 런타임에 결정하기 때문에 JVM은 동적 바인딩을 통해 실제 구현된 메소드를 찾아야하기 때문에 오버헤드 발생
    - 정적 메소드 호출 : 컴파일 시점에 메소드가 정해져 있는 경우로 정적 바인딩을 사용하여 호출
    ```java
    public interface Animal {
        void sound();
    }

    // before optimization
    public class Dog implements Animal {
        @Override
        public void sound() {
            System.out.println("Woof");
        }

        public static void main(String[] args) {
            Animal animal = new Dog();
            animal.sound();
        }
    }

    // after optimization
    public class Dog implements Animal {
        @Override
        public void sound() {
            System.out.println("Woof");
        }

        public static void main(String[] args) {
            Dog dog = new Dog();
            dog.sound();
        }
    }
    ```
### References
- [JVM warm up / if(kakao)dev2022](https://www.youtube.com/watch?v=utjn-cDSiog&list=PLyraqdoIVJhmCIlhXAYjZwqwxT5Ih1kBG&index=4)
- chatGPT