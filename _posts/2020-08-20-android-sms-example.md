---
title: "안드로이드 - Receiver 활용 SMS 예제"
categories: android java
---

## 수신한 메시지를 ArrayList에 저장해서 표시하고, Options 메뉴로 메시지를 전송하는 앱을 만들어보자!

<details>
<summary>필요한 파일 목록</summary>
- VO(DTO): Sms<br/>
- SMS 송/수신:<br/>
  - SMSReceiveResult<br/>
  - SMSSendResult<br/>
  - SMSReceiver<br/>
  - SMSActivity<br/>
- List UI: MsgAdapter, item_layout.xml<br/>
- Main Activity: SMSListActivity
</details>

### 1. Sms

```java
public class Sms implements Serializable {

    private String phone;
    private String message;
    //...
}    
```

### 2. SMSReceiveResult

```java
@Override
public void onReceive(Context context, Intent intent) {
    // TODO: This method is called when the BroadcastReceiver is receiving
    // an Intent broadcast.
    int result = getResultCode();
    switch (result) {
        case Activity.RESULT_OK:
            Toast.makeText(context, "receive : success", Toast.LENGTH_SHORT).show();
            break;
        case Activity.RESULT_CANCELED:
            Toast.makeText(context, "receive : fail", Toast.LENGTH_SHORT).show();
            break;
    }
}
```

### 3. SMSSendResult

```java
@Override
public void onReceive(Context context, Intent intent) {
    // TODO: This method is called when the BroadcastReceiver is receiving
    // an Intent broadcast.
    int result = getResultCode();
    switch (result) {
        case Activity.RESULT_OK:
            Toast.makeText(context, "success", Toast.LENGTH_SHORT).show();
            break;
        case SmsManager.RESULT_ERROR_GENERIC_FAILURE:
            Toast.makeText(context, "generic failure", Toast.LENGTH_SHORT).show();
            break;
        case SmsManager.RESULT_ERROR_RADIO_OFF:
            Toast.makeText(context, "radio off", Toast.LENGTH_SHORT).show();
            break;
        case SmsManager.RESULT_ERROR_NULL_PDU:
            Toast.makeText(context, "null pdu", Toast.LENGTH_SHORT).show();
            break;
        case SmsManager.RESULT_ERROR_NO_SERVICE:
            Toast.makeText(context, "no service", Toast.LENGTH_SHORT).show();
            break;
    }
}
```

### 4. SMSReceiver

```java
@Override
public void onReceive(Context context, Intent intent) {
    // TODO: This method is called when the BroadcastReceiver is receiving
    // an Intent broadcast.
    Bundle bundle = intent.getExtras();
    String msg;
    String sender;

    int i;
    if (bundle != null) {
        Object[] rawData = (Object[]) bundle.get("pdus");
        SmsMessage[] sms = new SmsMessage[rawData.length];

        for (i = 0; i < rawData.length; i++) {
            sms[i] = SmsMessage.createFromPdu((byte[]) rawData[i]);
        }

        for (i = 0; i < sms.length; i++) {
            msg = sms[i].getMessageBody();
            sender = sms[i].getOriginatingAddress();
            Toast.makeText(context, msg + " from " + sender, Toast.LENGTH_SHORT).show();
        }
    }
}
```

### 5. SMSActivity

**멤버변수**

```java
private EditText editPhone;
private EditText editMessage;

private final static String SEND_ACTION = "SENT";
private final static String DELIVER_ACTION = "DELIVERED";
```

