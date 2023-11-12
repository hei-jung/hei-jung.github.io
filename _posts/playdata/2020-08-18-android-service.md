---
title: "[Android] Android Service"
categories: playdata-android
tags: android java
---

## Service

> Service: UI가 필요하지 않은 백그라운드 기능 구현하는 용도로 사용 EX.자원관리,...

> 볼륨을 조절하는 간단한 버튼 앱을 이용해 Service 생명주기를 알아보자

(1) New>Service>Service 선택해서 Service 생성

- Service 클래스를 상속받는 MyService 클래스가 만들어진다.

```java
public class MyService extends Service {

}
```

- AndroidManifest.xml

```xml
<!-- Service 생성 시 자동으로 추가된다. -->
<service
    android:name=".MyService"
    android:enabled="true"
    android:exported="true"></service>
```

(2) MyService에 Service 클래스의 추상메서드 구현

```java
@Override
public IBinder onBind(Intent intent) {
    // TODO: Return the communication channel to the service.
    Toast.makeText(this, "onBind()", Toast.LENGTH_SHORT).show();
    return new MyBinder();
}

@Override
public void onCreate() {
    super.onCreate();
    Toast.makeText(this, "onCreate()", Toast.LENGTH_SHORT).show();
}

@Override
public int onStartCommand(Intent intent, int flags, int startId) {
    Toast.makeText(this, "onStartCommand()", Toast.LENGTH_SHORT).show();
    return super.onStartCommand(intent, flags, startId);
}

@Override
public void onDestroy() {
    super.onDestroy();
    Toast.makeText(this, "onDestroy()", Toast.LENGTH_SHORT).show();
}

@Override
public boolean onUnbind(Intent intent) {
    Toast.makeText(this, "onUnbind()", Toast.LENGTH_SHORT).show();
    return super.onUnbind(intent);
}
```

(3) MainActivity에서 서비스와 바인딩할 수 있는 메서드 구현

```java
private MyService.MyBinder mm;

//...

public void onStartBtn(View view) {
    Intent intent = new Intent(this, MyService.class);
    startService(intent);
}

ServiceConnection connection = new ServiceConnection() {
    @Override
    public void onServiceConnected(ComponentName componentName, IBinder iBinder) {
        mm = (MyService.MyBinder) iBinder;
    }

    @Override
    public void onServiceDisconnected(ComponentName componentName) {

    }
};

public void onBindBtn(View view) {
    Intent intent = new Intent(this, MyService.class);
    bindService(intent, connection, Context.BIND_AUTO_CREATE);  // 현재 이 액티비티와 서비스 연결
}

public void onUnbindBtn(View view) {
    unbindService(connection);
    // 직접 null로 처리해주는 게 안전하다. (안 그러면 바인딩이 되지 않았는데도 자원 사용)
    mm = null;
}

public void onStopBtn(View view) {
    Intent intent = new Intent(this, MyService.class);
    stopService(intent);
}
```

(4) Service와 Activity를 바인딩할 Binder 구현

- MyService.java

```java
private int volume = 50;  // 기본값 50

class MyBinder extends Binder {
    int volumeUp() {
        // 볼륨 10 증가. 100보다 클 수 없음. 현재 볼륨 반환
        if (volume == 100) {
            return 100;
        }
        volume += 10;
        return volume;
    }

    int volumeDown() {
        // 볼륨 10 감소. 0보다 작을 수 없음. 현재 볼륨 반환
        if (volume == 0) {
            return 0;
        }
        volume -= 10;
        return volume;
    }

    int getVolume() {
        return volume;
    }
}
```

(5) MainActivity에서 Progress Bar 설정

```java
    private ProgressBar progressBar;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        progressBar = findViewById(R.id.progressBar);
    }
```

- (3)의 connection 부분에 progressBar 설정 추가

```java
ServiceConnection connection = new ServiceConnection() {
    @Override
    public void onServiceConnected(ComponentName componentName, IBinder iBinder) {
        mm = (MyService.MyBinder) iBinder;
        int vol = mm.getVolume(); // 추가
        progressBar.setProgress(vol); // 추가
    }

    //...
};
```

- volume 조절 버튼 onClick 메서드

```java
public void onVolumeUp(View view) {
    if (mm == null) {
        Toast.makeText(this, "bind first", Toast.LENGTH_SHORT).show();
        return;
    }
    int vol = mm.volumeUp();
    if (vol == 100) {
        Toast.makeText(this, "max", Toast.LENGTH_SHORT).show();
    }
    progressBar.setProgress(vol);
}

public void onVolumeDown(View view) {
    if (mm == null) {
        Toast.makeText(this, "bind first", Toast.LENGTH_SHORT).show();
        return;
    }
    int vol = mm.volumeDown();
    if (vol == 0) {
        Toast.makeText(this, "min", Toast.LENGTH_SHORT).show();
    }
    progressBar.setProgress(vol);
}
```
