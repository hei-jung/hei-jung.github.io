---
title : "Android Receiver"
categories: java android
---

## Broadcast Receiver

- UI가 없다. -> 알림을 위해선 dialogue box, toast, notification 등 이용.
- 객체를 직접 생성해서 쓰는 게 가능하다. -> Manifest 파일에 등록하지 않아도 됨!
- 생명주기 메서드
  - onReceive()
- 백그라운드 대기 -> intent 호출 시 동작
- resource가 부족한 경우 직접 kill 가능

- 생성 방법

> New>Other>Broadcast Receiver

- Broadcast Receiver 클래스를 생성하면 Manifest에 자동으로 추가된다.

```xml
<!-- Broadcast Receiver 파일 생성 시 자동 등록 -->
<receiver
    android:name=".MyReceiver"
    android:enabled="true"
    android:exported="true" />
```

__Broadcast Receiver의 호출 방식__

(1) 명시적

> MyReceiver

```java
String str = intent.getStringExtra("str");
int num = intent.getIntExtra("num", 0);
Toast.makeText(context, "str: " + str + " / num: " + num, Toast.LENGTH_SHORT).show();
```

> MainActivity

```java
public void onBtn1(View view) {
    // 호출할 receiver의 이름을 직접 부른다.
    Intent intent = new Intent(this, MyReceiver.class);
    intent.putExtra("str", "aaa");
    intent.putExtra("num", 10);
    sendBroadcast(intent);
}
```

(2) 묵시적

> Manifest 파일의 MyReceiver2에 대한 Receiver 태그를 지운다.

> MyReceiver2

```java
Toast.makeText(context, "implicit activation to receiver", Toast.LENGTH_SHORT).show();
```

> MainActivity

- 멤버 변수

```java
// 묵시적 호출 시 실행할 action (따로 정해져 있는 게 아니라 직접 정의)
public final String ACTION = "com.example.receivertest.MyAction";
```

- onCreate

```java
// broadcast receiver 객체 생성
MyReceiver2 br = new MyReceiver2();
IntentFilter filter = new IntentFilter();
filter.addAction(ACTION);
registerReceiver(br, filter);
```

- 실행

```java
public void onBtn2(View view) {
    Intent intent = new Intent(ACTION);
    sendBroadcast(intent);
}
```