**실행메서드**

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_sms);

    editPhone = findViewById(R.id.editPhone);
    editMessage = findViewById(R.id.editMessage);

    Intent intent = getIntent();
    String tel = intent.getStringExtra("tel");
    if (tel != null && !(tel.equals(""))) {
        editPhone.setText(tel);
    }

    SMSSendResult send = new SMSSendResult();
    IntentFilter f1 = new IntentFilter();
    f1.addAction(SEND_ACTION);

    SMSReceiveResult recv = new SMSReceiveResult();
    IntentFilter f2 = new IntentFilter();
    f2.addAction(DELIVER_ACTION);

    registerReceiver(send, f1);
    registerReceiver(recv, f2);
}
```

**버튼 onClick 메서드**

```java
public void onSend(View view) {
    String msg = editMessage.getText().toString();
    String phone = editPhone.getText().toString();

    if (msg.length() <= 0 || phone.length() <= 0) {
        Toast.makeText(getApplicationContext(), "input the message and phone number!", Toast.LENGTH_SHORT).show();
        return;
    }

    // sms 보내는 쪽 상태 학인을 위한 객체
    PendingIntent sendStat = PendingIntent.getBroadcast(this, 0, new Intent("SENT"), 0);

    // sms 받는 쪽(상대방) 상태 확인을 위한 객체
    PendingIntent deliveredStat = PendingIntent.getBroadcast(this, 0, new Intent("DELIVERED"), 0);

    SmsManager sms = SmsManager.getDefault();
    // sms 전송
    sms.sendTextMessage(phone, null, msg, sendStat, deliveredStat);
    setResult(RESULT_OK);
    finish();
}
```

### 6. MsgAdapter

**기본설정**

```java
private Context context;
private ArrayList<Sms> list;
private int resId;

public MsgAdapter(@NonNull Context context, int resource, @NonNull List<Sms> objects) {
    super(context, resource, objects);
    this.context = context;
    this.list = (ArrayList<Sms>) objects;
    resId = resource;
}
```

**getView 메서드**

```java
@NonNull
@Override
public View getView(int position, @Nullable View convertView, @NonNull ViewGroup parent) {
    View itemView = convertView;
    if (itemView == null) {
        LayoutInflater vi = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        itemView = vi.inflate(resId, null);
    }
    final Sms m = list.get(position);
    if (m != null) {
        TextView textPhone = itemView.findViewById(R.id.textPhone);
        TextView textMsg = itemView.findViewById(R.id.textMsg);

        if (textPhone != null) {
            textPhone.setText(m.getPhone());
        }

        if (textMsg != null) {
            textMsg.setText(m.getMessage());
        }

    }
    return itemView;
}
```    

### 7. SMSListActivity

**(1) SMS 권한 설정**

- onCreate 메서드에 아래 코드 추가

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    // For device above MarshMallow
    boolean permission = getSMSPermission();
    if (permission) {
        Toast.makeText(this, "sms 권한 획득", Toast.LENGTH_SHORT).show();
    }
} else {
    Toast.makeText(this, "권한 획득 필수 아닌 버전", Toast.LENGTH_SHORT).show();
}
```

- 아래의 두 메서드 추가

```java
public boolean getSMSPermission() {
    boolean hasPermission = ((ContextCompat.checkSelfPermission(this,
            Manifest.permission.SEND_SMS) == PackageManager.PERMISSION_GRANTED) &&
            (ContextCompat.checkSelfPermission(this,
                    Manifest.permission.RECEIVE_SMS) == PackageManager.PERMISSION_GRANTED));
    if (!hasPermission) {
        ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.SEND_SMS,
                Manifest.permission.RECEIVE_SMS}, 1);
    }
    return hasPermission;
}
```

```java
@Override
public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    switch (requestCode) {
        case 1: {
            if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                Toast.makeText(this, "sms 권한 부여", Toast.LENGTH_SHORT).show();
            }
        }
    }
}
```

**(2) 나머지 코드**

- Receiver 등록 

