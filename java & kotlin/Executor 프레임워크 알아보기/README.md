# Executor 프레임워크 알아보기

초기 자바에서는 Thread 클래스를 직접 사용하여 스레드를 생성하고 관리했다. 하지만 해당 방식은 너무 로우했고, 좀 더 편리하게 멀티스레딩을 다루기 위해 Java5에서부터 `java.util.concurrent` 패키지가 도입되었다.

해당 패키지에는 Executor 프레임워크가 제공되었는데 이 프레임워크는 스레드 생성을 추상화하고, 비동기 작업의 실행을 단순화하여 어려운 멀티스레드 프로그래밍을 좀 더 간편하게 구현할 수 있도록 도와주었다.

Executor, ExecutorService, ThreadPoolExecutor는 이 프레임워크의 핵심 구성요소로 이에 대해 알아보자.

# Executor

```java
public interface Executor {
    void execute(Runnable command);
}
```

- Executor는 Runnable 작업을 받아 이를 비동기적으로 실행해준다.

# ExecutorService

```java
public interface ExecutorService extends Executor, AutoCloseable {
	...다양한 메서드...
}
```

- ExecutorService는 Executor 인터페이스를 확장한 인터페이스다.
- Executor 보다 훨씬 많은 기능을 제공한다.
    - 작업 제출 (`submit()`) → Runnable, Callable 모두 인자로 받음
    - 병렬 실행 및 결과 취합 (`invokeAll()`, `invokeAny()`)
    - 스레드 풀 생명주기 관리 및 종료 (`shutdown()`, `shutdownNow()`)
    - 등등…

# ThreadPoolExecutor

- ThreadPoolExecutor 클래스는 ExecutorService의 구현체 중 꼭 기억해야하는 클래스이다.
- 이 클래스 생성자의 인자는 아래와 같다.

```java
ThreadPoolExecutor(
    int corePoolSize,           // 스레드 풀의 기본 크기
    int maximumPoolSize,        // 스레드 풀의 최대 크기
    long keepAliveTime,         // 유지 시간
    TimeUnit unit,              // 유지 시간의 단위 (초, 밀리초 등)
    BlockingQueue<Runnable> workQueue, // 작업을 대기시키는 큐
    RejectedExecutionHandler handler   // 작업이 거부될 때의 처리 방식
)
```

각각을 알아보자

1. corePoolSize: 기본적으로 유지할 스레드의 수
    - 스레드 풀이 항상 유지할 최소한의 스레드 개수
    - 작업이 없더라도 corePoolSize 만큼의 스레드는 종료되지 않고 계속 대기 상태를 유지한다.
    - 다만 초기에 애플리케이션을 시작할때는 스레드가 없는 상태로 시작하는게 기본이기 때문에 스레드풀에 스레드를 채우고 애플리케이션을 시작하기 위해서는 `ThreadPoolExecutor.prestartAllCoreThreads()` 이 메서드를 사용해서 미리 채워주자.

1. maximumPoolSize : 최대 허용되는 스레드의 수
    - 작업이 밀려서 대기 큐가 꽉 차게 되면 maximumPoolSize 만큼 스레드 수가 확장될 수 있다.
    - 주의할 점은 corePoolSize 이상의 작업이 들어오면 스레드가 새로 maximumPoolSize 만큼 생기는 것이 아니고 대기 큐가 꽉차면 그때서야 스레드가 하나씩 추가되는 방식으로 동작한다는 것이다!
    - 이때 또 주의할 점은 대기큐의 사이즈가 무제한(`LinkedBlockingQueue` 같은 것)이면 maximumPoolSize를 설정해도 무용지물이다.

1. keepAliveTime : 초과된 스레드의 유지 시간
    - corePoolSzie를 초과하여 생긴 여분의 스레드가 작업이 없을때 얼마동안 대기하다가 사라질 것인지를 정의하는 인자다.

1. workQueue : 작업을 대기시키는 큐
    - corePoolSize만큼의 스레드가 이미 모두 작업을 처리하고 있을 때 새로운 작업을 대기시킬 큐

1. RejectedExecutionHandler : 작업 거부시 처리 방식
    - 큐가 가득 차거나, maximumPoolSize에 도달하는 경우 스레드 풀에서 새로운 작업을 거부하는데 이때 어떻게 처리할지를 정의하는 핸들러다.
    - 기본 정책은 예외를 던져 작업이 거부되었음을 알린다. (AbortPolicy)

# 동작방식 그림으로 표현

<img width="630" alt="image" src="https://github.com/user-attachments/assets/2cab9c43-9536-4875-a56d-979387ed5f19">


<img width="630" alt="image" src="https://github.com/user-attachments/assets/3368d214-f190-46d7-ab93-b444739e4118">


<img width="630" alt="image" src="https://github.com/user-attachments/assets/762b7187-4712-4133-9ed3-c0bcc1467331">


<img width="630" alt="image" src="https://github.com/user-attachments/assets/34966dd8-4a5a-4820-9a5f-096bbcba2165">



# Executors 클래스

- 스레드 풀 생성의 정적 팩토리 메서드를 제공하는 클래스다.
- 자주쓰는 몇개만 알아보자

### 1. newSingleThreadExecutor()

- 단일 스레드로 작업 처리
- 내부적으로 하나의 스레드 유지 → 작업 순차처리 보장
- 작업 처리 속도는 느리지만 순서 보장이 중요한 경우 유리하다.

### 2. newFixedThreadPool(int nThreads)

- 고정된 수의 스레드 풀 생성
- 스레드 수가 고정되어있어 자원 관리에 유리하다. (CPU 사용량 등을 예측할 수 있음)
- 스레드 수가 고정되어있어 작업량이 급증하면 대기큐에 작업이 쌓이고, 꽉차면 작업이 거부된다.

### 3. newCachedThreadPool()

- 동적으로 확장 가능한 스레드풀 생성
- 요청이 들어올때마다 새로운 스레드를 생성하고 기존에 사용되지 않는 스레드는 일정 시간이 지나면 제거한다.
- 많은 수의 작업이 동시에 처리될 때 유용하다.