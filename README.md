## JWP-advanced

- 로그 추적기
  - 요구사항:
    > - 모든 PUBLIC 메서드의 호출과 응답 정보를 로그로 출력
    > - 애플리케이션의 흐름을 변경하면 안됨
    >  - 로그를 남긴다고 해서 비즈니스 로직의 동작에 영향을 주면 X
    > - 메서드 호출에 걸린 시간
    > - 정상 흐름과 예외 흐름 구분
    >  - 예외 발생시 예외 정보가 남아야 함
    > - 메서드 호출의 깊이 표현
    >  - HTTP 요청을 구분
    >  - 트랜잭션 ID (DB 트랜잭션 X)
    
  - 로그 추적기 V1
  
  - 로그 추적기 V2
  
  
  
- 쓰레드 로컬 - ThreadLocal
  - 필드 동기화

  - 필드 동기화 - 동시성 문제
    
    > 동시성 문제
    > `FieldLogTrace` 는 싱글톤으로 등록된 스프링 빈 --> 이 객체의 인스턴스가 애플리케이션에 딱 1 존재한다는 뜻
    > 이렇게 하나만 있는 인스턴스의 `FieldLogTrace.traceIdHolder` 필드를 여러 쓰레드가 동시에 접근하기 때문에 문제가 발생
    > 실무에서도 자주 있는 동시성 문제
    >
    > **동시성 문제는 값을 읽기만 하면 발생하지 않음 -> 어디선가 값을 변경하기 때문에 발생**
    
    - ThreadLocal

      > 쓰레드 로컬은 해당 쓰레드만 접근할 수 있는 특별한 저장소를 말함.
      > *주의 :*  해당 쓰레드가 쓰레드 로컬을 모두 사용하고 나면 `ThreadLocal.remove()` 를 호출해서 저장된 값을 제거 해주어야 함.
      
      
  
- 템플릿 메서드 패턴 과 콜백 패턴

  - 템플릿 메서드 패턴

    - 좋은 Application 설계

      > **변하는 것과 변하지 않는 것을 분리**
      >
      > 좋은 설계 -> 변하는 것과 변하지 않는 것을 분리하는 것
      > 여기서 핵심 기능 부분은 변하고, 로그 추적기를 사용하는 부분은 변하지 않는 부분 따라서 이 둘을 분리해서 모듈화해야 함.
      > 템플릿 메서드 패턴(Template Method Pattern) 은 이런 문제를 해결하는 디자인 패턴.

    - 템플렛 메서트 패턴이란?

      > **템플릿 메서드 패턴** : 이름 그대로 템플릿을 사용하는 방식, 템플릿 기준이 되는 거대한 틀이다.  일부 변하는 부분을 별도로 호출해서 해결
      >
      > * 템플릿 메서드 패턴은 다형성을 사용해 변하는 것, 변하지 않는 것을 분리

    - 익명 내부 클래스 사용

    - 좋은 설계란?

      > **좋은 설계란 무엇일까?**
      >
      > 진정한 좋은 설계는 바로 **변경**이 일어날 때 자연스럽게 드러난다.
      >
      > 
      >
      > **단일 책임 원칙(SRP)**
      >
      > 템플릿 메서드 패턴을 적용해서 소스코드를 줄인 것이 전부가 아니라 변경 지점을 하나로 모아서 변경에 쉽게 대처할 수 있는 구조를 
      > 만드는 것

    - 템플릿 메스터 패턴 - 정의

      > **템플릿 메서드 디자인 패턴의 목적** :
      >
      > "작업에서 알고리즘의 골격을 정의, 일부 단계를 하위 클래스로 연기함. 템플릿 메서드를 사용하면 하위 클래스가 알고리즘의
      > 구조를 변경하지 않고도 알고리즘의 특정 단계를 재정의 할 수 있음" [GOF]
      >
      > 
      >
      > **하지만**
      >
      > 템플릿 메서드 패턴은 상속에 단점을 안고 간다.

  - 전략 패턴

    - 전략 패턴이란?

      > "알고리즘 제품군을 정의하고 각각을 캡슐화하여 상호 교환 가능하게 만들자. 전략을 사용하면 알고리즘을 사용하는 클라이언트와
      > 독립적으로 알고리즘을 변경할 수 있다." [GOF]
      
    - 정리 :

      > - 변하지 않는 부분을 `Context`두고 변하는 부분을 `Strategy`를 구현해서 만든다.
      >
      > - 우리가 스프링으로 로딩 시점에 의존관계 주입을 통해 필요한 의존관계를 모두 맺고 난 다음에 실제 요청을 처리하는 것 과 같은 원리
      >
      > - 단점은 조립한 이후에는 전략을 변경하기 번거롭다. **싱글톤으로 사용할 때는 동시성 이슈 등 고려할 점이 많다.

    - **템플릿**

      > 변하지 않는 부분을 템플릿이라 하고, 그 템플릿 안에서 변하는 부분에 약간 다른 코드 조각을 넘겨서 실핼하는 것이 목적.
      > 단순히 코드를 실행할 때 변하지 않는 템플릿이 있고, 그 템플릿 안에서 원하는 부분만 살짝 다른 코드를 실행하고 싶은 것일 뿐
      > 따라서 우리가 고민하는 문제는 실행 시점에 유연하게 실행 코드 조각을 전달하는 `ContextV2` 가 더 적합.

  - 템플릿 콜백 패턴

    - 콜백 정의

      > 콜백(CallBack) 또는 콜에프터(Call - After - Function) 함수는 다른 코드의 인수로서 넘겨주는 실행 가능한 코드를 말한다.
      > 콜백을 넘겨받는 코드는 이 콜백을 필요에 따라 즉시 실행할 수도 있고, 아니면 나중에 실행할 수도 있다.

    - 템플릿 콜백 패턴이란?

      > 템플릿 콜백 패턴은 GOF 패턴은 아니고, 스프링 내부에서 이런 방식을 자주 사용하기 때문에, 스프링 안에서만 이렇게 부른다. 전략 패턴에서 템플릿과 콜백 부분이 강조된 패턴이라 생각하면 된다.
      >
      > - 스프링에서는 `JdbcTemplate` , `RestTemplate` , `TransactionTemplate` , `RedisTemplate` 처럼 다양한 템플릿 콜백
      >   패턴이 사용된다. 스프링에서 이름에 `XxxTemplate` 이 있다면 템플릿 콜백 패턴으로 만들어져 있다고 생각하면 됨.
      
