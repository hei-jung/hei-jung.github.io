---
title: "[Android] Connecting Android App to Web Server"
categories: android
tags: android java
---

### 기본 설정(안드로이드)

- Manifest 인터넷 권한 설정
- Manifest application 태그에 CleartextTraffic 사용 추가(HTTP 접근 위함)

```xml
<application
  <!-- ... -->
  android:usesCleartextTraffic="true">
```

- build.gradle(Module: app)에 의존성 추가(live data 쓰기 위함)

```gradle
implementation 'androidx.lifecycle:lifecycle-extensions:2.2.0'
```

- MainActivity onCreate에서 thread 분리

```java
StrictMode.ThreadPolicy policy = new StrictMode.ThreadPolicy.Builder().permitAll().build();
StrictMode.setThreadPolicy(policy);
```


## Java 서버(Eclipse에서)

> web_and라는 이름의 Dynamic Web Project 생성

> andtest라는 패키지를 만들고 login이라는 Servlet을 만들었다.

- doGet에 이렇게 코드를 썼다.
- 목적: id가 'aaa'고 pwd가 '111'일 때만 '로그인 성공'을 띄운다.

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {
  // TODO Auto-generated method stub
  String id = request.getParameter("id");
  String pwd = request.getParameter("pwd");
  boolean flag = false;
  if (id.equals("aaa") && pwd.equals("111")) {
    flag = true;
  }
  request.setAttribute("flag", flag);
  RequestDispatcher rd = request.getRequestDispatcher("views/login.jsp");
  rd.forward(request, response);
}
```

> 프로젝트의 WebContent 폴더에 views라는 폴더를 만들고

> views 안에 login.jsp 파일을 만들었다.

```jsp
<%@ page language="java" contentType="text/html; charset=EUC-KR"
	pageEncoding="EUC-KR"%>
{"flag":${flag}}
```

> 웹서버 실행하면 처음엔 'localhost:7878/web_and/'로 404 에러가 뜬다. (7878은 내가 톰캣 설치할 때 지정한 포트주소)

> 주소 뒤에 'login?id=aaa&pwd=111' 추가하고 이동하면 flag가 true인 채로 나타난다.<br>
> 반면, aaa과 111 이외의 인수를 넣으면 flag가 false로 나타나게 된다.

## 안드로이드

### 1. MainActivity

> 레이아웃

- login Button: onClick 메서드 이름은 onLogin

> 버튼 핸들러

```java
public void onLogin(View view) {
    Intent intent = new Intent(this, LoginActivity.class);
    startActivity(intent);
}
```

### 2. WebViewModel 클래스

```java
public class WebViewModel extends ViewModel {

    private MutableLiveData<String> res;
    private ExecutorService executorService;
    private RequestHttpURLConnection conn;

    public void setRes(MutableLiveData<String> res) {
        this.res = res;
    }

    public WebViewModel() {
        executorService = Executors.newSingleThreadExecutor();
        conn = new RequestHttpURLConnection();
    }

