---
title: "[Android] Android Socket Communication"
categories: playdata-android
tags: android java
---

* [인터넷 권한 설정](https://hei-jung.github.io/android/java/android-internet-web-browser-example/)은 당연히 필수겠죠?

## Server

> Android Studio 말고 Eclipse에서 서버 만들고 실행

```java
class EchoServerTh extends Thread {

  private Socket socket;

	public EchoServerTh(Socket socket) {
		this.socket = socket;
	}

	@Override
	public void run() {
		// TODO Auto-generated method stub
		try {
			InputStream is = socket.getInputStream();
			InputStreamReader ir = new InputStreamReader(is);
			BufferedReader br = new BufferedReader(ir);
			PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
			while (true) {
				String str = br.readLine();// 클라이언트가 보낸 메시지 한줄 읽음
				System.out.println("클라이언트 메시지:" + str);
				out.println(str + "\n");// 클라이언트가 보낸 메시지를 다시 클라이언트에게 보냄
				if (str.startsWith("/stop")) {
					break;
				}
			}
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}

public class EchoServer {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		try {
			ServerSocket ss = new ServerSocket(포트번호(EX.3333));// 서버소켓오픈. 파라메터는 포트번호
			System.out.println("서버시작");
			while (true) {
        // 클라이언트 접속 기다리다 요청수락. 수락후 이 클라이언트와 1:1통신할 소켓 반환
				Socket socket = ss.accept();
				System.out.println("클라이언트 접속");
				new EchoServerTh(socket).start();
			}
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}
```


## Client

> 안드로이드 앱을 클라이언트로

__(1) MyThread 클래스__

```java
public class MyThread extends Thread {

    String msg;

    // 생성자, getter, setter

    @Override
    public void run() {
        try {
            InetAddress serverAddr = InetAddress.getByName(서버IP주소); // 서버 IP 객체 생성
            Socket socket = new Socket(serverAddr, 포트번호(서버 쪽에서 쓴 것과 동일하게)); // 통신 소켓 객체 생성
            BufferedWriter out = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
            BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            out.write(msg + "\n");
            out.flush();
            msg = "received from:" + in.readLine();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

__(2) 레이아웃__

- 서버한테 보낼 메시지를 쓸 EditText
- 전송 Button

```java
private EditText msg; // 메인 액티비티에서
```

__(3) MainActivity__

- 버튼 핸들러

```java
public void onSend(View view) {
    String m = msg.getText().toString();
    MyThread th = new MyThread(m);
    th.start();
}
```
