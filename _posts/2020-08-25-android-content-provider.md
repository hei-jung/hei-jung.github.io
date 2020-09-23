---
title: "Android Content Provider"
categories: java android
---

# Content Provider

- 두 애플리케이션이 있다고 하자. -> app1, app2
- **resolver**가 app2 대신 provider에 db query 작업 등 요청
  - getContentResolver()
  - 요청하려면 알아야 할 세 가지
    1. content provider의 이름
    2. 컬럼명(테이블명은 몰라도 돼)
    3. URI
- 데이터를 제공하는 app1서 **provider** 생성
  - provider는 intent가 아니라 **content resolver의 요청에 의해** 깨어남
  - provider가 만들어지려면, 각 행을 구분할 수 있는 primary key 역할의 **_id**가 있어야 함
  - cursor 객체 생성해서 query 작업 수행
- 그러면 app1서 처리한 뒤 그 결과를 app2로 전달
- provider에 접근할 수 있는 uri를 열어줘야 함
  - cf. URI(Uniform Resource Identifier)
  - content provider의 프로토콜은 'content://'로 시작
  - manifest 파일에 provider를 등록. 외부에서 접근할 수 있는 uri와 함께.
  - 쓰기 권한/읽기 권한 분류되어 있음


## 예제1


> Manifest 파일에 연락처 읽기/쓰기 권한 추가

```xml
<uses-permission android:name="android.permission.READ_CONTACTS" />
<uses-permission android:name="android.permission.WRITE_CONTACTS" />
```


> MainActivity에 연락처 읽기/쓰기 권한 추가

1. onCreate() 메서드 안에서

```java
/* 안드로이드 기본 내장 전화번호부에서 연락처 읽어오는 권한 얻기 */
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

2. 권한 요청하는 아래의 두 메서드 추가

```java
public boolean getPermission() {
    boolean hasPermission = (
            (ContextCompat.checkSelfPermission(this, Manifest.permission.READ_CONTACTS)
                    == PackageManager.PERMISSION_GRANTED) &&
                    (ContextCompat.checkSelfPermission(this, Manifest.permission.WRITE_CONTACTS)
                            == PackageManager.PERMISSION_GRANTED));
    if (!hasPermission) {
        ActivityCompat.requestPermissions(
                this,
                new String[]{Manifest.permission.READ_CONTACTS, Manifest.permission.WRITE_CONTACTS},
                1);
    }
    return hasPermission;
}
```

```java
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


> 연락처를 읽고 쓰도록 하는 코드: onCreate() 안에 아래 코드 추가

### 연락처 쓰기

```java
ContentValues values = new ContentValues();
String contactId = "aaa";
// 데이터 테이블의 _id 컬럼에 접근할 reference
values.put(ContactsContract.RawContacts.CONTACT_ID, contactId);
// ContentValues 객체를 이용해 db에 id 저장
Uri contactUri = getContentResolver().insert(ContactsContract.RawContacts.CONTENT_URI, values);
// _id 컬럼의 값 읽어와 할당
long contactId_l = ContentUris.parseId(contactUri);
// ContentValues의 데이터 모두 삭제(새 데이터 넣기 위해)
values.clear();
// ContentValues에 저장할 데이터의 id 저장
values.put(ContactsContract.RawContacts.Data.RAW_CONTACT_ID, contactId_l);
// 데이터 mime 타입 저장
values.put(ContactsContract.Data.MIMETYPE, Phone.CONTENT_ITEM_TYPE);
// 전화번호 저장
values.put(Phone.NUMBER, "010-1212-3434");
// 전화 종류(HOME, MOBILE, WORK, ETC) 저장
values.put(Phone.TYPE, Phone.TYPE_MOBILE);
// 사용자 식별 레이블 저장
values.put(Phone.LABEL, "aaa");
// ContentValues의 데이터를 db 테이블에 insert
Uri dataUri = getContentResolver().insert(ContactsContract.Data.CONTENT_URI, values);
```

### 연락처 읽기

