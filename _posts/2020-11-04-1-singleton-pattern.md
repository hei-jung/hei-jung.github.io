---
title: "[Design Pattern] Singleton"
permalink: /design-pattern/singleton/
categories: design-pattern
tags: sw-engineering design-pattern solution
---

싱글턴 패턴은 객체 인스턴스를 한 번만 생성하는 디자인 패턴이다.

## 특징

단 한 개의 인스턴스만 생성해서 프로그램 어디서든 접근할 수 있게 하는 기법이다.
즉, 생성자가 몇 번이나 호출되어도 실제로 생성되는 객체는 하나 뿐이며 최초 생성 이후에 호출된 생성자는 미리 생성해둔 객체를 반환한다.
공통된 객체를 여러 개 생성해서 써야 하는 DBCP(DataBase Connection Pool)와 같은 상황에서 주로 사용된다.

Java에서 싱글턴을 구현할 때는 생성자를 `private`으로 선언하고, `static getInstance()` 메서드를 통해 최초 생성된 객체를 받아오는 방식으로 한다.<br> 
참고로 Python 모듈은 그 자체로 싱글턴이다.

### 장점
- 프로그램이 시작될 때 최초 한 번만 메모리를 할당하므로, 메모리 낭비를 막을 수 있다.
  - 인스턴스 남발 방지
- 전역 인스턴스이기 때문에 서로 다른 클래스의 인스턴스들 간 데이터 공유에 용이하다.
  - 하나의 자원을 모두가 공유해서 써야 하는 경우 유리

### 단점
- 동시성 문제를 잘 고려해서 설계해야 한다.
  - 멀티스레드 환경에서의 동기화 처리
- 싱글턴 인스턴스를 너무 남발하면 서로 다른 클래스의 인스턴스들 간 결합도가 높아진다.
  - 객체 지향 설계 원칙에 위배 → 꼭 필요한 경우가 아니면 지양하자.

## 예제 코드

### 1. Eager Initialization

> 이른 초기화, Thread-safe

```java
public class Singleton {
	
	private static Singleton instance = new Singleton();
	
	private Singleton() {}
	
	public static Singleton getInstance() {
		return instance;
	}
}
```

### 2. Lazy Initialization

> 게으른 초기화, Thread-safe

`synchronized` 키워드를 사용해서 Thread-safe하게 만든다. 그런데 이 키워드를 사용하면 성능이 심하게 떨어져, 권장하지 않는다.

```java
public class Singleton {
	
	private static Singleton instance;
	
	private Singleton() {}
	
	public static synchronized Singleton getInstance() {
		if(instance == null) {
			instance = new Singleton();
		}
		return instance;
	}
}
```

### 3. Lazy Initialization + Double-checked Locking

> DCL, Thread-safe

위의 `synchronized` 키워드를 쓴 동기화 방식을 개선한 방식이다. 멀티스레드 환경에서도 인스턴스가 한 번만 생성된다.

```java
public class Singleton {
	
	private volatile static Singleton instance;
	
	private Singleton() {}
	
	public static Singleton getInstance() {
		if(instance == null) {
			synchronized(Singleton.class) {
				if(instance == null) {
					instance = new Singleton();
				}
			}
		}
		return instance;
	}
}
```

### 4. Lazy Initialization + Lazy Holder

> 게으른 초기화, Holder에 의한 초기화, Thread-safe

클래스 안에 Holder 클래스를 두는 방식이다.
동시성 문제에 있어 가장 성능이 뛰어나, 제일 많이 사용된다.

```java
public class Singleton {
	
	private Singleton() {}
	
	private static class LazyHolder {
		public static final Singleton INSTANCE = new Singleton();
	}
	
	public static Singleton getInstance() {
		return LazyHolder.INSTANCE;
	}
}
```

참고.
[싱글턴 패턴](https://medium.com/webeveloper/%EC%8B%B1%EA%B8%80%ED%84%B4-%ED%8C%A8%ED%84%B4-singleton-pattern-db75ed29c36),
[싱글톤 패턴을 쓰는 이유와 문제점](https://jeong-pro.tistory.com/86),
[싱글톤 패턴](https://gyoogle.dev/blog/design-pattern/Singleton%20Pattern.html)
