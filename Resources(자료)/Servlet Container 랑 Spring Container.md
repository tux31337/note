## **1. 서블릿 컨테이너 (Servlet Container)**

서블릿 컨테이너는 **Java EE (Jakarta EE)에서 제공하는 웹 애플리케이션 실행 환경**입니다.

### **✔ 주요 역할**

1. **HTTP 요청 처리:**
    
    - 클라이언트(웹 브라우저)에서 들어오는 HTTP 요청을 받아서 적절한 서블릿(Controller 등)으로 전달합니다.
2. **서블릿 생명주기 관리:**
    
    - 서블릿 객체의 생성, 초기화, 실행, 소멸을 관리합니다.
3. **멀티스레딩 지원:**
    
    - 다중 요청을 처리하기 위해 내부적으로 스레드를 관리합니다.
4. **JSP 및 필터 지원:**
    
    - JSP(Java Server Pages)를 컴파일하고 실행하며, 필터(Filter)를 통해 요청과 응답을 가공할 수 있도록 합니다.

### **✔ 대표적인 서블릿 컨테이너**

- **Tomcat** (가장 많이 사용됨)
- Jetty
- Undertow
- WildFly (JBoss)
- GlassFish

---

## **2. 스프링 컨테이너 (Spring Container)**

스프링 컨테이너는 **스프링 프레임워크에서 제공하는 의존성 주입(DI)과 빈(Bean) 생명주기를 관리하는 컨테이너**입니다.

### **✔ 주요 역할**

1. **빈(Bean) 관리:**
    
    - 애플리케이션에서 사용되는 객체(빈)의 생성과 소멸을 관리합니다.
2. **의존성 주입(DI, Dependency Injection):**
    
    - 객체 간의 의존성을 자동으로 해결하여 코드의 결합도를 낮춰줍니다.
3. **AOP(Aspect-Oriented Programming) 지원:**
    
    - 공통 기능(로깅, 트랜잭션 등)을 분리하여 코드의 가독성을 높입니다.
4. **트랜잭션 관리:**
    
    - 데이터베이스 작업을 안정적으로 수행할 수 있도록 트랜잭션을 관리합니다.

### **✔ 스프링 컨테이너의 종류**

1. **BeanFactory** (경량 컨테이너, 일반적인 DI 제공)
2. **ApplicationContext** (BeanFactory + 다양한 기능 추가, 실무에서 주로 사용됨)
    - `AnnotationConfigApplicationContext` (Java Config 기반)
    - `XmlWebApplicationContext` (XML 설정 기반)
    - `GenericWebApplicationContext` (웹 애플리케이션용)

---

## **3. 서블릿 컨테이너와 스프링 컨테이너의 관계**

### **✔ 어떻게 함께 동작하는가?**

1. **서블릿 컨테이너가 먼저 실행됨**
    
    - 서블릿 컨테이너(Tomcat 등)가 실행되면서 웹 애플리케이션을 구동합니다.
2. **서블릿 컨테이너가 `DispatcherServlet`을 실행**
    
    - 서블릿 컨테이너는 `web.xml` 또는 `@WebServlet` 설정을 기반으로 **`DispatcherServlet`(스프링 프론트 컨트롤러)** 을 실행합니다.
3. **스프링 컨테이너(ApplicationContext) 생성**
    
    - `DispatcherServlet`이 실행되면서 스프링 컨테이너(ApplicationContext)가 초기화됩니다.
    - 이때, `Controller`, `Service`, `Repository` 등의 빈이 생성되고 관리됩니다.
4. **요청이 서블릿 컨테이너 → 스프링 컨테이너로 전달됨**
    
    - 클라이언트의 HTTP 요청이 들어오면 서블릿 컨테이너가 이를 받아 `DispatcherServlet`에 전달합니다.
    - `DispatcherServlet`은 스프링 컨테이너에서 알맞은 `Controller`를 찾아 요청을 처리합니다.

### **✔ 관계 정리**

|서블릿 컨테이너|스프링 컨테이너|
|---|---|
|HTTP 요청을 받아 서블릿 실행|`DispatcherServlet`을 실행하여 스프링 컨테이너 초기화|
|서블릿 생명주기 관리|스프링 빈(Bean) 생명주기 관리|
|JSP, 필터, 리스너 등을 관리|`Controller`, `Service`, `Repository` 빈을 관리|
|멀티스레드 HTTP 요청 처리|DI, AOP, 트랜잭션 등 다양한 기능 제공|

---

## **4. 정리**

- **서블릿 컨테이너는 웹 서버 역할을 하며, HTTP 요청을 처리하고 서블릿을 실행**합니다.
- **스프링 컨테이너는 애플리케이션의 객체(빈)를 관리하며, 비즈니스 로직을 실행하는 역할**을 합니다.
- **스프링 MVC 환경에서는 서블릿 컨테이너가 `DispatcherServlet`을 실행하면서 스프링 컨테이너(ApplicationContext)를 초기화**합니다.
- **즉, 서블릿 컨테이너는 스프링 컨테이너를 실행하는 역할을 하며, HTTP 요청이 서블릿 컨테이너 → 스프링 컨테이너로 전달되어 최종적으로 처리**됩니다.

👉 **서블릿 컨테이너는 웹 서버 역할, 스프링 컨테이너는 애플리케이션 관리 역할을 한다고 이해하면 쉽습니다!** 🚀