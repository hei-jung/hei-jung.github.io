---
title: "[Design Pattern] Observer"
permalink: /design-pattern/observer/
categories: design-pattern
tags: sw-engineering design-pattern solution
toc: true
toc_sticky: true
---

참고.
[위키백과](https://ko.wikipedia.org/wiki/%EC%98%B5%EC%84%9C%EB%B2%84_%ED%8C%A8%ED%84%B4),
[디자인패턴 - 옵저버 패턴(2016.01.25 작성)](https://flowarc.tistory.com/entry/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%98%B5%EC%A0%80%EB%B2%84-%ED%8C%A8%ED%84%B4Observer-Pattern),
[디자인패턴 - 옵저버 패턴(2016.05.03 작성)](https://jusungpark.tistory.com/8),
[옵저버 패턴 아주 간단하게 정리해보기(2019.10.07 작성)](https://pjh3749.tistory.com/266)
[옵저버 패턴(2019.03.09 작성)](https://coloredrabbit.tistory.com/86),
[감시자 패턴(2012.12.09 작성)](https://kimsunzun.tistory.com/entry/Observer%EA%B0%90%EC%8B%9C%EC%9E%90%ED%8C%A8%ED%84%B4)

## 특징

옵저버 패턴은 객체의 상태 변화와 관련이 있는 디자인 패턴이다.<br> 
주체 객체(Subject)에 관찰자 객체(Observer 또는 Listener)들과의 의존 관계를 정의해둔다.
주체 객체가 자신의 상태가 변할 때마다 관찰자들에게 메서드 등을 통해서 상태 변화를 직접 알려, 관찰자들이 변화에 대해 자동으로 갱신될 수 있도록 한다.<br>
이러한 동작 방식으로, Publish/Subscribe 모델로도 알려져 있다.
주로 분산 이벤트 핸들러를 구현할 때 사용된다. 즉, UI 설계할 때 많이 쓰인다. 예를 들어서 안드로이드 앱의 버튼 OnClickListener 같은...?

Java에서는 옵저버 패턴을 적용한 java.util.Observable 클래스를 기본 제공하고 있다.

### 구성

```
- 상태를 가지고 있는 주체 객체: Subject
- 상태 변화를 알아야 하는 관찰자 객체: Observer
```

주체 객체와 관찰자 객체의 관계는 1대1이 될 수도 있고, 1대多가 될 수도 있다.

(장점)<br>
- 직관적으로 이해할 수 있다.
- 인터페이스를 이용하여 클래스나 객체 간 의존도가 낮아진다.
  - 결합도↓↓
- 객체들 간 주고받는 정보 단위가 크거나, 객체의 규모가 크고 객체 간의 관계가 복잡할 때 유리하다.

(단점)<br>
- Thread-safe하지 않다.
  - 비동기 방식이므로, 이벤트를 제때 못 받을 수도.
- Observer 객체를 제때 제거하지 않으면 메모리 누수가 일어날 수도 있다.
- 너무 자주 쓰면 상태 관리가 어려워진다.
- 서로 다른 Observer 객체에 의해 동일한 변경 작업이 반복 수행되기도 한다.

## 예제 코드

옵저버 객체 정의

```java
public interface Observer {
	void update(String message);//정보 갱신
}
```

주체 객체 정의

```java
public interface Publisher {
	void subscribe(Observer observer);//옵저버 등록
	void unsubscribe(Observer observer);//옵저버 제거
	void notifyObserver(String message);//상태 변화 알림
}
```

주체 객체 복실이 구현: 상태 변화 정보 제공하는 publisher 

```java
public class Boksil implements Publisher {
	//참고. 복실이는 우리집 고양이 이름이다.
	private List<Observer> observers;
	
	public Boksil() {
		observers = new ArrayList<>();
	}
	
	public void eat() {
		System.out.println("맘먀우");
		notifyObserver("맘마를 달라");
	}
	
	public void play() {
		System.out.println("아오오오우");
		notifyObserver("사냥놀이 한다");
	}
	
	public void kneading() {
		System.out.println("골골골");
		notifyObserver("꾹꾹이 하는 중");
	}
	
	@Override
	public void subscribe(Observer observer) {
		observers.add(observer);
	}
	
	@Override
	public void unsubscribe(Observer observer) {
		observers.remove(observer);
	}
	
	@Override
	public void notifyObserver(String message) {
		observers.forEach(observer->observer.update(message));
	}
}
```

복실이의 울음소리를 관찰하는 집사들

```java
public class Me implements Observer {
	@Override
	public void update(String message) {
		System.out.println("내가 들은 복실이 소리: " + message);
	}
}
```

```java
public class Mom implements Observer {
	@Override
	public void update(String message) {
		System.out.println("엄마가 들은 복실이 소리: " + message);
	}
}
```

main 함수에서 적절한 실행코드 구현:

```java
public class Main {
	public static void main(String[] args) {
		Boksil boksil = new Boksil();
		Me me = new Me();
		Mom mom = new Mom();
		
		//옵저버 등록(구독)
		boksil.subscribe(me);
		boksil.subscribe(mom);
		
		boksil.eat();
		
		boksil.unsubscribe(me);//옵저버 제거(구독 해지)
		
		boksil.play();
		boksil.kneading();
	}
}
```