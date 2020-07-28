---
title: "IOC 란?"
date: 2020-07-23 15:44:00 -0400
categories: devpome83 update.
---

프로그램의 제어 흐름구조를 바꾸는 것이다.
일반적으로 main() 과 같은 프로그램이 시작되는 지점에서 다음에 사용할 오브젝트를 결정, 생성, 호출하는 작업이 수행된다.
이런 구조에서 각 오브젝트는 프로그램 흐름을 결정하게 된다.
즉, 모든 종류의 작업을 사용하는 쪽에서 제어하는 구조이다.

반면, Ioc 는 제어흐름의 개념을 바꾸어
오브젝트가 자신이 사용할 오브젝트를 스스로 선택하거나 생성하지 않는다. 또한 자신도 어떻게 만들어지고 어디에 사용되는지 알 수 없다.
오브젝트의 결정, 생성, 호출되는 모든 제어권을 다른 대상(컨테이너)에게 위임하여 작업이 수행되는 것이다.

## 1. DL (Dependency Lookup) 의존성 검색

## 2. DI (Dependency Injection) 의존성 주입

    각 클래스 사이에 필요로 하는 의존 관계를 컨테이너가 자동으로 생성하여 연결시켜 줌.
    => 빈 설정(Bean definition) 정보를 기반으로 자동으로 연결.

-   Constructor Injection
    생성자를 이용하여 클래스 사이의 의존관계를 연결
-   Setter Injection
    setter 메소드를 이용하여 클래스 사이의 의존관계를 연결
-   Method Injection
    Singleton 인스턴스와 Non Singleton 인스턴스의 의존관계를 연결

# 용어 정리

## 1. Bean

스프링에서 제어권을 가지고 의존 관계를 부여하는 오브젝트

## 2. Bean Factory

스프링의 IoC를 담당하는 핵심 컨테이너

## 3. Application Context

Bean Factory 를 확장한 IoC 컨테이너

## 4. Configuration metadata

Application Context 또는 Bean Factory가 IoC를 적용하기 위해 사용하는 메타정보

## 5. Container (IoC Container)

IoC 방식으로 Bean 을 관리한다는 의미에서 Bean Factory 또는 Application 를 통칭
