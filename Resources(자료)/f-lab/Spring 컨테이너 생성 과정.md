
## Spring Container 등록과정

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```
```java
@Configuration  
public class AppConfig {  
  
    //@ Bean 이붙으면 기본적으로 method 이름으로 등록됨  
  
    @Bean  
    public MemberService memberService() {  
        return new MemberServiceImpl(getMemberRepository());  
    }  
  
    @Bean  
    public MemoryMemberRepository getMemberRepository() {  
        return new MemoryMemberRepository();  
    }  
  
    @Bean  
    public OrderService orderService() {  
        return new OrderServiceImpl(getMemberRepository(), discountPolicy());  
    }  
  
    @Bean  
    public DiscountPolicy discountPolicy() {  
        return new FixDiscountPolicy();  
    }  
}
```


- ApplicationContext 를 스프링 컨테이너라 한다.
- ApllicationContext는 인터페이스이다.
- 스프링 컨테이너는 XML 기반, 애노테이션 기반의 자바 설정 클래스로 만들 수 있음.
- 더 정확히 말하면 BeanFactory, ApplicationContext 로 구분해서 이야기한다.
  BeanFactory를 직접 사용하는 경우는 거의 없어서, 일반적으로 ApplicationContext를 스프링 컨테이너라 한다.

스프링 컨테이너의 생성 과정
![[Pasted image 20250319065434.png]]
- 스프링 컨테이너를 생성할 때는 구성 정보를 지정해줘야한다.
- 여기서는 AppConfig.class 를 구성 정보로 저장

2. 스프링 빈 등록
![[Pasted image 20250319065559.png]]
- 빈 이름은 메서드 이름을 사용한다.
- 빈 이름을 직접 부여도 할 수 있음 @Bean(name="memberService2")
**빈 이름은 항상 다른 이름을 부여해야 한다** 같은 이름을 부여하면, 다른 빈이 무시되거나, 기존 빈을 덮어버리거나 설정에 따라 오류 발생함.


![[Pasted image 20250319070230.png]]
![[Pasted image 20250319070249.png]]
- 스프링 컨테이너는 설정 정보를 참고해서 의존관계를 주입
- 단순히 자바 코드 같지만 차이가 있다.

스프링은 빈을 생성하고, 의존관계를 주입하는 단계가 나누어져 있다. 그런데 이렇게 자바 코드로 스프링 빈을 등록하면 생성자를 호출하면서 의존관계 주입도 한번에 처리된다



## 스프링 컨테이너에서 모든 빈 조회
### 모든 빈 출력하기
```java
void findAllBean() {  
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();  
  
    for (String beanDefinitionName : beanDefinitionNames) {  
        Object bean = ac.getBean(beanDefinitionName);  
        System.out.println("name = " + beanDefinitionName + " object = " + bean);  
    }  
}
```

### 애플리케이션 빈 출력하기
```java
void findApllicationBean() {  
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();  
  
    for (String beanDefinitionName : beanDefinitionNames) {  
        BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);  
  
        // Role ROLE_APPLICATION 내가 application을 개발하기위헤 등록한 빌, or 외부 라이브러리  
        // Role ROLE_INFRASTRUCTURE : 스프링 내부에서 사용하는 빈  
        if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {  
            Object bean = ac.getBean(beanDefinitionName);  
            System.out.println("name = " + beanDefinitionName + " object = " + bean);  
        }  
    }  
}
```


### 스프링 빈 조회(타입으로만 조회, 구체 타입으로 조회, 없는 이름으로 조회)
```java
@DisplayName("이름 없이 타입으로만 조회")  
void findBeanByType() {  
    MemberService memberService = ac.getBean(MemberService.class);  
    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);  
}

@DisplayName("구체 타입으로 조회")  
void findBeanByName2() {  
    MemberService memberService = ac.getBean("memberService", MemberServiceImpl.class);  
    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);  
}

@DisplayName("빈 이름으로 조회X")  
void findBeanByNameX() {  
    //ac.getBean("xxxxx", MemberService.class)  
    MemberService memberService = ac.getBean("xxxxx", MemberService.class);  
    assertThrows(NoSuchBeanDefinitionException.class, () -> ac.getBean("xxxxx", MemberService.class));  
}
```


### 동일한 타입이 둘 이상일 때
```java
AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SameBeanConfig.class);  
  
