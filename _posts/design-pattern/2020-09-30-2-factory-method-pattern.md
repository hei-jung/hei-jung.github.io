---
title: "[Design Pattern] Factory Method"
permalink: /design-pattern/factory-method/
categories: design-pattern
tags: sw-engineering design-pattern solution
---

팩토리 메서드 패턴은 객체지향 디자인 패턴이다.<br>
객체지향 참고: [Object Orient Basics](https://hei-jung.github.io/oop/)<br>

## 특징

상위 클래스의 구체적인 처리를 하위 클래스에서 결정하도록 하는 기법으로, 주로 Java에서 많이 쓰인다.

### 장점

- 객체 간 결합도↓↓
- 유지보수 용이
  - 객체를 한 곳에서 관리하므로, 객체의 생명주기 관리가 쉽다.
- 인터페이스를 바탕으로 하여 유연하고 확장성 있는 구조
- 디자인 원칙 준수: 구상 클래스에 의존하지 않는다.

### 단점
- 자칫 불필요하게 많은 클래스를 정의할 수 있다.

## 예제 코드

팩토리 메서드를 Bread라는 인터페이스에 정의한다.

```java
public interface Bread {
	// recipe 설정하는 추상 메소드
	public Map<String, Integer> getRecipe();
}
```

Bread 인터페이스를 BreadFactory에서 간접적으로 생성한다.

```java
public class BreadFactory {
	public Bread getBread(String breadType) {
		if (breadType == null) {
			return null;
		}
		switch (breadType) {
		case "cream":
			return new Cream();
		case "sugar":
			return new Sugar();
		case "butter":
			return new Butter();
		}
		return null;
	}
}
```

자식 클래스마다 Bread 인터페이스에 대한 구체적인 처리를 구현한다.

```java
public class Cream implements Bread {
	@Override
	public Map<String, Integer> getRecipe() {
		Map<String, Integer> recipe = new HashMap<String, Integer>();
		recipe.put("flour", 100);
		recipe.put("water", 100);
		recipe.put("cream", 200);
		return recipe;
	}
}
```

```java
public class Sugar implements Bread {
	@Override
	public Map<String, Integer> getRecipe() {
		Map<String, Integer> recipe = new HashMap<String, Integer>();
		recipe.put("flour", 100);
		recipe.put("water", 50);
		recipe.put("sugar", 200);
		return recipe;
	}
}
```

```java
public class Butter implements Bread {
	@Override
	public Map<String, Integer> getRecipe() {
		Map<String, Integer> recipe = new HashMap<String, Integer>();
		recipe.put("flour", 100);
		recipe.put("water", 100);
		recipe.put("butter", 50);
		return recipe;
	}
}
```

### 실행 결과

```java
// 팩토리 클래스에서 객체 생성
BreadFactory breadFactory = new BreadFactory();
// breadType: cream, sugar, butter
System.out.println("bread type 1: cream");
String breadType = "cream";
Bread bread = breadFactory.getBread(breadType);
// 팩토리 메소드로부터 recipe 받아옴
Map<String, Integer> recipe = bread.getRecipe();

System.out.println("recipe");
for (String key : recipe.keySet()) {
	int value = recipe.get(key);
	System.out.println(key + ": " + value);
}
```

![cap1](https://user-images.githubusercontent.com/40985307/94659597-81c61f00-033f-11eb-8756-1c1e62be249a.png)

