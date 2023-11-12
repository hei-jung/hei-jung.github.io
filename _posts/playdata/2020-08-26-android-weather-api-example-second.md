---
title: "[Android] Android Open API Application(2)"
categories: playdata-android
tags: android java
---

- [이전 글](https://hei-jung.github.io/android/java/weather-api-example-first/)에서는 xml 형식 데이터를 활용했다.
- 이번에는 json 형식 데이터를 불러와보자.
- 액티비티와 레이아웃은 이전 글과 동일하게 설정.
- Main Activity에서 show 함수만 다르게 쓴다.


### show()

> json parsing

```java
public void show() {

    String urlStr = "https://samples.openweathermap.org/data/2.5/weather?q=London&appid=439d4b804bc8187953eb36d2a8c26a02";
    URL url;
    char[] data = new char[255];
    StringBuffer sb = new StringBuffer();

    try {
        url = new URL(urlStr);

        // url에 커넥션 수립
        URLConnection connection = url.openConnection();

        // HTTP 프로토콜에 적합하게 HttpURLConnection로 캐스팅
        HttpURLConnection httpConnection = (HttpURLConnection) connection;

        // 연결상태를 체크하여 정상일 때만 데이터 처리
        int resCode = httpConnection.getResponseCode();
        if (resCode == HttpURLConnection.HTTP_OK) {

            // 자원에서 데이터를 읽을 수 있도록 InputStream을 얻는다.
            InputStream in = httpConnection.getInputStream();
            InputStreamReader isr = new InputStreamReader(in);
            while (isr.read(data) > 0) {
                sb.append(data);
                if (in.available() < 255) {
                    for (int i = 0; i < data.length; i++) {
                        data[i] = ' ';
                    }
                }
            }
            System.out.println(sb);
            isr.close();
            JSONObject weatherInfo = new JSONObject(sb.toString());
            JSONObject coord = weatherInfo.getJSONObject("coord");
            JSONObject main = weatherInfo.getJSONObject("main");
            String name = weatherInfo.getString("name");
            Coord c = new Coord(coord.getDouble("lon"), coord.getDouble("lat"));
            Main m = new Main(main.getDouble("temp"), main.getDouble("pressure"), main.getDouble("humidity"));
            WeatherInfo w = new WeatherInfo(name, c, m);
            textView.setText(w.toString());
        }

    } catch (IOException e) {
        e.printStackTrace();
    } catch (JSONException e) {
        e.printStackTrace();
    }
}
```


### Main 클래스(DTO)

```java
public class Main {
    private double temp;
    private double pressure;
    private double humidity;
    //...
```


### Coord 클래스(DTO)

```java
public class Coord {
    private double lon;
    private double lat;
    //...
```


### WeatherInfo 클래스(DTO)

```java
public class WeatherInfo {
    private String name;
    private Coord c;
    private Main m;
    //...
```
