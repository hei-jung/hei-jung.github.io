---
title: "안드로이드 - 데이터베이스 연동 (1)"
categories: android java
---

### 안드로이드-DB 연동 복습

- 지난 주 목요일(8/20) 필기 내용
  - 안드로이드 스튜디오 우측 하단의 Device File Explorer > data > data > 프로젝트패키지명 > shared_prefs > idcheck.xml
  - 안드로이드: 보안을 위해 다른 애플리케이션과 데이터 폴더를 공유하지 않는다.

(8/21 수업 내용. 이 날 수업 하루 빠져서 내용 놓침..ㅠㅠ)

- [(8/21)예지언니 필기 참고](https://github.com/Ahnyezi/Android_note/blob/master/0821.md)
  
- sqlite
  - 가장 정석적인 방법
- room
  - RoomDatabase 클래스를 상속받는 추상 클래스를 하나 만들어서 singleton 생성
  - UI 상에선 실행 안 된다는 문제점이 있다. -> .allowMainThreadQueries() 쓰면 해결

> AppDatabase.java

```java
public static AppDatabase getInstance(Context context){
    if(appDatabase==null){
        appDatabase = Room.databaseBuilder(context, AppDatabase.class, "app_db")
                .allowMainThreadQueries() //원래 room db 작업은 ui 쓰레드에서 실행할 수 없으나 이를 허용하는 코드
                .build();
    }
    return appDatabase;
}
```

## 안드로이드 DB 연동 이어서

### 새 프로젝트 생성

#### 1. .allowMainThreadQueries() 쓰지 않고 UI에서 DB 

> build.gradle (Module: app)

* gradle 파일을 수정한 뒤엔 꼭 sync 해주기

```gradle
//...(앞에 생략)
dependencies {
    //...
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
    /* 여기서부터 추가 */
    def room_version = "2.2.5"

    implementation "androidx.room:room-runtime:$room_version"
    annotationProcessor "androidx.room:room-compiler:$room_version" // For Kotlin use kapt instead of annotationProcessor

    // optional - Kotlin Extensions and Coroutines support for Room
    implementation "androidx.room:room-ktx:$room_version"

    // optional - RxJava support for Room
    implementation "androidx.room:room-rxjava2:$room_version"

    // optional - Guava support for Room, including Optional and ListenableFuture
    implementation "androidx.room:room-guava:$room_version"

    // Test helpers
    testImplementation "androidx.room:room-testing:$room_version"

    // View Model 의존성
    //implementation "androidx.lifecycle:lifecycle-extensions:2.2.0"
}
```

> Member 클래스

```java
@Entity
public class Member{
    @PrimaryKey(autoGenerate = true)
    public int _id;
    public String name;
    public String tel;
    public Member(){}
    public Member(int _id, String name, String tel) {
        this._id = _id;
        this.name = name;
        this.tel = tel;
    }

    @Override
    public boolean equals(@Nullable Object obj) {
        if(obj instanceof Member){
            if(this._id==((Member) obj)._id){
                return true;
            }
        }
        return false;
    }

    @Override
    public String toString() {
        return "_id=" + _id + "/ name=" + name + "/ tel=" + tel;
    }
}
```

> MemberDao 인터페이스

```java
@Dao
public interface MemberDao {
    @Insert
    void insert(Member m);

    @Query("select * from member")
    List<Member> selectAll();

    @Query("select * from member where _id=(:id)")
    Member selectById(int id);

    @Delete
    void delete(Member m);

    @Update
    void update(Member m);
}
```

> AppDatabase 추상클래스

```java
@Database(entities = {Member.class}, version=1)
public abstract class AppDatabase extends RoomDatabase {
    private static AppDatabase appDatabase;
    public abstract MemberDao memberDao();

    public static AppDatabase getInstance(Context context){
        if(appDatabase==null){
            appDatabase = Room.databaseBuilder(context, AppDatabase.class, "app_db")
                    .build();
        }
        return appDatabase;
    }

    public static void delDB(){
        appDatabase = null;
    }
}
```

> MemberDBAdapter 

```java
public class MemberDBAdapter {
    private AppDatabase database;
    private MemberDao dao;
    private ExecutorService executorService;    // 백그라운드 실행 도구
    private Handler handler;    // UI 쓰레드와 DB 작업 쓰레드 사이의 통신 담당(메시지 교환)

    public MemberDBAdapter(Context context, Handler handler) {
        this.handler = handler;
        database = AppDatabase.getInstance(context);
        dao = database.memberDao();
        executorService = Executors.newSingleThreadExecutor();
    }

    public void getAll() {
        executorService.execute(new Runnable() {
            // Runnable: thread 같은 거.
            @Override
            public void run() {
                Message msg = handler.obtainMessage();
                msg.what = 0;   // what: 메시지 종류 지정. 처리 방식에 따라 메시지 구분 가능하도록
                msg.obj = (ArrayList<Member>) dao.selectAll();
                handler.sendMessage(msg);
            }
        });
    }

    public void getMember(final int _id) {
        executorService.execute(new Runnable() {
            @Override
            public void run() {
                Message msg = handler.obtainMessage();
                msg.what = 1;   // getAll()과 구분하기 위해 0 외의 새로운 숫자인 1로 지정.
                msg.obj = dao.selectById(_id);
                handler.sendMessage(msg);
            }
        });
    }

    public void addMember(final Member m) {
        executorService.execute(new Runnable() {
            @Override
            public void run() {
                dao.insert(m);
            }
        });
    }

    public void editMember(final Member m) {
        executorService.execute(new Runnable() {
            @Override
            public void run() {
                dao.update(m);
            }
        });
    }

    public void delMember(final Member m) {
        executorService.execute(new Runnable() {
            @Override
            public void run() {
                dao.delete(m);
            }
        });
    }
```

> MainActivity 액티비티

- 멤버변수

```java
private EditText editName;
private EditText editTel;
private ListView listView;
private ArrayAdapter<Member> adapter;
private ArrayList<Member> list;
private Handler handler;
private MemberDBAdapter db;
private Member m;
```

- onCreate()

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

/* 레이아웃 설정 */
editName = findViewById(R.id.editName);
editTel = findViewById(R.id.editTel);
listView = findViewById(R.id.listView);
list = new ArrayList<>();

/* 레이아웃 어댑터 연결 */
adapter = new ArrayAdapter(this, android.R.layout.simple_list_item_1, list);
listView.setAdapter(adapter);

/* 핸들러 등록 */
handler = new Handler(Looper.getMainLooper()) {
    // handler: Message Queue와 같다고 보면 됨.
    // 메시지를 한 쪽에서 보내면 다른 한 쪽에서 받아 처리
    @Override
    public void handleMessage(@NonNull Message msg) {
        // 메시지가 도착하면 자동 호출되는 함수 handleMessage(message)
        if (msg.what == 0) {
            ArrayList<Member> tmp = (ArrayList<Member>) msg.obj;
            list.clear();
            list.addAll(tmp);
            adapter.notifyDataSetChanged();
        } else {
            Member x = (Member) msg.obj;
            editName.setText(x.name);
            editTel.setText(x.tel);
        }
    }
};
db = new MemberDBAdapter(this, handler);
registerForContextMenu(listView);   // context 메뉴 등록

// listView의 한 항목을 누르면 EditText 칸에 해당 항목의 내용이 뜨도록
listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
    @Override
    public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {
        db.getMember(list.get(i)._id);
    }
});
```

- SAVE 버튼과 EDIT 버튼에 대한 onClick 메서드

```java
public void onSave(View view) {
    String n = editName.getText().toString();
    String t = editTel.getText().toString();
    db.addMember(new Member(0, n, t));
    editName.setText("");
    editTel.setText("");
    db.getAll();
}

public void onEdit(View view) {
    if (m != null) {
        m.name = editName.getText().toString();
        m.tel = editTel.getText().toString();
        db.editMember(m);
        db.getAll();
        m = null;
        editName.setText("");
        editTel.setText("");
    }
}
```

- Context 메뉴

```java
@Override
public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
    super.onCreateContextMenu(menu, v, menuInfo);
    menu.add(0, 1, 0, "edit");
    menu.add(0, 2, 0, "delete");
}

@Override
public boolean onContextItemSelected(@NonNull MenuItem item) {
    super.onContextItemSelected(item);
    AdapterView.AdapterContextMenuInfo info = (AdapterView.AdapterContextMenuInfo) item.getMenuInfo();
    int idx = info.position;
    m = list.get(idx);
    switch (item.getItemId()) {
        case 1:
            editName.setText(m.name);
            editTel.setText(m.tel);
            break;
        case 2:
            db.delMember(m);
            db.getAll();
            m = null;
            break;
    }
    return true;
}
```

- UI 갱신

```java
@Override
protected void onPostResume() {
    // 액티비티 실행하자마자 전체 검색한 게 뜨도록
    super.onPostResume();
    db.getAll();
}
```
