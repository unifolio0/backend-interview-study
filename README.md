# Spring
<details>
<summary><b>@Async는 어떻게 동작하나요?</b></summary>
<hr />
@Async는 스프링 AOP를 통해 동작합니다.
스프링이 @Async가 붙은 메서드를 발견하면, 해당 클래스의 프록시 객체를 생성해서 빈으로 등록합니다.
다른 빈에서 이 서비스를 주입받으면 실제로는 프록시 객체가 주입되고, 메서드를 호출하면 프록시 안에 있는 MethodInterceptor가 AsyncExecutionInterceptor를 실행합니다.
AsyncExecutionInterceptor는 TaskExecutor를 사용해서 새로운 스레드에서 실제 메서드를 실행하고, 호출한 쪽에는 즉시 리턴해서 비동기 처리가 가능해집니다.
중요한 점은, 같은 클래스 내부에서 @Async 메서드를 직접 호출하면 프록시를 거치지 않기 때문에 비동기로 동작하지 않습니다.
</details>
