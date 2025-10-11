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
  우선 요청이 들어오면 필터가 동작합니다. 필터 체인을 거치면서 요청을 처리한 이후 DispatcherServelet에 진입합니다. 그 뒤 HandlerMapping을 통해 요청을 처리하기 적절한 Handler를 찾은 뒤, 인터셉터의 preHandle이 동작합니다. 그리고 Handler의 응답을 처리할 HandlerAdapter를 선택합니다. 그 다음 ArgumentResolver에서 RequestBody나 PathVariable과 같이 파라미터 타입에 알맞게 요청 정보를 변환합니다. Handler가 실행된 뒤 해당 반환 값을 Adapter가 적절히 변환합니다. 그 다음 인터셉터의 postHandle이 동작하고, 만약 뷰 렌더링이 필요하면 뷰 리졸버가 동작하여 뷰 렌더링을 거치게 되고 모든 처리가 완료된 후 인터셉터의 afterCompletion이 동작합니다. 마지막으로 필터 체인을 역순으로 거치게 됩니다.
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
