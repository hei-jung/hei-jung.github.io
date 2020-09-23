---
title: "Android Basics"
categories: java android
---

안드로이드 프레임워크(Android Framework)
===

> 중요한 components

- (1)activity -> 화면 페이지(sth that will be displayed on)
- (2)service, (3)broadcast receiver -> 백그라운드 작업
- (4)content provider -> 데이터를 공유하는 방법(to share data btw apps. 데이터를 제공해주는 쪽에서 구현)

cf. JNI(java native interface)

cf. XML의 역할
- 공공데이터 표현
- 컨테이너에게 description
- 안드로이드에서는 XML에 UI를 설계.
    - java코드가 아닌 자원 코드를 src 폴더에 따로 저장.
    - 자원을 자동으로 컴파일해서 자바에서 사용할 수 있는 객체 형태로 바꿔줌. 이렇게 변환해서 등록해놓는 코드가 R.java)
    - .xml 파일명엔 *대문자가 포함되면 x* / *숫자로 시작하면 x*

cf. 생명주기(life cycle) 메서드를 잘 알아야 해
-> 프레임워크 구조의 패턴에 맞게 코드를 작성하기 위함.

cf. intent: 컴포넌트 활성화시키는 객체
- use: 다른 액티비티를 깨워서 새 페이지를 활성화
- broadcast receiver와 관련이 있다. broadcast receiver 동작 예> 문자메시지(알람이 울리다가도 메시지를 수신함.)

cf. manifest file: 권한 설정, 추가. 프로그램에 대한 명세서.


## Component Lifecycles

**1. Activity lifecycle**
- active
- pause: 객체는 소멸되었지만 상태 정보는 유지.
- stop

(i) 액티비티 생명주기 메서드
- onCreate() // 액티비티 초기화 코드. 뷰 생성.
  - cf. Bundle: key와 value 저장. 안드로이드 개발에서 자주 쓰이는 일종의 Map.
  - setContentView(): XML로 만든 View 객체를 현재 Activity에 붙여준다.
  
(ii) activity_main.xml
- Attribute id: Attribute을 부를 이름. 속성들은 R 클래스(R.java)에 저장된다. 따라서 .java 파일에서 접근하려면 *R.id.정의한id*라고 쓴다.


## UI

**1. 이벤트 리스너(event listener)**

: 이벤트의 발생을 기다리다가 이벤트가 발생하면 핸들러를 호출. 항상 귀를 기울이고 있다 해서 listener. 핸들러에 대한 추상 클래스가 있음. 여기에 이벤트 발생에 대한 동작을 구현.

-> setOnTtemClickListener()에 붙여줌. 여기에 붙여야, listView에 대해 동작하게 됨.

**2. 메뉴**
- 상단의 : 누르면 나타나는 메뉴 - options 메뉴
- view를 길게 누르면 나타나는 메뉴 - context 메뉴


**Adapter**

getView(position, convertView, parent): Adapter에서 가장 중요한 메서드 (--이하 parameter 설명--)
  - position: 처리할 데이터의 위치. 해당 위치의 데이터를 읽어 리소스로 지정한 뷰로 생성하여 반환


## Intent

- Activity를 이동한다는 것은, 다른 Activity를 활성화시키는 것! ---> **Intent**
  - 안드로이드는 현재 프로그램 외에도 다른 프로그램을 불러올 수 있음
  - 다른 프로그램은 분리된 프로세스에 있으므로, 메모리 자원을 공유하지 않음
  - 직접적이지 않은 다른 방식으로 지목을 해서 깨워야 함
    => 묵시적인(implicit) 방법 - 조건 사용: intent filter, 상수 코드 사용 (명시적인 방법 - intent. 이동할 Activity 클래스 명시)
    EX. AndroidManifest.xml 안에서 활성화할 액티비티에 intent-filter 태그 사용
```xml
    <intent-filter>
      <action></action>
      <data></data>
      <category></category>
    </intent-filter>
```

cf. URI: 자원의 위치

- getIntent(): Activity 클래스에서 제공하는, intent를 받아오는 메서드. 현재 Activity를 활성화하려고 던진 intent를 받아옴.
- finish(): 현재 Activity를 종료하는 메서드 -> 이전 Activity로 되돌아감.
  - putExtra(): 종료하면서 Activity 정보를 담아서 보내줌
  - setResult(): 처리 결과가 정상/비정상인지 보내줌
- Intent intent = new Intent(packageContext, target 액티비티명): 타깃 액티비티를 **명시**
- startActivity(intent): target으로 지정한 intent로 이동
- startActivityForResult(intent, requestCode)
  - requestCode: 어느 액티비티 다녀왔는지 표시
- onActivityResult(requestCode, resultCode, data)
  - startActivityForResult()로 다른 액티비티 다녀오면 자동 호출됨. 다녀온 액티비티 처리 결과를 현재 액티비티에 적용
  - requestCode: 어느 액티비티 다녀왔는지 확인
  - resultCode: 다녀온 액티비티의 처리 결과가 정상인지 아닌지 학인
  - data: 다녀온 액티비티 쪽에서 처리 결과를 담아 보낸 intent
    
cf. 왜 어떨 땐 this 쓰고 어떤 땐 액티비티명.this 쓰지?
  - 이벤트 핸들링을 하는 경우엔 context의 혼동이 있을 수도 있어, 액티비티명.this라고 쓴다.
  - 반면, 단순히 액티비티를 이동하는 경우에는 프로그램이 context를 헷갈려하지 않기 때문에 그냥 this라고 써도 무방하다.
