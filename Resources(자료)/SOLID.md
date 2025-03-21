### SRP(Single responsibility principle) : 단일 책임 원칙
- 하나의 클래스는 하나의 책임만 가져아 한다.
- 변경이 있을 경우 다른 부분에 미치는 영향이 최소화되어야 있으면 SRP를 잘 지킨 것 
  변경이 있을 때 하나의 클래스 하나의 지점만 바뀌면 잘 지킨 것.

### OCP(Open/closed principle) : 개방-폐쇄 원칙
- 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.
- 구현 객체를 변경하더라도 클라이언트 코드에 변경이 없게끔 설계를 해야한다.
- 다형성과 DI 를 활용해 외부에서 의존성을 주입하면 확장은 되지면 주입을 받는 코드는 변경하지 않을 수 있어서 지킬 수 있다. 
- 위를 지키려면 객체를 생성하고, 연관관계를 맺어주는 별도의 조립, 설정자가 필요하다. 결국 DI와 IoC가 필요하다.

### LSP (Liskov substitution principle) : 리스코프 치환 원칙
- LSP는 객체 지향 프로그래밍에서 상속을 올바르게 사용하는 중요한 원칙
- 자식 클래스는 부모 클래스의 기능을 변경하지 않고 확장해야 한다
- 부모 클래스를 기대하는 코드에서 자식 클래스로 대체해도 프로그램이 정상적으로 동작해야 함

### ISP(Interface segregation principle) : 인터페이스 분리 원칙
- 하나의 커다란 인터페이스보다는 여러 개의 작은 인터페이스로 분리하여 필요한 기능만 구현하도록 설계해야 한다
- 인터페이스가 명확해지고, 대체 가능성이 높아짐

### DIP(Dependency inversion Principle) 의존관계 역전 원칙
- 추상화(interface) 에 의존해야지, 구체화(class)에 의존하면 안된다.
- 구체적인 구현이 아닌, 인터페이스(추상화)에 의존하도록 설계하는 원칙
- 테스트가 용이하고, 코드 변경 시 영향이 최소화되고 유지보수성이 향상됨.



