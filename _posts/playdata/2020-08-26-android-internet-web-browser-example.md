---
title: "[Android] Android & Internet"
categories: playdata-android
tags: android java
---

## 간단한 웹 브라우저

### 1. 인터넷 권한 설정

> Manifest

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

> MainActivity onCreate 안에서

```java
/* 인터넷 접속 권한 얻기 */
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    // for device above MarshMallow
    boolean permission = getPermission();
    if (permission) {
        Toast.makeText(this, "obtained contact permission", Toast.LENGTH_SHORT).show();
    } else {
        Toast.makeText(this, "failed to obtain contact permission", Toast.LENGTH_SHORT).show();
    }
}
```

> MainActivity 권한 요청 함수 추가

```java
/* 인터넷 접속 권한 요청 */
public boolean getPermission() {
    boolean hasPermission = (
            (ContextCompat.checkSelfPermission(this, Manifest.permission.INTERNET) == PackageManager.PERMISSION_GRANTED));
    if (!hasPermission) {
        ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.INTERNET}, 1);
    }
    return hasPermission;
}

@Override
public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    switch (requestCode) {
        case 1: {
            if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                Toast.makeText(this, "obtained contact permission", Toast.LENGTH_SHORT).show();
            }
        }
    }
}
```


### 2. 기능

> 위젯 멤버변수

```java
private WebView webView;
private EditText webAddr;
```

> 버튼 핸들러

```java
public void onGo(View view) {
    String url = webAddr.getText().toString();
    webView.loadUrl(url);
    webAddr.setText("https://");
}
```
