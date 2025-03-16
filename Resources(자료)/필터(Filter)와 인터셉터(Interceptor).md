웹 애플리케이션을 개발하다 보면 **모든 요청마다 공통적으로 적용해야 하는 로직**이 필요할 때가 많습니다.

Spring AOP도 관심사를 분리하는 좋은 방법이지만, 웹 요청과 관련된 작업이라면 **Filter**나 **Interceptor**를 사용하는 것이 더 적절합니다.

이유는 간단합니다. **Filter와 Interceptor는 `HttpServletRequest`, `HttpServletResponse` 같은 객체를 직접 다룰 수 있기 때문에 URL 정보나 HTTP 헤더를 수정하기가 훨씬 수월합니다**

## Filter란?
**Filter는 클라이언트에서 요청이 들어올 때 `DispatcherServlet`에 도달하기 전에 실행되며, 응답을 보낼 때는 서블릿이 실행된 후에 동작합니다.**

즉, **요청이 들어오면 가장 먼저 확인하고, 응답을 보낼 때도 마지막으로 가공할 수 있는 위치**에 있습니다.

서블릿컨테이너에 등록되기 때문에 ORDER같은게 먹지않음.

필터에서 제공하는 메서드는 총 3개 입니다.


init() - 오버라이딩 선택
필터가 **처음 생성될 때 한 번만 실행**되는 메서드로, 여기서 초기 설정을 진행할 수 있습니다.
```java
public void init(final FilterConfig filterConfig) throws ServletException {
    // 필터 초기화 로직
}```

- `FilterConfig filterConfig` : 필터의 설정 정보를 담고 있는 객체입니다.
- 생성될 때 딱 한번만 호출

doFilter()
필터의 핵심 메서드로, 요청과 응답을 가로채, 관심사항을 따로 분리해서 처리하는데 그러한 로직들이 담겨있음
요청이 들어올 때마다 실행이되는 메소드라 인증이나 추가적인 작업은 dofilter에서 해야한다. 


destroy()
was가 닫힐 때 filter가 소명될 때 한번망 실행된다.

필터를 등록하는 방법 3가지
@Component 을 이용해서 등록
모든 URL에 적용
모든 URL에 적용하고 싶으면 이 방법 사용
```java
@Component
public class MyComponentFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) 
            throws IOException, ServletException {
        HttpServletRequest req = (HttpServletRequest) request;
        System.out.println("Component Filter 실행: " + req.getRequestURI());
        
        chain.doFilter(request, response); // 다음 필터 또는 컨트롤러 실행
    }
}
```

@WebFilter + @ServletComponentScan 을 이용해서 등록록
특정 URL에 적용할 수 있음.
```java
import jakarta.servlet.Filter;
import jakarta.servlet.FilterChain;
import jakarta.servlet.FilterConfig;
import jakarta.servlet.ServletException;
import jakarta.servlet.ServletRequest;
import jakarta.servlet.ServletResponse;
import jakarta.servlet.annotation.WebFilter;
import java.io.IOException;

@WebFilter(urlPatterns = "/*") // 모든 요청에 적용
public class MyWebFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("WebFilter 초기화");
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) 
            throws IOException, ServletException {
        System.out.println("WebFilter 실행");
        
        chain.doFilter(request, response);
    }

    @Override
    public void destroy() {
        System.out.println("WebFilter 종료");
    }
}



```


```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletComponentScan;

@SpringBootApplication
@ServletComponentScan // @WebFilter, @WebListener 등의 서블릿 컴포넌트 자동 등록
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

`FilterRegistrationBean`을 이용하여 필터 등록
```java
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FilterConfig {

    @Bean
    public FilterRegistrationBean<MyFilter> loggingFilter() {
        FilterRegistrationBean<MyFilter> registrationBean = new FilterRegistrationBean<>();
        registrationBean.setFilter(new MyFilter()); // 필터 등록
        registrationBean.addUrlPatterns("/*"); // 모든 URL에 적용
        registrationBean.setOrder(1); // 필터 실행 순서 지정 (낮을수록 먼저 실행됨)
        return registrationBean;
    }
}
```
 

스프링 인터셉터
인터셉터는 Spring이 제공하는 기술로, 디스패쳐 서블릿이 컨트롤러를 호출하기 전/후 요청에 대해 부가적인 작업을 처리하는 객체입니다.

제공하는 메서드(3개 다 default method라 필요한거만 사용하면 됩니다)
preHandle : 핸들러가 실행되기 전에 실행, 비지니스 로직에서 공통적으로 처리해야 할 사항이 있으면 사용

postHandle : 핸들러가 실행된 후에 실행  파라미터로 model and view 가넘어옴, model and view에 추가적인 작업을 하고 싶을 때 사용

afterCompletion : 핸들러 이후에 실행되는 메서드  parameter로 exception 이 넘어오는데 비즈니스 로직에서  예외가 발생했거나, 어떤 리소스를 정리할때 사용할 수 있음

인터셉터 등록방법
WebMvcConfigurer 인터페이스를 구현한 클래스 내부에서
addInterceptors라는 메서드를 오버라이딩해서 사용할 수 있음.

스프링 인서텝트 실행 시점
핸들러 조회 -> 알맞은 핸들러 어댑터 가져옴 -> preHandle -> 핸들러 어댑터를 통해 핸들러 실행 -> postHandle -> view 관련 처리 -> afterCompletion

![[Pasted image 20250316134308.png]]
동작 방식
-doDispatch-
1. 핸들러 조회
getHandler 를 통해 핸들러를 조회 해서 알맞은 핸들러가지고 오면
핸드러뿐만 아니라 인터셉터 들이 리스트에 들어가 있음

 2. 핸들러를 처리할 수 있는 핸들러 어댑터 조회
3. 그다음에 우리가  인터셉터에서 구현한 prehandle들을 수행할 수 있는 applyPreHandle이라는 메서드가 실행됨
4. applyPreHandle 은 인터셉트들을 반복문을 돌면서 prehandle을 실행함. - 인터셉트에 등록된 순서로 실행
5. 그 다음 비즈니스 로직 실행  3, 4, 5번 과정
6. 비즈니스 로직 성공 후 applyPostHandle이 실행되면서 우리가 등록한 postHandle들이 실행됨
     - 인터셉터가 등록된 수선의 역순으로 실행
 7. 6번이 끝나면 processDispatchRresult 가 실행
 8. 우선 view관련 로직을 처리하고 triggerAfterCompletion이 실행하면서 afterCompletion을 처리함 - 이것도 역순으로 실행하는듯?


![[Pasted image 20250316135026.png]]
필터는 chain.doFilter를 호출해야함

왜 @ControllerAdvice??
이건 적용범위가 dISPATCHERsERVLET
인터셉터 자체가 DispatcherServlet에서 호출되기 때문에 @ControllerAdivce 관리 영역안에있음
필터는 DispathcherServlet 이전이나 종료된 다음에 실행되서 범위를 넘어감.

그래서 필터는 언제쓰고, 인터셉터는 언제써??

서블릿필터를 스프링빈으로 등록할 수 없었음 예전에는...
스프링에거 기본으로 제공하는 필터는
인코딩, Post처리 뿐 아니라 Put, Patch, Delete HTTP 메서드를 서블릿에서 할 수 있도록 wRAPPING 해주는 스프링이 아닌 전반적인 웹에 관련된 것들을 해줌.

인터셉터는 스프링 기술에 관련된 작업을 해줌..

관심사 분리

인가 처리 이런 스프링 관련 기술은 인터셉터, 웹에 관련된 거는 필터에서 처리
