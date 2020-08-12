---
title: "안드로이드 기본"
categories: android java jekyll update
---

**안드로이드 프레임워크**

(Android Framework)


****중요한 components****

(1)activity -> 화면 페이지(sth that will be displayed on)

(2)service, (3)broadcast receiver -> 백그라운드 작업

(4)content provider -> 데이터를 공유하는 방법(to share data btw apps. 데이터를 제공해주는 쪽에서 구현)


cf. JNI(java native interface)


cf. XML의 역할

공공데이터 표현

컨테이너에게 description

안드로이드에서는 XML에 UI를 설계.

  - java코드가 아닌 자원 코드를 src 폴더에 따로 저장.
  - 자원을 자동으로 컴파일해서 자바에서 사용할 수 있는 객체 형태로 바꿔줌. 이렇게 변환해서 등록해놓는 코드가 R.java)
  - .xml 파일명엔 *대문자가 포함되면 x* / *숫자로 시작하면 x*


cf. 생명주기(life cycle) 메서드를 잘 알아야 해

-> 프레임워크 구조의 패턴에 맞게 코드를 작성하기 위함.


cf. intent

다른 액티비티를 깨워서 새 페이지를 활성화

--- broadcast receiver와 관련이 있다. broadcast receiver 동작 예> 문자메시지(알람이 울리다가도 메시지를 수신함.)

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