```java
Cursor c = getContentResolver().query(ContactsContract.Data.CONTENT_URI,
        new String[]{ContactsContract.Data._ID, Phone.NUMBER, Phone.TYPE,
                Phone.LABEL}, null, null, null);
String id = null, number = null, type = null, label = null;
int num;
// Cursor의 시작 위치로 이동해 데이터를 한 줄씩 읽는다.
if (c.moveToFirst()) {
    do {
        // 현재 줄의 데이터(number, type, label)을 읽는다.
        // 먼저 number와 type 컬럼의 값을 읽는다.
        number = c.getString(c.getColumnIndex(Phone.NUMBER));
        num = c.getShort((c.getColumnIndex(Phone.TYPE)));
        // 전화 타입을 나타내는 값은 정수
        // -> 사용자가 이해할 수 있는 형태로 변환한다.
        switch (num) {
            case 1:
                type = "HOME";
                break;
            case 2:
                type = "MOBILE";
                break;
            case 3:
                type = "WORK";
                break;
            default:
                type = "ETC";
        }
        label = c.getString(c.getColumnIndex(Phone.LABEL));
        String str = "label:" + label + ", number:" + number + ", type:" + type;
        Toast.makeText(this, str, Toast.LENGTH_SHORT).show();
    } while (c.moveToNext());   // 커서에 다음 데이터가 있는 동안엔 계속 읽음
}
```


## 예제2


> 어제 실습의 AppDatabase, Member, MemberDao 클래스 코드 복사

- AppDatabase 오류 해결
  - build.gradle (Module: app)에 아래 코드 추가(지난 실습에서 사용) 후 동기화

```gradle
def room_version = "2.2.5"
implementation "androidx.room:room-runtime:$room_version"
annotationProcessor "androidx.room:room-compiler:$room_version"
implementation "androidx.room:room-ktx:$room_version"
implementation "androidx.room:room-rxjava2:$room_version"
implementation "androidx.room:room-guava:$room_version"
testImplementation "androidx.room:room-testing:$room_version"
```


> New>Other>Content Provider

- 이 때 URI Authorities에 현재 패키지명.provider라고 씀(다르게 써도 ok)
  - 여기선 com.example.providertest.provider라고 썼다.
- finish 누르고 AndroidManifest.xml 파일로 가면 다음 코드가 자동으로 등록된다.

```xml
<provider
    android:name=".MyContentProvider"
    android:authorities="com.example.providertest.provider"
    android:enabled="true"
    android:exported="true"></provider>
```

- 보면 authorities가 새 Content Provider 만들 때 URI Authorities에 썼던 것과 같음을 확인할 수 있다.


> 새 java class 만들기: SQLiteSupport

- 멤버변수와 생성자

```java
private AppDatabase database;
private SupportSQLiteDatabase db;   // Room db를 SQLite db로 쓸 수 있도록 도와주는 클래스

public SQLiteSupport(Context context) {
    database = AppDatabase.getInstance(context);
}
```

- DB Query 등 DB 활용하도록 하는 메서드

```java
public void open() {
    SupportSQLiteOpenHelper helper = database.getOpenHelper();
    db = helper.getWritableDatabase();
}

public Cursor select(String t, String where) {
    String sql = "select * from " + t;
    if (where != null && !where.equals("")) {
        sql += " where " + where;
    }
    return db.query(sql);
}

public long insert(String t, ContentValues cv) {
    return db.insert(t, 0, cv);
}

public int update(String t, ContentValues cv, String where, String[] args) {
    return db.update(t, 0, cv, where, args);
}

public int delete(String t, String where, String[] args) {
    return db.delete(t, where, args);
}

public void close() {
    try {
        db.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```


> 다시 아까 만든 content provider인 MyContentProvider로 돌아와서,

- 멤버변수 추가

```java
private SQLiteSupport db;
// 외부에서 이 provider에 접근할 수 있도록 하는 URI
public final static Uri CONTENT_URI = Uri.parse("content://com.example.providertest.provider/member");
private final static int DATA = 1;
private final static int DATA_ID = 2;
private final static UriMatcher uri_matcher;    // 요청이 왔을 때 URI 형태 확인
private final static String TABLE_NAME = "member";

static {
    uri_matcher = new UriMatcher(UriMatcher.NO_MATCH);
    uri_matcher.addURI("com.example.providertest.provider", "member", DATA);
    uri_matcher.addURI("com.example.providertest.provider", "member/#", DATA_ID);
}
```

- provider 생성 코드

```java
@Override
public boolean onCreate() {
    // TODO: Implement this to initialize your content provider on startup.
    db = new SQLiteSupport(getContext());
    db.open();
    return false;
}
```

