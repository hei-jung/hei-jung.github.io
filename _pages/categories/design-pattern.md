---
title: "Design Pattern"
permalink: /design-pattern/
layout: category
author_profile: true
taxonomy: design-pattern
---

## 목차

1. [디자인 패턴의 정의와 목적](#디자인-패턴의-정의와-목적)
2. [디자인 패턴 유형](#디자인-패턴-유형)
3. [디자인 패턴 포스트 전체 보기](#디자인-패턴-포스트-전체-보기)

## 디자인 패턴의 정의와 목적

디자인 패턴은 효율적인 소프트웨어 설계를 위한 일종의 설계 기법으로, GoF(Gang of Four)가 쓴 책 "디자인 패턴"을 통해 구체적으로 처음 제시되었다.<br>
특정한 알고리즘이 아니라, 소프트웨어 개발 시 자주 발생하는 반복적인 문제에 대한 해결 방법론이다.<br>

디자인 패턴을 활용하여 코드의 복잡도를 줄이고,
코드의 **재사용성**, **유지보수성**, **호환성**을 보장함으로써 프로그래밍 생산성을 높일 수 있다.<br>

## 디자인 패턴 유형

대표적인 유형으로는 GoF 외에도 MVC(Model View Controller) 등이 있다.<br>
여기서는 GoF 디자인 패턴을 정리해보고자 한다.
GoF는 디자인 패턴을 수행 목적에 따라 `생성 패턴`, `구조 패턴`, `행위 패턴`으로 분류한다.<br>

### 1. 생성 패턴(Creational Patterns)

객체의 생성 방식을 정해준다. 캡슐화와 관련이 있다.

- [Factory Method](https://hei-jung.github.io/design-pattern/factory-method/)
- Abstract Factory
- Builder
- Singleton
- Prototype

### 2. 구조 패턴(Structural Patterns)

객체를 조직화한다. 클래스나 객체의 구성과 관련이 있다.

- Adapter
- Bridge
- Composite
- Decorator
- Proxy

### 3. 행위 패턴(Behavioral Pattern)

클래스나 객체 간의 상호작용에 관여한다.

- Template Method
- Iterator
- Observer
- Strategy
- Visitor

---

디자인 패턴은 다루는 영역, 즉 적용 범위에 따라 `클래스 패턴`, `객체 패턴`으로 나눌 수도 있다.<br>

### 1. 클래스 패턴(Class Pattern)

클래스 간의 관계에 관여하며, 보통 서브클래스 상속을 통해 정의한다. 정적(static)인 패턴이다.<br>
(EX. Factory Method, Adapter, Template Method, Interpreter)

### 2. 객체 패턴(Object Pattern)

객체 간의 관계에 관여한다. 다른 객체에게 기능을 위임하는 것으로 정의할 수 있다. 프로그램 실행 중에 생성되어 동적(dynamic)이고 유연하다.

## 디자인 패턴 포스트 전체 보기
