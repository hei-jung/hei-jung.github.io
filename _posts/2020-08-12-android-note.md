---
title: "안드로이드 기본"
categories: android java
---

**안드로이드 프레임워크**
(Android Framework)


****중요한 components****

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


Component Lifecycles
===

1. Activity lifecycle
- active
- pause: 객체는 소멸되었지만 상태 정보는 유지.
- stop

(i) 액티비티 생명주기 메서드
- onCreate() // 액티비티 초기화 코드. 뷰 생성.
  - cf. Bundle: key와 value 저장. 안드로이드 개발에서 자주 쓰이는 일종의 Map.
  - setContentView(): XML로 만든 View 객체를 현재 Activity에 붙여준다.