```java
private ListView listView;
private ArrayList<Sms> list;
private MsgAdapter adapter;
private String msg;
private String sender;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_sms_list);

    listView = findViewById(R.id.listView);
    list = new ArrayList<>();
    adapter = new MsgAdapter(this, R.layout.item_layout, list);
    listView.setAdapter(adapter);
    registerForContextMenu(listView);

    BroadcastReceiver smsRecv = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, final Intent intent) {
            // an Intent broadcast.
            final Bundle bundle = intent.getExtras();

            int i;
            if (bundle != null) {
                Object[] rawData = (Object[]) bundle.get("pdus");
                SmsMessage[] sms = new SmsMessage[rawData.length];

                for (i = 0; i < rawData.length; i++) {
                    sms[i] = SmsMessage.createFromPdu((byte[]) rawData[i]);
                }

                for (i = 0; i < sms.length; i++) {
                    msg = sms[i].getMessageBody();
                    sender = sms[i].getOriginatingAddress();
                    AlertDialog.Builder builder = new AlertDialog.Builder(SMSListActivity.this);

                    builder.setMessage(msg + "\nfrom: " + sender)
                            .setPositiveButton("OK", new DialogInterface.OnClickListener() {
                                @Override
                                public void onClick(DialogInterface dialogInterface, int i) {
                                    /*다이얼로그 창 닫고 메시지 ArrayList에 저장하도록 구현 */
                                    list.add(new Sms(sender, msg));
                                    dialogInterface.cancel();   // 다이얼로그 창 닫음
                                    adapter.notifyDataSetChanged();
                                }
                            })
                            .setTitle("You've got Message!");
                    AlertDialog alertDialog = builder.create(); // 다이얼로그 생성
                    alertDialog.show(); // 다이얼로그 표시
                }
            }
        }
    };

    IntentFilter f = new IntentFilter();
    f.addAction("android.provider.Telephony.SMS_RECEIVED");
    registerReceiver(smsRecv, f);   // Receiver에 intent filter 등록
}
```

- Options 메뉴

```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    super.onCreateOptionsMenu(menu);
    menu.add(0, Menu.FIRST, 0, "send");
    return true;
}

@Override
public boolean onOptionsItemSelected(@NonNull MenuItem item) {
    super.onOptionsItemSelected(item);
    switch (item.getItemId()) {
        case 1:
            Intent intent = new Intent(this, SMSActivity.class);
            startActivityForResult(intent, 1);
            break;
    }
    return true;
}

@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode == RESULT_OK) {
        switch (requestCode) {
            case 1:
                Toast.makeText(this, "message sent", Toast.LENGTH_SHORT).show();
                break;
        }
    }
}
```

- Context 메뉴

```java
@Override
public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
    super.onCreateContextMenu(menu, v, menuInfo);
    menu.add(0, Menu.FIRST, 0, "delete");
}

@Override
public boolean onContextItemSelected(@NonNull MenuItem item) {
    super.onContextItemSelected(item);
    AdapterView.AdapterContextMenuInfo info = (AdapterView.AdapterContextMenuInfo) item.getMenuInfo();
    int idx = info.position;
    switch (item.getItemId()) {
        case 1:
            list.remove(idx);
            adapter.notifyDataSetChanged();
            break;
    }
    return true;
}
```    

### 8. Manifest 설정(제일 중요!)

> SMS 권한 설정

```xml
<uses-permission android:name="android.permission.RECEIVE_SMS" />
<uses-permission android:name="android.permission.SEND_SMS" />
```

> SMSListActivity를 main intent로 설정

```xml
<activity android:name=".SMSListActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />

        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>

> SMSActivity를 묵시적으로 활성화: 지난번 전화번호부 app이랑 연동하기 위해

```xml
<activity android:name=".SMSActivity">
    <!-- 묵시적으로 활성화 -->
    <intent-filter>
        <action android:name="com.example.receivertest.sms_send" />
    </intent-filter>
</activity>
```

> SMSReceiver를 묵시적으로 활성화

```xml
<receiver
    android:name=".SMSReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter>
        <action android:name="android.provider.Telephony.SMS_RECEIVED" />
    </intent-filter>
</receiver>
```       

### 9. 지난번 MyPhoneBook의 PhoneAdaptor에 아래의 코드 추가

```java
sms.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        ComponentName cn = new ComponentName("com.example.receivertest", "com.example.receivertest.SMSActivity");
        Intent intent = new Intent("com.example.receivertest.sms_send");
        intent.putExtra("tel", m.getTel());
        intent.setComponent(cn);
        context.startActivity(intent);
    }
});
```
