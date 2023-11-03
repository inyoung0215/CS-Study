# DI(Dependency Injection, 의존성 주입)

## 개념
DI란 **외부** 에서 **두 객체 간의 관계를 결정** 해주는 디자인 패턴으로, 

각 객체 간 의존관계를 스프링 컨테이너가 개발자가 정의한 Bean 등록 정보를 바탕으로 자동으로 주입해주는 기능이다.

DI의 장점으로는 코드의 유연성, 재사용성, 테스트 용이성, 결합도 낮춤, 코드의 의존성 분리 등이 있다. 이로 인해 코드가 더 가독성이 높아지고 유지보수가 용이해진다.


의존성이란 한 객체가 다른 객체를 사용(참조)할 때 의존성이 있다고 한다.

예시로, 장난감은 배터리가 있어야 움직일 수 있고 배터리에 의존하고 있다. 

장난감이 배터리를 직접 만들어 사용하지 않고, 외부에서 배터리를 넣어주는 것이 곧 의존성 주입이라고 생각하면 좋다.

![image](https://github.com/inyoung0215/CS-Study/assets/65496092/a275be2b-79f3-4358-b88e-22863b381e34)

## 의존성 주입의 필요성 
예를 들어 연필이라는 상품과 1개의 연필을 판매하는 Store 클래스가 있다고 하자.

```java
public class Store {

    private Pencil pencil;

    public Store() {
        this.pencil = new Pencil();
    }

}
```
위 클래스는 크게 다음과 같은 문제점을 가지고 있다.

### 1. 두 클래스가 강하게 결합되어 있음
두 클래스가 강하게 결합되어 있어서 만약 Store에서 Pencil이 아닌 Food와 같은 다른 상품을 판매하고자 한다면 Store 클래스의 생성자에 변경이 필요하다. 

즉, 유연성이 떨어진다. 각각의 다른 상품들을 판매하기 위해 생성자만 다르고 나머지는 중복되는 Store 클래스들이 파생되는 것은 좋지 못하다. 

이에 대한 해결책으로 상속을 떠올릴 수 있지만, 상속은 제약이 많고 확장성이 떨어지므로 피하는 것이 좋다.

### 2. 객체들 간의 관계가 아니라 클래스 간의 관계가 맺어짐
위의 Store와 Pencil는 객체들 간의 관계가 아니라 클래스들 간의 관계가 맺어져 있다는 문제가 있다. 

올바른 객체지향적 설계라면 객체들 간에 관계가 맺어져야 한다. 

객체들 간에 관계가 맺어졌다면 다른 객체의 구체 클래스(Pencil인지 Food 인지 등)를 전혀 알지 못하더라도, (해당 클래스가 인터페이스를 구현했다면) 인터페이스의 타입(Product)으로 사용할 수 있다.

ex) Product product = new pencil();

결국 위와 같은 문제점이 발생하는 근본적인 이유는 Store에서 불필요하게 어떤 제품을 판매할 지에 대한 관심이 분리되지 않았기 때문이다. Spring에서는 DI를 적용하여 이러한 문제를 해결하고자 하였다.

## 의존성 주입 종류
### 1. 생성자 주입 (Constructor Injection)
```java
@Controller
public class TestController {

    private final TestService testService;

    @Autowired // 생성자가 1개만 있을 경우 생략 가능
    public TestController(TestService testService) {
        this.testService = testService;
    }
    
}
```

생성자를 통해 의존관계를 주입받는 방법으로, 우리가 일반적으로 사용해 오던 자바 코드와 딱히 다를 것 없으며
현재 스프링 프레임워크에서 가장 권장되는 방식이다.

- 정적 팩토리 메서드를 사용해서 생성자를 호출하더라도 그 결과는 동일하다.
- 생성자를 호출할 때 딱 1번만 호출되기 때문에 변수를 final로 관리할 수 있다.
- 생성자 주입 사용 시, 생성자가 1개인 경우 @Autowired를 생략할 수 있다. -> 순수 자바 코드로도 잘 작동할 수 있기 때문에, 굳이 DI 프레임워크에 의존성을 가질 필요가 없다

### 2. 필드 주입 (Field Injection)
```java
@Controller
public class TestController {

    @Autowired
    TestService testService;
    
}
```
프레임워크 의존도가 높아 추천되지 않는 방식(IDE에서도 warning 발생)

- 코드가 간결하다는 것이 가장 큰 장점이다.
- 클래스 외부에서 접근이 불가능해 테스트하기 어렵다는 단점이 있다.
- 기존에 작성하던 자바의 코드와는 매우 이질적이다. 이 코드는 DI 프레임워크가 없으면 사용할 수 없게 된다.
- final 선언이 불가하기 때문에 객체가 변경될 가능성이 있다.

### 3. 수정자 주입 (Setter Injection)
```java
@Controller
public class TestController {

    private TestService testService;

    @Autowired
    public void setTestService(TestService testService) {
        this.testService = testService;
    }
    
}
```
필드의 값을 변경하는 setter 메서드를 통해서 의존 관계를 주입하는 방법이다.

- 변경 가능성이 있는 의존 관계에 사용한다.
- 생성자 호출 이후에 필드 변수에 변경이 일어나야 하므로, final 제어자를 붙일 수 없다. 
- DI가 없이 순수한 자바 코드를 사용할 때 setter를 모두 호출해 필드에 값을 넣을 것을 강제할 수 없다. 누락된 필드의 변수는 null로 남아있어 NullPointerException이 발생하게 된다.

