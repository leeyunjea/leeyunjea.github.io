---
layout: post
title: "Spring 기본 개념"
date: 2019-05-31
tags: [spring]
categories:
  - SPRING
description: "Spring의 IoC, DI, AOP, POJO, MVC를 알아본다."
image: /image/spring/spring-core-tutorial.png
image-sm: /image/spring/spring-framework.png

---



<br>
### IoC
<br />
 - Inversion of Control 의 약자로 제어의 역전이라는 말이다. 메소드나 객체의 호출작업을 개발자가 결정하는 것이 아니라 외부에서 결정되는 것을 의미한다.
 - 이 방식은 대부분의 프레임워크에서 사용하는 방법으로, 개발자는 필요한 부분을 개발해서 끼워 넣기의 형태로 개발하고 실행하게 된다.
 - IoC 개념은 스프링에서만 사용하는 것은 아니고 대부분의 프레임 워크에서 사용되고 있다. 유독 스프링에서 IoC를 강조하는 이유는 스프링은 그 자체가 IoC로 설계되어 있기 때문이다.
<br>

<br />
### DI
<br />
- Dependency Injection 의 약자로 의존성 주입이라는 의미이다.
- 의존성 주입은 제어의 역행이 일어날 때 스프링이 내부에 있는 객체들간의 관계를 관리할 때 사용하는 기법이다.
- 의존적인 객체를 직접 생성하거나 제어하는 것이 아니라, 특정 객체에 필요한 객체를 외부에서 결정해서 연결시키는 것을 의미한다.
- 우리는 클래스의 기능을 추상적으로 묶어둔 인터페이스를 갖다 쓰면 되는 것이다. 나머지는 스프링에서 객체를 주입해주기 때문이다.
- 의존성 주입으로 인해 모듈 간의 결합도가 낮아지고 유연성이 높아진다.
<br><br>

### AOP
<br />
 - Aspect Oriented Programming 의 약자로 관점지향 프로그래밍이다.
 - IoC가 낮은 결합도와 관련되 있다면 AOP는 높은 응집도와 관련되어 있다.
 - 서비스들의 비즈니스 메소드들은 복잡한 코드로 구성되어 있는데, 그 중 핵심 로직은 얼마 안되고 대부분은 트랜잭션, 로깅 처리, 인증과 관련된 코드들이 있을 수 있다. 이럴 때 비핵심적이지만 꼭 필요하고, 공통화할 수 있는 부분을 따로 빼서(횡단분리) 관리하는 것이다.

 {:.post-img.small}
![producer](/image/spring/aop.jpeg)

<br>
### POJO
<br />
 - Plain old java object의 약자로 단일 책임 원칙을 지키는 클래스이다.
 - 특정 규약에 종속되지 않는다.
    - 단일 상속 제한 때문에 객체지향적인 설계기법 적용하기 쉬워진다.
    - 다른 환경으로의 이전이 쉽다.
- 특정 환경에 종속되지 않는다.
    - 다른 환경에서 사용하기 쉽다.
    - 비즈니스 로직과 기술적인 내용을 담은 웹정보 코드가 분리되어 이해하기 쉬워진다.
    - 웹서버에 올리지 않고 독립적으로 테스트하기 쉽다.
- 단일 책임 원칙을 지키는 클래스
    - 즉, POJO란 객체지향적인 원리에 충실하면서, 특정 환경과 규약에 종속되지 않아 필요에 따라 재사용될 수 있는 방식으로 설계된 오브젝트이다.
<br /><br />

### MVC 패턴
<br />
 - 스프링은 웹 프로그래밍 개발 시 거의 표준적인 방식인 Spring MVC라 불리는 모델-뷰-컨트롤러 패턴을 사용한다.
 - Dispatcher Servlet이 Controller 역할을 담당하여 각종 요청을 적절한 서비스에 분산시켜주며 이를 각 서비스들이 처리를 하여 결과를 생성하고 그 결과는 다양한 형식의 View 서비스들로 화면에 표시될 수 있다.
{:.post-img.small}
![producer](/image/spring/mvc2.png)

#### Model-View-Controller
<br>
- Model
    - 응용 프로그램 데이터를 캡슐화하고 일반적으로 데이터들은 POJO를 포함한다.
- View
    - 모델 데이터 렌더링을 담당하며 일반적으로 HTML 출력을 생성한다.
- Controller
    - 사용자 요청을 처리하고 적절한 모델을 작성하고이를 렌더링하기 위해 뷰에 전달한다.

<br />

### DAO 와 DTO의 차이
<br />
 - DAO(Data Access Object)란?
    - 실제로 DB에 접근하는 객체이다.
    - Service와 DB를 연결하는 고리의 역할을 한다.
    - SQL을 사용하여 DB에 접근한 후 적절한 CRUD APi를 제공한다.
 - DTO(Data Transfer Object)란?
    - 계층간 데이터 교환을 위한 객체(Java Bean)이다.
    - Request 와 Response용 DTO는 View를 위한 클래스이다.

<br /><br />