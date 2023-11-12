---
title: "[Android] Connecting Android App to Web Server(1)"
categories: playdata-android
tags: android java
---

[어제 수업내용 및 코드](https://hei-jung.github.io/android/java/android-x-web-app/)
<details>
<summary>요약 접기/펼치기</summary>
<h3>웹 서버 통한 로그인 기능</h3>
- Eclipse에서 web_and라는 이름의 웹 서버를 만들었다.<br>
- 여기서 아이디가 aaa이고 비밀번호가 111일 때만 로그인 성공(flag <- true)이 되도록 구현하였다.<br>
- 앱에서 로그인 액티비티 실행하면 flag를 확인해 Toast를 띄우도록 하였다.<br>
- 위의 동작을 위해 만든 클래스: WebViewModel, RequestHttpURLConnection
</details>

## 로그인 세션 유지

웹 실습할 때, 로그인을 유지하기 위해 session(세션)을 사용했었다.

### <동작원리>
- 서버와 클라이언트를 구분하기 위해, 중복되지 않은 난수를 쿠키에 담아서 클라이언트로 전송! (웹서버도 일종의 클라이언트.)
- 클라이언트는 요청을 보낼 때마다 request header에 세션id를 담음
- 이 세션id를 통해 요청 구분. 즉, 세션id는 주민등록번호 같은 것!!!

```
Web Server
---
session_id: 0001 <- A(id:aaa)
session_id: 0002 <- B(id:bbb)
session_id: 0003 <- C(id:ccc)
```

- session 번호가 0001이 온다 => Web Server에 aaa
- 각각의 개별적인 id가 서로 다른 메모리에 할당되어 저장됨.


코드
---

#### 1. 서버

> login Servlet 수정

```java
if (id.equals("aaa") && pwd.equals("111")) {
  flag = true;
  HttpSession session = request.getSession(); // 추가
  session.setAttribute("id", id); // 추가
}
```

> MyInfo Servlet 추가, doGet

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {
  // TODO Auto-generated method stub
  System.out.println("myinfo");
  HttpSession session = request.getSession(false);
  String id = (String) session.getAttribute("id");

  System.out.println("login id:" + id);
  request.setAttribute("loginid", id);
  RequestDispatcher rd = request.getRequestDispatcher("views/myinfo.jsp");
  rd.forward(request, response);
}
```

> WebContent>views>myinfo.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=EUC-KR"
	pageEncoding="EUC-KR"%>
{"loginid":"${loginid}"}
```


#### 2. 안드로이드

> RequestHttpURLConnection 수정

```java
// request 파라미터에 문자열 method 추가
public String request(String _url, String method, ContentValues _params) {
  HttpURLConnection urlConn = null;
  StringBuffer sbParams = new StringBuffer();
  /* 아래 코드 추가 */
  method = method.toUpperCase();
  if (method == null || method.equals("") || !method.equals("POST")) {
      method = "GET";
  }
  //...
}  
```

└ 요청 방식에 따라 다르게 처리할 수 있도록 method를 확인하는 코드를 추가했다.

```java
/**
 * 2. HttpURLConnection을 통해 web의 데이터를 가져온다.
 * */
try {
    URL url = new URL(_url);
    urlConn = (HttpURLConnection) url.openConnection();

    // [2-1]. urlConn 설정.
    urlConn.setRequestMethod(method); // setRequestMethod안의 파라미터를 "POST"에서 method로 수정
    urlConn.setRequestProperty("Accept-Charset", "UTF-8");
    urlConn.setRequestProperty("Context_Type", "application/x-www-form-urlencoded;cahrset=UTF-8");

    /* 아래 코드 추가 */
    // session ID가 있으면 요청 헤더에 추가
    if (!MainActivity.session_id.equals("")) {
        urlConn.setRequestProperty("Cookie", MainActivity.session_id);
    }

    //...
```

```return page;``` 위에 다음 코드 추가.

```java
/* 추가 */
if (MainActivity.session_id.equals("")) {
    List<String> cookies = urlConn.getHeaderFields().get("Set-Cookie");
    // cookies -> [난수;Path=/smartudy;HttpOnly] -> []가 쿠키1개
    // Path: 쿠키가 유효한 경로, /smartudy의 하위 경로에 위의 쿠키를 사용 가능.
    if (cookies != null) {
        for (String cookie : cookies) {
            MainActivity.session_id = cookie.split(";")[0];
            System.out.println("session:" + MainActivity.session_id);
        }
    }
}

return page;
```

참고.<br>
세션은 객체 타입을 비롯한 다양한 타입을 저장할 수 있지만, 쿠키는 문자열만 저장 가능.

> RequestHttpURLConnection의 request 파라미터를 수정했으므로 WebViewModel의 req도 수정

```java
// req 파라미터와 conn.request 파라미터에 method 추가
public void req(String url, String method, ContentValues cv) {
    String r = conn.request(url, method, cv);
```

> MyInfoActivity 액티비티 생성

레이아웃은 따로 설정할 필요가 없다. 액티비티 코드만 구현!

```java
public class MyInfoActivity extends AppCompatActivity {

    private WebViewModel model;
    private MutableLiveData<String> res;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_my_info);

        res = new MutableLiveData<>();
        model = new ViewModelProvider(this).get(WebViewModel.class);
        model.setRes(res);

        res.observe(this, new Observer<String>() {
            @Override
            public void onChanged(String s) {
                try {
                    JSONObject obj = new JSONObject(s);
                    String id = obj.getString("loginid");
                    Toast.makeText(MyInfoActivity.this, "login id:" + id, Toast.LENGTH_SHORT).show();
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }
        });

        model.req("http://서버IP주소:7878/web_and/MyInfo", "get", null);
    }
}
```

> MainActivity에 MyInfoActivity로 넘어가도록 하는 My Info 버튼 추가

```java
public void onMyInfo(View view) {
    Intent intent = new Intent(this, MyInfoActivity.class);
    startActivity(intent);
}
```


#### 3. 실행

(1) Eclipse에서 web_and 서버 실행<br>
(2) 그럼 처음에 ```localhost:7878/web_and/```로 이동하고 404 에러가 뜸<br>
(3) URL에 ```login?id=aaa&pwd=111``` 써서 이동하면 true 플래그가 뜸<br>
(-) 여기까진 어제 수업 내용과 같다.<br>
(4) ```localhost:7878/web_and/MyInfo```로 이동하면 로그인 ID가 뜬다. -> 세션 유지<br>
(5) 이제 안드로이드 앱 실행<br>
(6) 웹에서와 마찬가지로 먼저 아이디 aaa, 비밀번호 111로 로그인을 한다.<br>
(7) 그 다음에 첫 화면으로 돌아가서 My Info 버튼 누르면 세션 정보를 담은 Toast 메시지가 뜬다.