    public void req(String url, ContentValues cv) {
        String r = conn.request(url, cv);
        res.postValue(r);
    }
}
```

### 3. RequestHttpURLConnection 클래스

```java
public class RequestHttpURLConnection {
    public String request(String _url, ContentValues _params) {

        // HttpURLConnection 참조 변수.
        HttpURLConnection urlConn = null;
        // URL 뒤에 붙여서 보낼 파라미터.
        StringBuffer sbParams = new StringBuffer();

        /**
         * 1. StringBuffer에 파라미터 연결
         * */
        // 보낼 데이터가 없으면 파라미터를 비운다.
        if (_params == null)
            sbParams.append("");
            // 보낼 데이터가 있으면 파라미터를 채운다.
        else {
            // 파라미터가 2개 이상이면 파라미터 연결에 &가 필요하므로 스위칭할 변수 생성.
            boolean isAnd = false;
            // 파라미터 키와 값.
            String key;
            String value;

            for (Map.Entry<String, Object> parameter : _params.valueSet()) {
                key = parameter.getKey();
                value = parameter.getValue().toString();

                // 파라미터가 두개 이상일때, 파라미터 사이에 &를 붙인다.
                if (isAnd)
                    sbParams.append("&");

                sbParams.append(key).append("=").append(value);

                // 파라미터가 2개 이상이면 isAnd를 true로 바꾸고 다음 루프부터 &를 붙인다.
                if (!isAnd)
                    if (_params.size() >= 2)
                        isAnd = true;
            }
        }

        /**
         * 2. HttpURLConnection을 통해 web의 데이터를 가져온다.
         * */
        try {
            URL url = new URL(_url);
            urlConn = (HttpURLConnection) url.openConnection();

            // [2-1]. urlConn 설정.
            urlConn.setRequestMethod("POST"); // URL 요청에 대한 메소드 설정 : POST.
            urlConn.setRequestProperty("Accept-Charset", "UTF-8"); // Accept-Charset 설정.
            urlConn.setRequestProperty("Context_Type", "application/x-www-form-urlencoded;charset=UTF-8");

            // [2-2]. parameter 전달 및 데이터 읽어오기.
            String strParams = sbParams.toString(); //sbParams에 정리한 파라미터들을 스트링으로 저장. 예)id=id1&pw=123;
            OutputStream os = urlConn.getOutputStream();
            os.write(strParams.getBytes("UTF-8")); // 출력 스트림에 출력.
            os.flush(); // 출력 스트림을 플러시(비운다)하고 버퍼링 된 모든 출력 바이트를 강제 실행.
            os.close(); // 출력 스트림을 닫고 모든 시스템 자원을 해제.
            System.out.println("param:" + strParams);
            // [2-3]. 연결 요청 확인.
            // 실패 시 null을 리턴하고 메서드를 종료.
            if (urlConn.getResponseCode() != HttpURLConnection.HTTP_OK) {
                System.out.println("연결실패");
                return null;
            }

            // [2-4]. 읽어온 결과물 리턴.
            // 요청한 URL의 출력물을 BufferedReader로 받는다.
            BufferedReader reader = new BufferedReader(new InputStreamReader(urlConn.getInputStream(), "UTF-8"));

            // 출력물의 라인과 그 합에 대한 변수.
            String line;
            String page = "";

            // 라인을 받아와 합친다.
            while ((line = reader.readLine()) != null) {
                page += line;
            }
            System.out.println("result:" + page);
            return page;

        } catch (MalformedURLException e) { // for URL.
            e.printStackTrace();
        } catch (IOException e) { // for openConnection().
            e.printStackTrace();
        } finally {
            if (urlConn != null)
                urlConn.disconnect();
        }

        return null;

    }
}
```

### 4. LoginActivity

> 레이아웃

- 아이디 입력칸
- 비밀번호 입력칸
- 로그인 버튼

> 액티비티

```java
public class LoginActivity extends AppCompatActivity {

    private EditText editId;
    private EditText editPwd;
    private WebViewModel model;
    private MutableLiveData<String> res;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        editId = findViewById(R.id.editId);
        editPwd = findViewById(R.id.editPwd);
        res = new MutableLiveData<>();
        model = new ViewModelProvider(this).get(WebViewModel.class);
        model.setRes(res);

        res.observe(this, new Observer<String>() {
            @Override
            public void onChanged(String s) {
                String str = "";
                try {
                    JSONObject obj = new JSONObject(s);
                    if (obj.getBoolean("flag")) {
                        str = "로그인 성공";
                    } else {
                        str = "로그인 실패";
                    }
                    Toast.makeText(LoginActivity.this, str, Toast.LENGTH_SHORT).show();
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }
        });
    }

    public void onLogin(View view) {
        String id = editId.getText().toString();
        String pwd = editPwd.getText().toString();
        ContentValues cv = new ContentValues();
        cv.put("id", id);
        cv.put("pwd", pwd);
        model.req("https://서버IP주소:포트주소/web_and/login", cv);
        id_et.setText("");
        pwd_et.setText("");
    }
}    
```
