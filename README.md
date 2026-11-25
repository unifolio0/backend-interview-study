# OS
<details>
<summary><b>동기/비동기, 블로킹/논블로킹은 무엇이고 각각의 차이점은 무엇인가요?</b></summary>
<hr />
동기와 비동기는 '작업 결과를 누가 처리하냐'의 문제입니다.
동기는 호출한 쪽에서 결과를 직접 받아서 처리하고,
비동기는 콜백이나 이벤트를 통해 나중에 별도로 처리됩니다.

블로킹과 논블로킹은 '제어권을 언제 돌려주냐'의 문제입니다.
블로킹은 작업이 끝날 때까지 제어권을 반환하지 않아 대기하게 되고,
논블로킹은 작업 완료와 관계없이 즉시 제어권을 돌려줘서
다른 작업을 할 수 있습니다.

- [동기/비동기, 블로킹/논블로킹은 무엇이고 각각의 차이점은 무엇인가요?](https://study-note10.tistory.com/57)
</details>

# Spring
<details>
  <summary><b>스프링 프레임워크에서 MVC가 어떻게 동작하나요?</b></summary>
  <hr />
  요청이 들어오면 필터가 동작합니다. 필터 체인을 거치면서 요청을 처리한 이후 DispatcherServlet에 진입합니다. 그 뒤 HandlerMapping을 통해 요청을 처리하기 적절한 Handler와 인터셉터 목록을 찾고, 그 Handler를 실행할 HandlerAdapter를 선택합니다. 그 다음 인터셉터의 preHandle이 동작합니다. 이후 HandlerAdapter가 ArgumentResolver를 통해 PathVariable이나 RequestBody 같은 요청 정보를 파라미터 타입에 맞게 변환하고 Handler를 실행합니다. Handler의 반환 값은 ReturnValueHandler가 처리하는데, @ResponseBody라면 HttpMessageConverter가 JSON 등으로 변환해 응답 본문에 바로 씁니다. 그 다음 인터셉터의 postHandle이 동작하고, 뷰 렌더링이 필요하면 ViewResolver가 뷰를 찾아 렌더링합니다. 모든 처리가 완료된 후 인터셉터의 afterCompletion이 동작하고, 마지막으로 필터 체인을 역순으로 거치게 됩니다. 이때 @ResponseBody 응답은 postHandle 전에 이미 쓰여 있기 때문에 postHandle에서 응답을 바꿀 수 없고, 예외가 발생하면 postHandle은 건너뛰고 afterCompletion만 동작합니다.
</details>

<details>
<summary><b>@Async는 어떻게 동작하나요?</b></summary>
<hr />
@Async는 스프링 AOP를 통해 동작합니다.
스프링이 @Async가 붙은 메서드를 발견하면, 해당 클래스의 프록시 객체를 생성해서 빈으로 등록합니다.
다른 빈에서 이 서비스를 주입받으면 실제로는 프록시 객체가 주입되고, 메서드를 호출하면 프록시 안에 있는 MethodInterceptor가 AsyncExecutionInterceptor를 실행합니다.
AsyncExecutionInterceptor는 TaskExecutor를 사용해서 새로운 스레드에서 실제 메서드를 실행하고, 호출한 쪽에는 즉시 리턴해서 비동기 처리가 가능해집니다.
중요한 점은, 같은 클래스 내부에서 @Async 메서드를 직접 호출하면 프록시를 거치지 않기 때문에 비동기로 동작하지 않습니다.

- [@Async는 어떻게 동작하나요?](https://study-note10.tistory.com/55)
</details>

<details>
<summary><b>AOP를 사용할 때 주의점에 대해서 설명해주세요</b></summary>
<hr />
AOP를 활용할 때 우선 같은 클래스 내에서 호출하거나 접근 제어자가 private인 경우 AOP가 동작하지 않습니다. 
그리고 순서 보장 문제가 있습니다. 이는 여러 AOP 어노테이션이 함께 사용될 때 한 메소드에 전부 붙이면 어떤 순서로 적용될 지 예측하기 어려운 문제를 의미합니다. 
예를 들어 트랜잭션 어노테이션과 Async 어노테이션을 함께 사용할 때 어싱크가 먼저 동작해야 비동기로 동작하면서 트랜잭션까지 적용이 되는데 만약 트랜잭션이 먼저 동작하면 비동기로 동작하지만 트랜잭션은 적용되지 않는 문제점이 발생합니다.
</details>

# JPA
<details>
<summary><b>엔티티 상태에 대해 설명해주세요</b></summary>
<hr />
엔티티의 상태는 비영속(New/Transient), 영속(Managed), 준영속(Detached), 삭제(Removed) 4가지가 존재합니다. 
비영속은 엔티티 인스턴스를 생성했지만, 아직 영속성 컨텍스트에 관리되지 않은 상태를 의미합니다. 
영속은 엔티티가 영속성 컨텍스트에 의해 관리되는 상태로 엔티티 매니저의 `persist()` 메소드 호출 후, 영속성 컨텍스트에 저장되면 영속 상태가 됩니다. 
준영속은 영속성 컨텍스트에서 분리된 상태로 `detach()`로 특정 엔티티를 분리하거나, `clear()`로 영속성 컨텍스트를 초기화하거나, `close()`로 닫으면 준영속 상태가 됩니다. `flush()`는 변경 내용을 DB에 반영할 뿐이고 엔티티는 계속 영속 상태로 남습니다. 다만 스프링처럼 트랜잭션 범위의 영속성 컨텍스트를 쓰면 트랜잭션이 끝날 때 영속성 컨텍스트도 닫히기 때문에, 그 이후에는 엔티티가 준영속 상태가 됩니다. 
삭제는 엔티티 매니저의 `remove()` 메소드를 호출하면 삭제 상태가 되며 DB에서도 삭제됩니다.
</details>
