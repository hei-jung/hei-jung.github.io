---
title: "Connecting Android App to Database(2)"
categories: java android
---

## 안드로이드-DB 연동 이어서 두 번째

### Live Data를 써보자.

#### Live Data 실험!

> LiveDataTestActivity 액티비티 생성

1. Live Data 쓰지 않고 파생 쓰레드에서 데이터 갱신하기

- 멤버변수

```java
private TextView textView;  // onCreate에서 findViewById(R.id.textView)
```

- onCreate(): 쓰레드 생성 및 실행

```java
new Thread() {
    @Override
    public void run() {
        ArrayList<String> list = new ArrayList<>();
        list.add("aaa");
        list.add("bbb");
        list.add("ccc");
        for (int i = 0; i < 100; i++) {
            textView.setText(list.get(i % 3));
            try {
                sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}.start();
```

**=> 원래 이렇게 하면 실행 오류 나야 함. 근데 왜 되지?**

2. Live Data 추가

- 멤버변수에 liveData 변수 추가

```java
// handler를 쓰지 않고 파생된 쓰레드를 돌릴 때 생기는 오류를 막아주는 Live Data 객체!
private MutableLiveData<String> liveData;
```

- onCreate()

```java
//...
liveData = new MutableLiveData<>(); // 파생된 쓰레드를 지켜보다가 UI를 갱신해줌.

// observer: 데이터 변경 있나/없나 감시하는 애 (handler와 비슷한 역할.)
// developer site에선 쓰레드를 쓸 때 observer 쓰는 것을 권장하고 있다.
liveData.observe(this, new Observer<String>() {
    @Override
    public void onChanged(String s) {
        textView.setText(s);
    }
});

new Thread() {
    @Override
    public void run() {
        ArrayList<String> list = new ArrayList<>();
        list.add("aaa");
        list.add("bbb");
        list.add("ccc");
        for (int i = 0; i < 100; i++) {
            //liveData.setValue("asdf");  // setValue(): main 쓰레드에서 값을 저장할 때 사용. 그런데 파생 쓰레드에선 쓰지 못한다.
            liveData.postValue(list.get(i % 3));  // => 파생 쓰레드에서 넣어준 값을 저장하려면 postValue() 사용!
            try {
                sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}.start();
```

**=> 액티비티 화면 상에 0.1초 단위로 aaa, bbb, ccc가 번갈아 나타남(바뀌는 횟수: 100번. 위의 코드 그대로 실행되는 것!)**


#### 이제 Live Data를 DB 연동에도 적용해보자.

> build.gradle (Module: app)

[참고: build.gradle 전체코드](https://hei-jung.github.io/android/java/android-db/)

```gradle
// View Model 의존성
implementation "androidx.lifecycle:lifecycle-extensions:2.2.0"  // 주석 해제
```

> ViewModel 클래스를 상속받는 MemberViewModel 클래스 만들기

- MemberDBAdapter의 코드를 복사해와서 생성자, handler와 handler가 쓰이는 코드 지우기

- ViewModel이란?

```java
public class MemberViewModel extends ViewModel {
    /*
     * ViewModel:
     * - view와 model 사이의 매개체 역할!
     * 액티비티의 생명주기와 연결되어
     * 액티비티 실행 중에는 데이터를 유지하지만
     * 액티비티가 소멸되면 같이 소멸됨 */
}
```

- 멤버변수

```java
private AppDatabase database;
private MemberDao dao;
private ExecutorService executorService;
private MutableLiveData<ArrayList<Member>> members; // 추가
private MutableLiveData<Member> member; // 추가
```

- 생성자 같은 역할을 할 create 메서드

```java
public void create(Context context) {
    // View Model은 new로 생성하는 것이 아니라, provider로부터 객체를 얻어와서 씀
    // 그래서 생성자 대신 create 메서드를 만듦
    database = AppDatabase.getInstance(context);
    dao = database.memberDao();
    executorService = Executors.newSingleThreadExecutor();
    members = new MutableLiveData<>();
    member = new MutableLiveData<>();
}
```

- members와 member에 대한 getter

```java
public MutableLiveData<ArrayList<Member>> getMembers() {
    return members;
}

public MutableLiveData<Member> getMember() {
    return member;
}
```    

- getAll() 메서드와 getMember() 메서드 수정

```java
public void getAll() {
    executorService.execute(new Runnable() {
        // Runnable: thread 같은 거.
        @Override
        public void run() {
            members.postValue((ArrayList<Member>) dao.selectAll());
        }
    });
}

public void getMember(final int _id) {
    executorService.execute(new Runnable() {
        @Override
        public void run() {
            member.postValue(dao.selectById(_id));
        }
    });
}
```

- 추가, 수정, 삭제하는 코드는 수정할 필요 x

- onCleared() 메서드 추가

```java
@Override
protected void onCleared() {
    // 액티비티가 끝날 때 정리하는 메서드
    super.onCleared();
    database.close();   // 사용한 DB 닫아줌
    System.out.println("db closed");
    // --> 이를 통해 액티비티와 생명주기를 함께 함을 알 수 있음.
}
```

> ViewModelTestActivity 액티비티 생성

- MainActivity에서 코드 복사해와서 handler와 handler가 쓰이는 코드 지우기

- 멤버변수 수정

```java
//...
private MemberViewModel db; // MemberDBAdapter -> MemberViewModel로 수정
private MutableLiveData<ArrayList<Member>> members; // 추가
private MutableLiveData<Member> member; // 추가
private Member m;
```

- onCreate()에 아래의 코드 추가

```java
// 추가
db = new ViewModelProvider(this).get(MemberViewModel.class);
db.create(this);
members = db.getMembers();
member = db.getMember();

// 추가
members.observe(this, new Observer<ArrayList<Member>>() {
    @Override
    public void onChanged(ArrayList<Member> members) {
        // listView에서 가져온 걸 토대로 화면 갱신
        list.clear();
        list.addAll(members);
        adapter.notifyDataSetChanged();
    }
});

// 추가
member.observe(this, new Observer<Member>() {
    @Override
    public void onChanged(Member member) {
        editName.setText(member.name);
        editTel.setText(member.tel);
        m = member;
    }
});
```
