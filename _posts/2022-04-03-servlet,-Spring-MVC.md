---
title: Servlet, Spring MVC
author: hj
date: 2022-04-03 16:10:00 +0800
categories: [Spring, MVC]
tags: [Spring]
render_with_liquid: false
---

### 스프링 컨테이너

자바 객체의 생명 주기를 관리하며, 생성된 자바 객체들에게 추가적인 기능을 제공하는 역할

여기서 자바 객체를 스프링에서는 빈(Bean)이라고 부름

IoC 컨테이너에 의해 관리되는 객체를 빈이라고 함

IoC는 종속성 주입(DI)라고도 함

스프링 컨테이너의 종류는 `Bean Factory`와 이를 상속한 `ApplicationContext` 2가지가 존재한다.

### Bean Factory

Bean Factory는 스프링 설정파일에 등록된 Bean 객체를 생성하고 관리하는 기본적인 기능만 제공한다.

컨테이너가 구동될 때 Bean 객체를 생성하는 것이 아니라 클라이언트의 요청에 의해서 Bean 객체가 사용되는 시점(Lazy Loading) 에 객체를 생성하는 방식을 사용하고 있다.

일반적으로 스프링 프로젝트에서는 사용될 일이 없다.

### Application Context

Application Context는 Bean Factory를 상속받고 있다.

Bean Factory와 마찬가지로, Bean 객체를 생성하고 관리하는 기능을 가지고 있다.

### Servlet

Dynamic Web Page를 만들 때 사용되는 자바 기반의 웹 애플리케이션 프로그래밍 기술

웹 기반의 요청에 대해 동적으로 처리해주는 역할로 Server Side에서 작동

클라이언트가 요청하면 그 결과를 다시 전송해주는 역할

요청 당 쓰레드 사용

- CGI (Common Gateway Interface)

  Servlet 등장 이전에 사용하던 기술로, 요청 당 프로세스를 만들어 사용


**장점 (CGI와 비교)**

- 빠름
- 플랫폼 독립적
- 보안
- 이식성

### 서블릿 컨테이너

서블릿은 요청을 받거나 내보낼 때, 자신이 알아서 하는 것이 아니라 서블릿을 실행시키는 주체인 서블릿 컨테이너를 이용한다.

Servlet container는 어떤 요청에 대해 어떤 Servlet을 실행할 것인지 제어

즉, 서블릿이 웹 서버와 통신할 수 있도록 해준다.

**주요 기능**

- 서블릿의 생명주기 관리
- 웹 서버와 소켓을 만들어서 통신
- 멀티스레딩 관리
- XML을 통한 선언적인 보안 관리

대표적인 서블릿 컨테이너에는 톰캣, 제티 등이 있다.

### Dispatcher Servlet

Servlet Container에서 HTTP 프로토콜을 통해 들어오는 모든 요청을 처리하기 위해 첫 번째로 실행되는 Servlet (Front Controller)

공통 작업을 처리한 후 적절한 세부 컨트롤러로 작업을 위임해준다.

Dispatcher가 받은 요청은 HandlerMapping으로 넘어간다.

**Dispatcher Servlet의 흐름**

![dispatcher_servlet](/assets/img/dispatcher_servlet.png)

**장점**

- web.xml의 역할 축소

  ⇒ 해당 애플리케이션으로 들어오는 모든 요청을 dispatcher servlet이 핸들링

- @MVC 사용 가능

  ⇒ MVC 패턴에 맞게끔 애플리케이션을 개발하도록 유도


**단점**

모든 요청을 Controller로 넘겨주는 방식이 효율적으로 보이지만, 정적 파일에 대한 요청까지 Controller로 넘겨버리는 단점이 있다.

**문제 해결 방법**

1. 정적 자원에 대한 요청과 애플리케이션에 대한 요청을 분리
  - /apps 의 URL로 접근하면 Dispatcher Servlet이 담당
  - /resources 의 URL로 접근하면 Dispatcher Servlet이 컨트롤할 수 없으므로 담당 X

   ⇒ 모든 요청에 대해 위의 URL을 붙여줘야 하기 때문에 코드가 지저분해질 뿐더러 직관적인 설계가 될 수없음

2. 애플리케이션에 대한 요청을 탐색하고 없으면 정적 자원에 대한 요청으로 처리
  - 모든 요청을 컨트롤러에 해두고 Dispatcher Servlet에서 해당 요청에 대한 컨트롤러를 찾을 수 없는 경우, 2차적으로 설정된 resource 경로를 탐색하여 자원을 찾아냄

   ⇒ 영역 분리로 인한 효율적인 resources 관리, 추후 확장 용이


Spring의 경우

web.xml에서 Dispatcher Servlet 설정. 그 외의 다른 servlet도 마찬가지로 web.xml에 설정

Spring Boot의 경우

web.xml이 아닌 java config 사용

spring boot에서는 Dispatcher Servlet이 기본으로 설정되어 있어서 설정을 하지 않아도 됨

### **Spring MVC**

Model : 도메인 객체 또는 DTO로 화면에 전달할 또는 전달 받은 데이터를 담고 있는 객체

View : 데이터를 보여주는 역할. HTML, JSP, thymeleaf

Controller : 사용자의 입력을 받아 모델 객체의 데이터를 변경하거나, 모델 객체를 뷰에 전달하는 역할

**장점**

- 동시 다발적 개발 ⇒ 프론트, 백 독립적으로 개발 가능
- 높은 결합도 ⇒ 논리적으로 관련있는 기능을 하나로 묶거나, 특정 모델과 관련있는 뷰를 그룹화 할 수 있다.
- 낮은 의존도 ⇒ model, view, controller는 각각 독립적
- 개발 용이성 ⇒ 책임이 구분되어 있어 수정 용이함

**단점**

- 코드 네비게이션 복잡함
- 코드 일관성 유지에 노력 필요
- 높은 학습 곡선