- 프록시 패턴과 데코레이터 패턴
  - 예제 프로젝트 만들기 V1 :
      > `@RequestMapping` : 스프링MVC는 `@Controller` 또는 `@RequestMapping` 에노테이션이 타입에 있어야 스프링 컨트롤러로 인식.
      > `@ResponseBody` : HTTP 메시지 컨버터를 사용해서 응답.
      > 코드에서 `request()` 는 LogTrace를 적용할 대상.
  - 예제 프로젝트 만들기 V2 :
      > `@RequestMapping` 애노테이션을 사용한 이유는 `@Controller` 를 사용하면 자동 컴포넌트 스캔의 대상이 되기 때문.
  - 예제 프로젝트 만들기 V3 
  - 요구사항 추가:
      - 원본 코드를 전혀 수정하지 않고, 로그 추적기를 적용해라.
      - 특정 메서드는 로그를 출력하지 않는 기능
        - 보안상 일부는 로그를 출력하면 안됨
      - 다음과 같은 다양한 케이스에 적용 가능 해야 함
        - v1 - 인터페이스가 있는 구현 클래스 적용
        - v2 - 인터페이스가 없는 구체 클래스에 적용
        - v3 - 컴포넌트 스캔 대상에 기능 적용
  - 프록시, 프록시 패턴, 데코레이터 패턴
    - 프록시
      > 클라이언트는 서버에 필요한 것을 요청, 서버는 클라이언트의 요청을 처리.
      > 객체를 도입하게 되면 요청하는 객체는 클라이언트, 요청을 처리하는 객체는 서버.
      > 대체 가능 : 객체에서 프록시가 되려면, 클라이언트는 서버에게 요청 한 것인지, 프록시에게 요청 한 것인지 조차 몰라야 한다.
      - 프록시의 주요기능
        > 프록시를 통해서 할 수 있는 일은 크게 2가지로 구분
        > - 접근 제어
        >  - 권한에 따른 접근 차단
        >  - 캐싱
        >  - 지연 로딩
        > - 부가 기능 추가
        >  - 원래 서버가 제공하는 기능에 더해서 기능을 수행
        >  - 예) 요청값이나, 응답 값을 중간에 변형
        >  - 예) 실행 시간을 측정해서 추가 로그를 남긴다
        > 프록시 객체가 중간에 있으면 크게 **접근 제어** 와 **부가 기능 추가**를 수행 가능
      - **GOF 디자인 패턴**
        둘다 프록시를 사용하는 방법이지만 GOF 디자인 패턴에서는 이 툴을 의도에 따라서 프록시 패턴과 데코레이터 패턴으로 구분
        - 프록시 패턴: 접근 제어가 목적
        - 데코레이터 패턴: 새로운 기능 추가가 목적
        둘다 프록시를 사용하지만 의도가 다르다는 점이 핵심이다.
      > **참고** : 프록시라는 개념은 클라이언트 서버라는 큰 개념안에서 자연스럽게 발생할 수 있다.
      > 객체안에서 객체로 구현되어있는가, 웹 서버로 구현되어 있는가 처럼 규모의 차이가 있을 뿐 근본적인 역할은 같다.
    - 프록시 패턴 - 접근 제어
      > **정리**
      > 프록시 패턴의 핵심은 `ReqalSubject` 코드와 클라이언트 코드를 전혀 변경하지 않고, 프록시를 도입해 접근 제어를 했다는 점
      > 그리고 클라이언트 코드의 변경 없이 자유롭게 프록시를 넣고 뺄 수 있다. 실제 클라이언트 입장에서 프록시 객체가 주입되었는지,
      > 실제 객체가 주입되었는지 알지 못함.
    - 데코레이터 패턴 - 예제 코드1
    - 데코레이터 패턴 - 예제 코드2
      > 프록시로 부가 기능을 추가하는 것을 데코레이터 패턴이라 한다.
    - 데코레이터 패턴 - 예제 코드3

[참고 자료](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B3%A0%EA%B8%89%ED%8E%B8/dashboard) : 스프링 핵심 원리 - 고급편