## 왜 Constructor Injection을 권장하나?
1. 단일 책임의 원칙

    생성자의 인자가 많을 경우 코드량도 많아지고, 의존관계도 많아져 단일 책임의 원칙에 위배된다. 그래서 Constructor Injection을 사용함으로써 의존관계, 복잡성을 쉽게 알수 있어 리팩토링의 단초를 제공하게 된다.

2. 테스트 용이성

    DI 컨테이너에서 관리되는 클래스는 특정 DI 컨테이너에 의존하지 않고 POJO여야 한다. DI 컨테이너를 사용하지 않고도 인스턴스화 할 수 있고, 단위 테스트도 가능하며, 다른 DI 프레임 워크로 전환할 수도 있게 된다.

    ```
   POJO?
   Plain Old Java Object, 직역하면 오래된 방식의 자바 객체라는 뜻으로 평범한 클래스이다. 
   종속되지 않는다는 것은 코드를 간결히 할 수 있고, 객체지향 설계를 충실히 이행하고 있음을 보여준다.
   
   스프링 특징을 보다보면 POJO라는 단어가 존재한다. 과거에는 자바로 웹 어플리케이션을 개발하기 위해서는 Servlet 클래스를 상속받아서 사용했다. 
   이 Servlet 클래스는 POJO가 아닌 것이다. 즉 Servlet 클래스를 작성하지 않고 POJO만으로 웹 어플리케이션을 개발할 수 있다는 것이 스프링의 특징이다.
   ```

3. Immutability

    Constructor Injection에서는 필드는 final로 선언할 수 있다. 불변 객체가 가능한데 비해 Field, Setter Injection은 final는 선언할 수 없기 때문에 객체가 변경 가능한 상태가 된다.

4. 순환 의존성

    Constructor Injection에서는 멤버 객체가 순환 의존성을 가질 경우 BeanCurrentlyInCreationException이 발생해서 순환 의존성을 알 수 있게 된다.

5. 의존성 명시

    의존 객체 중 필수는 Constructor Injection을 옵션인 경우는 Setter Injection을 활용할 수 있다.

# 보충
## Q1. 어떤 상황에서 DI를 사용해야 하나요?
DI는 특히 큰 애플리케이션, 모듈성이 중요한 경우, 테스트 가능한 코드를 작성하려는 경우, 객체의 의존성을 외부에서 쉽게 변경하고자 할 때 유용합니다. 

또한, DI는 유닛 테스트 및 모킹에 필수적입니다.

## Q2. Spring에서 DI를 어떻게 구현하나요? DI를 지원하기 위해 Spring이 제공하는 주요 구성 요소는 무엇인가요?
Spring에서 DI는 빈 컨테이너를 통해 구현됩니다. 빈 컨테이너는 객체의 라이프사이클을 관리하고 객체 간의 의존성을 관리합니다. 

주요 DI 구성 요소로는 XML 설정 파일, Java 설정 클래스, 컴포넌트 스캔, 어노테이션(@Autowired, @Resource, @Inject) 등이 있습니다.

## Q3. Spring에서 어떻게 순환 의존성(Circular Dependency) 문제를 해결하나요?
Spring은 순환 의존성을 해결하기 위한 여러 방법을 제공합니다. 

@Lazy 어노테이션을 사용하여 지연 로딩을 활성화하거나, @Autowired와 @Qualifier를 조합하여 의존성을 명시적으로 지정할 수 있습니다. 

또한, 인터페이스를 사용한 프록시 패턴을 적용하거나, @PostConstruct와 @PreDestroy 어노테이션을 사용하여 초기화 및 소멸 메서드를 관리할 수 있습니다.

## Q4. @Autowired, @Resource, @Inject, @Qualifier 등 DI 관련 주석(Annotation)을 설명할 수 있나요?
@Autowired는 Spring에서 자동 와이어링을 수행하는 어노테이션으로, 의존성을 주입하기 위해 사용됩니다. 

@Resource는 Java EE에서 제공되며 이름 또는 JNDI 위치를 기반으로 의존성을 주입합니다.

@Inject는 Java EE 및 Java SE에서 사용되며 @Autowired와 유사하지만 자바 표준이기도 합니다. 

@Qualifier는 여러 후보 빈 중에서 어떤 빈을 주입할 것인지 지정하는 데 사용됩니다.


> Reference <br></br>
> [[Spring] 의존성 주입(Dependency Injection, DI)이란? 및 Spring이 의존성 주입을 지원하는 이유](https://mangkyu.tistory.com/150) <br></br>
> [[Spring] 스프링 의존성 주입(DI) 이란?](https://devlog-wjdrbs96.tistory.com/165) <br></br>
> [[Spring] 스프링 의존성 주입 (Dependency Injection) 개념 및 종류](https://kim-oriental.tistory.com/32) <br></br>
> [[Spring] Spring Core(2): 의존성 주입(DI), 개념, 방법, 장단점, 생성자 주입을 사용하자!](https://engineerinsight.tistory.com/46#%F0%9F%92%8B%20%EC%9D%98%EC%A1%B4%EC%84%B1%EC%9D%84%20%EC%A3%BC%EC%9E%85%ED%95%98%EB%8A%94%20%EB%B0%A9%EB%B2%95-1)  <br></br>
> [DI(의존성 주입)가 필요한 이유와 Spring에서 Field Injection보다 Constructor Injection이 권장되는 이유](https://www.mimul.com/blog/di-constructor-injection/) <br></br>