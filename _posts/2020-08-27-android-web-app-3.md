---
title: "[Android] Connecting Android App to Web Server(3)"
categories: android
tags: android java
---

웹 서버 상의 이미지 데이터를 안드로이드 앱으로 가져와보자!<br>
Eclipse의 web_and>WebContent에 이미지 파일 하나 추가 후 서버 실행<br>
  - EX. img1.jpg

```localhost:7878/web_and/img1.jpg```로 이동하면 저장한 이미지가 잘 뜨는 걸 확인할 수 있다.

## 코드

### ImgLoadActivity 액티비티 생성

레이아웃<br>
- Vertical Linear Layout에 Image View

```java
public class ImgLoadActivity extends AppCompatActivity {

    private ImageView imageView;
    private MutableLiveData<Bitmap> bm;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_img_load);

        imageView = findViewById(R.id.imageView);
        bm = new MutableLiveData<>();
        bm.observe(this, new Observer<Bitmap>() {
            @Override
            public void onChanged(Bitmap bitmap) {
                imageView.setImageBitmap(bitmap);
            }
        });

        new Thread() {
            @Override
            public void run() {
                try {
                    URL url = new URL("http://서버IP주소:7878/web_and/img1.jpg");
                    InputStream is = url.openStream();
                    Bitmap x = BitmapFactory.decodeStream(is);
                    bm.postValue(x);
                } catch (MalformedURLException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }.start();
    }
}
```


### MainActivity에 ImgLoadActivity로 넘어가도록 하는 ```LOAD IMG``` 버튼 추가

```java
public void onLoad(View view) {
    Intent intent = new Intent(this, ImgLoadActivity.class);
    startActivity(intent);
}
```

```LOAD IMG``` 버튼을 누르면 web_and의 WebContent에 저장했던 이미지가 안드로이드 앱 화면 상에 출력된다.
