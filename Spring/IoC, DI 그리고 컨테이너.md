### IoC(Inversion of Control) 제어의 역전

- 기존 프로그램은 클라이언트 구현 객체가 스스로 필요한 서버 구현 객체를 생성하고, 연결하고, 실행했다. 한
  마디로 구현 객체가 프로그램의 제어 흐름을 스스로 조종했다.
- 반면에 AppConfig가 등장한 이후에 구현 객체는 자신의 로직을 실행하는 역할만 담당한다.
- 프로그램에 대한 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있다.
- 이렇듯 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)이라
  한다.

### 프레임워크 vs 라이브러리

- 프레임워크가 내가 작성한 코드를 제어하고, 대신 실행하면 그것은 프레임워크가 맞다.
- 반면에 내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그것은 프레임워크가 아니라 라이브러리다.

### DI(Dependency Injection) 의존관계 주입

- 의존관계는 정적인 클래스 의존 관계와, 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계 둘을 분리
  해서 생각해야 한다.
- 정적인 클래스 의존관계
  - 클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있다. 정적인 의존관계는 애플리케이션
    을 실행하지 않아도 분석할 수 있다.

- 동적인 객체 인스턴스 의존관계
  - 애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계다.
  - 애플리케이션 실행 시점(런타임)에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언
    트와 서버의 실제 의존관계가 연결 되는 것을 의존관계 주입이라 한다.
  - 객체 인스턴스를 생성하고, 그 참조값을 전달해서 연결된다.
  - 의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스
    를 변경할 수 있다.
  - 의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽
    게 변경할 수 있다.

### IoC 컨테이너, DI 컨테이너

- AppConfig 처럼 객체를 생성하고 관리하면서 의존관계를 연결해 주는 것을 IoC 컨테이너 또는 DI 컨테이너라 한다.
- 의존관계 주입에 초점을 맞추어 최근에는 주로 DI 컨테이너라 하고 어샘블러, 오브젝트 팩토리 등으로 불리기도 한다.

### 스프링 컨테이너

- ApplicationContext를 스프링 컨테이너라 한다
- 기존에는 개발자가 AppConfig를 사용하여 직접 객체를 생성하고 DI를 했지만, 이제는 스프링 컨테이너를 이용한다
- 스프링 컨테이너는 @Configuration이 붙은 AppConfig를 설정(구성) 정보로 사용한다, @Bean이라 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다, 이렇게 스프링 컨테이너에 등록된 객체를 스프링 빈이라 한다
- 스프링 빈은 @Bean이 붙은 메서드의 명을 스프링 빈의 이름으로 사용한다, @Bean(name="이름")과 같이 빈 이름을 직접 부여할 수도 있다
- 스프링 빈은 applicationContext.getBean() 메서드를 이용하여 찾을 수 있다

```java
// AppConfig
@Configuration
public class AppConfig {
    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }
    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}

// MemberApp
public class MemberApp {
    public static void main(String[] args) {
	    // AppConfig appConfig = new AppConfig();
	    // MemberService memberService = appConfig.memberService();
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberService memberService = ac.getBean("memberService", MemberService.class);
    }
}
```

---

### BeanFactory

- 스프링 컨테이너의 최상위 인터페이스
- 스프링 빈을 관리하고 조회하는 역할을 담당
- getBean() 또한 BeanFactory가 제공

### ApplicationContext

- BeanFactory 기능을 모두 상속받아서 제공한다
- 메시지소스를 활용한 국제화 기능
  - 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력
- 환경변수
  - 로컬, 개발, 운영등을 구분해서 처리
- 애플리케이션 이벤트
  - 이벤트를 발행하고 구독하는 모델을 편리하게 지원
- 편리한 리소스 조회
  - 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회
- BeanFactory를 직접 사용할 일은 없고 부가기능이 포함된 ApplicationContext를 사용한다
- BeanFactory나 ApplicationContext를 스프링 컨테이너라 한다
