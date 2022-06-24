---
title: "[Android] Android Open API Application(1)"
categories: android
tags: android java
---

[Open날씨API](https://openweathermap.org/current)를 활용해보자.


## MainActivity

### 1. [인터넷 권한 설정](https://hei-jung.github.io/android/java/android-internet-web-browser-example/)

### 2. TextView를 하나 만든다.

### 3. onCreate

> 네트워크는 예측 불가능하기 때문에 main 쓰레드에서 처리되면 안 됨 -> thread 분리 ([참고](https://codetravel.tistory.com/27))

```java
StrictMode.ThreadPolicy policy = new StrictMode.ThreadPolicy.Builder().permitAll().build();
StrictMode.setThreadPolicy(policy);
```

> xml parsing

```java
show();
```

### 4. show()

```java
public void show() {

    String urlStr = "https://samples.openweathermap.org/data/2.5/weather?q=London&mode=xml&appid=439d4b804bc8187953eb36d2a8c26a02";
    URL url;
    System.out.println(urlStr);
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

            // factory 객체 생성
            DocumentBuilderFactory factory =
                    DocumentBuilderFactory.newInstance();

            // DOM 객체 생성을 위한 builder 생성
            DocumentBuilder builder = factory.newDocumentBuilder();

            // DOM 객체 생성
            Document dom = builder.parse(in);

            // document element를 읽어옴
            Element element = dom.getDocumentElement();

            Element cityTag = (Element) element.getElementsByTagName("city").item(0);
            String city = cityTag.getAttribute("name");

            Element temperatureTag = (Element) element.getElementsByTagName("temperature").item(0);
            String temperature = temperatureTag.getAttribute("value");

            Element humidityTag = (Element) element.getElementsByTagName("humidity").item(0);
            String humidity = humidityTag.getAttribute("value") +
                    humidityTag.getAttribute("unit");

            Element cloudsTag = (Element) element.getElementsByTagName("clouds").item(0);
            String clouds = cloudsTag.getAttribute("name");

            WeatherInfo w = new WeatherInfo(city, temperature, humidity, clouds);
            System.out.println(w);
            textView.setText(w.toString());
        }

    } catch (MalformedURLException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } catch (ParserConfigurationException e) {
        e.printStackTrace();
    } catch (SAXException e) {
        e.printStackTrace();
    }

}
```


## DTO

> WeatherInfo

```java
public class WeatherInfo {
    private String city;
    private String temperature;
    private String humidity;
    private String clouds;

    public WeatherInfo() {
    }

    public WeatherInfo(String city, String temperature, String humidity, String clouds) {
        this.city = city;
        this.temperature = temperature;
        this.humidity = humidity;
        this.clouds = clouds;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getTemperature() {
        return temperature;
    }

    public void setTemperature(String temperature) {
        this.temperature = temperature;
    }

    public String getHumidity() {
        return humidity;
    }

    public void setHumidity(String humidity) {
        this.humidity = humidity;
    }

    public String getClouds() {
        return clouds;
    }

    public void setClouds(String clouds) {
        this.clouds = clouds;
    }

    @Override
    public String toString() {
        return "WeatherInfo{" +
                "city='" + city + '\'' +
                ", temperature='" + temperature + '\'' +
                ", humidity='" + humidity + '\'' +
                ", clouds='" + clouds + '\'' +
                '}';
    }
}
```