@Test  
@DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면, 중복 오류가 발생한다")  
void findBeanByTypeDuplicate() {  
    Assertions.assertThrows(NoUniqueBeanDefinitionException.class, () -> ac.getBean(MemberRepository.class));  
}  
  
@Test  
@DisplayName("타입으로 조회 시 같은 타입이 둘 이상 있으면, 빈 이름을 지정하면 된다.")  
void findByBeanByName() {  
    MemberRepository memberRepository = ac.getBean("memberRepository1", MemberRepository.class);  
    org.assertj.core.api.Assertions.assertThat(memberRepository).isInstanceOf(MemberRepository.class);  
}  
  
@Test  
@DisplayName("특정 타입을 모두 조회하기")  
void findAllBeanByType() {  
    Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);  
  
    for (String key : beansOfType.keySet()) {  
        System.out.println("key = " + key + " value = " + beansOfType.get(key));  
  
    }  
  
    System.out.println("beansOfType = " + beansOfType);  
    org.assertj.core.api.Assertions.assertThat(beansOfType.size()).isEqualTo(2);  
}  
  
@Configuration  
static class SameBeanConfig {  
    @Bean  
    public MemberRepository memberRepository1() {  
        return new MemoryMemberRepository();  
    }  
  
    @Bean  
    public MemberRepository memberRepository2() {  
        return new MemoryMemberRepository();  
    }  
  
  
}
```

스프링 빈 조회 - 상속관계
- 부모 타입으로 조회하면, 자식 타입도 함께 조회된다.
- 그래서 모든 자바 객체의 최고 부모인 'Object' 타입으로 조회하면, 모든 스프링 빈을 조회한다
![[Pasted image 20250319081449.png]]

```java
package hello.core.beanfind;  
  
import hello.core.discount.DiscountPolicy;  
import hello.core.discount.FixDiscountPolicy;  
import hello.core.discount.RateDiscountPolicy;  
import org.junit.jupiter.api.Assertions;  
import org.junit.jupiter.api.DisplayName;  
import org.junit.jupiter.api.Test;  
import org.springframework.beans.factory.NoUniqueBeanDefinitionException;  
import org.springframework.context.annotation.AnnotationConfigApplicationContext;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
  
import java.util.Map;  
  
import static org.assertj.core.api.Assertions.assertThat;  
  