- URI에 따라 MIME 타입 구분

```java
@Override
public String getType(Uri uri) {
    // TODO: Implement this to handle requests for the MIME type of the data
    // at the given URI.
    switch (uri_matcher.match(uri)) {
        case DATA:
            return "com.example.providertest.provider/dir";  // 집합
        case DATA_ID:
            return "com.example.providertest.provider/item";  // 단일행
    }
    return null;
}
```

- 삭제

```java
@Override
public int delete(Uri uri, String selection, String[] selectionArgs) {
    // Implement this to handle requests to delete one or more rows.
    int cnt = 0;
    switch (uri_matcher.match(uri)) {
        case DATA:
            cnt = db.delete(TABLE_NAME, selection, selectionArgs);
            break;
        case DATA_ID:
            String _id = uri.getPathSegments().get(1);
            cnt = db.delete(TABLE_NAME,
                    "_id=" + _id + (!TextUtils.isEmpty(selection) ? " and " + selection : ""), selectionArgs);
            break;
    }
    getContext().getContentResolver().notifyChange(uri, null);
    return cnt;
}
```

- 추가

```java
@Override
public Uri insert(Uri uri, ContentValues values) {
    // TODO: Implement this to handle requests to insert a new row.
    long id = db.insert(TABLE_NAME, values);
    Uri u = null;
    if (id > 0) {
        u = Uri.withAppendedPath(CONTENT_URI, id + "");
        getContext().getContentResolver().notifyChange(u, null);
    }
    return u;
}
```

- 검색

```java
@Override
public Cursor query(Uri uri, String[] projection, String selection,
                    String[] selectionArgs, String sortOrder) {
    // TODO: Implement this to handle query requests from clients.
    Cursor cursor = null;
    switch (uri_matcher.match(uri)) {
        case DATA:
            cursor = db.select(TABLE_NAME, selection);
            break;
        case DATA_ID:
            String _id = uri.getPathSegments().get(1);
            cursor = db.select(TABLE_NAME,
                    "_id=" + _id + (!TextUtils.isEmpty(selection) ? " and " + selection : ""));
            break;
    }
    return cursor;
}
```

- 수정

```java
@Override
public int update(Uri uri, ContentValues values, String selection,
                  String[] selectionArgs) {
    // TODO: Implement this to handle requests to update one or more rows.
    int cnt = 0;
    switch (uri_matcher.match(uri)) {
        case DATA:
            cnt = db.update(TABLE_NAME, values, selection, selectionArgs);
            break;
        case DATA_ID:
            String _id = uri.getPathSegments().get(1);
            cnt = db.update(TABLE_NAME, values,
                    "_id=" + _id + (!TextUtils.isEmpty(selection) ? " and " + selection : ""), selectionArgs);
            break;
    }
    getContext().getContentResolver().notifyChange(uri, null);
    return cnt;
}
```


> ProviderTestActivity 액티비티

- 멤버변수

```java
private EditText editName;
private EditText editTel;
private ListView listView;
private ArrayAdapter<Member> adapter;
private ArrayList<Member> list;
private Uri uri = MyContentProvider.CONTENT_URI;
```    

- onCreate() 메서드

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_provider_test);
    editName = findViewById(R.id.editName);
    editTel = findViewById(R.id.editTel);
    listView = findViewById(R.id.listView);
    list = new ArrayList<>();
    adapter = new ArrayAdapter(this, android.R.layout.simple_list_item_1, list);
    listView.setAdapter(adapter);
    getAll();
}
```    

- getAll() 메서드 추가

```java
public void getAll() {
    list.clear();
    Cursor c = getContentResolver().query(uri, null, null, null, null);
    if (c.moveToFirst()) {
        do {
            list.add(new Member(c.getInt(0), c.getString(1), c.getString(2)));
        } while (c.moveToNext());
    }
    adapter.notifyDataSetChanged();
}
```    

- 버튼 핸들러 onClick 메서드

```java
public void onSave(View view) {
    String n = editName.getText().toString();
    String t = editTel.getText().toString();
    ContentValues cv = new ContentValues();
    cv.put("name", n);
    cv.put("tel", t);
    getContentResolver().insert(uri, cv);
    editName.setText("");
    editTel.setText("");
    getAll();
}

public void onEdit(View view) {
    // 숙제
}
```