public class ApplicationContextExtendsFindTest {  
  
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);  
  
    @Test  
    @DisplayName("부모 타입으로 조회 시, 자식이 둘 이상 있으면, 중복 오류가 발생한다")  
    void findBeanByParentTypeDuplicate() {  
        Assertions.assertThrows(NoUniqueBeanDefinitionException.class, () -> ac.getBean(DiscountPolicy.class));  
    }  
  
    @Test  
    @DisplayName("부모 타입으로 조회 시, 자식이 둘 이상 있으면, 빈 이름을 지정하면 발생한다")  
    void findBeanByParentTypeBeanName() {  
        DiscountPolicy rateDiscountPolicy = ac.getBean("rateDiscountPolicy", DiscountPolicy.class);  
        assertThat(rateDiscountPolicy).isInstanceOf(RateDiscountPolicy.class);  
    }  
  
    @Test  
    @DisplayName("특정 하위 타입으로 조회 - 좋은 방법 아님")  
    void findBeanBySubType() {  
        RateDiscountPolicy bean = ac.getBean(RateDiscountPolicy.class);  
        assertThat(bean).isInstanceOf(RateDiscountPolicy.class);  
    }  
  
  
    @Test  
    @DisplayName("부모 타입으로 모두 조회하기")  
    void findAllBeanByParentType() {  
        Map<String, DiscountPolicy> beansOfType = ac.getBeansOfType(DiscountPolicy.class);  
        assertThat(beansOfType.size()).isEqualTo(2);  
  
        for (String key : beansOfType.keySet()) {  
            System.out.println("key = " + key + " value = " + beansOfType.get(key));  
        }  
    }  
  
    @Test  
    @DisplayName("부모 타입으로 모두 조회하기 - Object")  
    void findAllBeanByObjectType() {  
        Map<String, Object> beansOfType = ac.getBeansOfType(Object.class);  
  
        for (String key : beansOfType.keySet()) {  
            System.out.println("key = " + key + " value = " + beansOfType.get(key));  
        }  
    }  
  
    @Configuration  
    static class TestConfig {  
        @Bean  
        public DiscountPolicy rateDiscountPolicy() {  
            return new RateDiscountPolicy();  
        }  
  
        @Bean  
        public DiscountPolicy fixDiscountPolicy() {  
            return new FixDiscountPolicy();  
        }  
    }  
}
```


BeanFacotry와 ApllicationContext
![[Pasted image 20250319083145.png]]

**BeanFacotry**
- 스프링 컨테이너의 최상위 인터페이스
- 스프링 빈을 관리하고 조회하는 역할 담당
- getBean() 을 제공한다.

ApllicationContext
- BeanFacotry 기능을 모두 상속받아서 제공
- 그럼 BeanFacotry하고 차이?
- 더 많은 부가 기능을 제공해줌.

![[Pasted image 20250319083335.png]]
- 메세지소스를 활용한 국제화 기능
	- 에를 들어서 한국에서 들어오면 한국으로, 영어권에서 들어오면 영어로 출력
- 환경변수
	- 로컬, 개발, 운영등을 구분해서 처리
- 애플리케이션 이벤트
	- 이벤트를 발행하고 구독하는 모델을 편리하게 지원
- 편리한 리소스 조회
	- 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회
**정리**
- ApplicationContext는 BeanFactory 의 기능을 상속받음
- ApplicationContext는 빈 과ㅣㄴ리기능 + 편리한 부가 기능 제공
- BeanFacotry 나 ApplicationContext을 스프링 컨테이너라한다., BeanFacotry는 거의 사용 안함



## 다양한 설정 형식 지원

![[Pasted image 20250319084640.png]]


XML은 컴파일 없이 빈 설정 정보를 변경할 수 있다는 장점이 있음.

### 🌱 **스프링 빈 설정 메타 정보 - `BeanDefinition`**

`BeanDefinition`은 스프링이 다양한 형식을 지원할 수 있도록 추상화한 개념입니다.  
즉, **역할과 구현을 분리**하여 빈을 설정하는 메타 정보를 제공하는 역할을 합니다.

#### 🛠 **주요 개념**

- XML을 읽어서 `BeanDefinition`을 생성할 수 있다.
- 자바 코드를 읽어서 `BeanDefinition`을 생성할 수 있다.
- `@Bean`, `<bean>` 각각에 대해 하나의 `BeanDefinition` 메타 정보가 생성된다.
- 스프링 컨테이너는 이 **메타 정보**를 기반으로 스프링 빈을 생성한다

![[Pasted image 20250319090023.png]]


![[Pasted image 20250319090240.png]]



AnnotationConfigApplicationContext 는 AnnotatedBeanDefinitionReader 를 사용해서 AppConfig.class 를 읽고 BeanDefinition 을 생성한다. GenericXmlApplicationContext 는 XmlBeanDefinitionReader 를 사용해서 appConfig.xml 설정 정보를 읽고 BeanDefinition 을 생성한다. 새로운 형식의 설정 정보가 추가되면, XxxBeanDefinitionReader를 만들어서 BeanDefinition 을 생성하 면 된다.









## 싱글톤 컨테이너

- 스프링 컨테이너는 싱글톤 패턴을 적용하지 않안도, 객체 인스턴스를 싱글톤으로 관리한다.
- 스프링 컨테이너는 싱글톤 컨테이너 역할을 한다. 이렇게 싱글톤 객체를 관리하는 기능을 싱글톤 레지스트리라 한다.
- 스프링 컨테이너의 이런 기능 덕분에 싱글톤 패턴의 모든 단점을 해결하면서 객체를 싱글톤으로 유지할 수 있다
- DI, OCP, TEST, private 생성자로 부터 자유롭게 싱글톤을 사용할 수 있음.

### 싱글톤 방식의 주의점
- 싱글톤 방식은 여러 클아이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안된다
- 무상태(stateless)로 설계해야 한다!!!!
	- 특정 클라이언트에 의존적인 필드가 있으면 안된다.
	- 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
	- 가급적 읽기만 가능해야 한다.
	- 필드 대신 자바에서 공유되지 않는, 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.
- 스프링 빈의 필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있다.
