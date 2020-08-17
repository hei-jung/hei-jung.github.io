---
title: "전화번호부 예제"
categories: android java
---

## AndroidManifest.xml
```xml
<uses-permission android:name="android.permission.CALL_PHONE" />
<application>...</application>
```

## Member 클래스
```java
public class Member implements Serializable {
    // Member 객체를 View로 전달할 수 있도록 Serializable 불러옴
    private String name;
    private String tel;
    private Integer imgRes;
    private int phoneType;//핸드폰(0), 집전화(1), 회사전화(2)
    ...
```

## PhoneAdaptor
```java
public class PhoneAdaptor extends ArrayAdapter<Member> {
    private Context context;
    private ArrayList<Member> list;
    private int resId;

    public PhoneAdaptor(@NonNull Context context, int resource, @NonNull List<Member> objects) {
        super(context, resource, objects);
        this.context = context;
        this.list = (ArrayList<Member>) objects;
        resId = resource;
    }

    @NonNull
    @Override   //position 위치의 데이터를 읽어와서 리소스로 지정한 뷰로 생성하여 반환
    public View getView(int position, @Nullable View convertView, @NonNull ViewGroup parent) {
        View itemView = convertView;
        if (itemView == null) {
            LayoutInflater vi = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
            itemView = vi.inflate(resId, null);
        }
        final Member m = list.get(position);
        if (m != null) {
            TextView t1 = itemView.findViewById(R.id.textView);
            TextView t2 = itemView.findViewById(R.id.textView2);
            ImageView imgv = itemView.findViewById(R.id.imageView);
            imgv.setImageResource(m.getImgRes());
            Button sms = itemView.findViewById(R.id.button18);
            Button call = itemView.findViewById(R.id.button19);

            if (t1 != null) {
                t1.setText(m.getName());
            }
            if (t2 != null) {
                t2.setText(m.getTel());
            }

            call.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    Intent intent = new Intent(Intent.ACTION_DIAL, Uri.parse("tel:" + m.getTel()));
                    context.startActivity(intent);
                }
            });
        }
        return itemView;
    }
}
```

## PhoneAdaptor에 대한 View
> item_layout.xml
```xml
<Linear Layout>
<ImageView/>
<TextView/>
<Button/>
...
</Linear Layout>
```

## MainActivity.java
> 액티비티 
```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        listView = findViewById(R.id.listView);
        list = new ArrayList<>();
        pa = new PhoneAdaptor(this, R.layout.item_layout, list);
        listView.setAdapter(pa);
        registerForContextMenu(listView);// listView에 context menu를 붙임
    }
```    

> option메뉴 생성
```java
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        super.onCreateOptionsMenu(menu);
        menu.add(0, Menu.FIRST, 0, "add");
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        super.onOptionsItemSelected(item);
        switch (item.getItemId()) {
            case 1:
                Intent intent = new Intent(this, AddActivity.class);
                startActivityForResult(intent, 1);// onActivityResult에서 쓰일 requestCode:1
                break;
        }
        return true;
    }
```    

> 다른 액티비티에서 되돌아왔을 때 실행할 내용
```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (resultCode == RESULT_OK) {
            Member m = null;
            switch (requestCode) {
                case 1://추가
                    m = (Member) data.getSerializableExtra("m");
                    if (m != null) {
                        list.add(m);
                        pa.notifyDataSetChanged();
                    } else {
                        Toast.makeText(MainActivity.this, "추가정보없음", Toast.LENGTH_SHORT).show();
                    }
                    break;
                case 2://수정
                    m = (Member) data.getSerializableExtra("m");
                    pa.notifyDataSetChanged();
                    if (m != null) {
                        list.set(idx, m);
                        pa.notifyDataSetChanged();
                    } else {
                        Toast.makeText(MainActivity.this, "수정정보없음", Toast.LENGTH_SHORT).show();
                    }
                    break;
            }
        }
    }
```    

> context메뉴 
```java
    @Override
    public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
        super.onCreateContextMenu(menu, v, menuInfo);
        menu.setHeaderTitle("UPDATE");
        menu.add(0, Menu.FIRST, 0, "edit");
        menu.add(0, Menu.FIRST + 1, 0, "delete");
    }

    @Override
    public boolean onContextItemSelected(@NonNull MenuItem item) {
        super.onContextItemSelected(item);
        AdapterView.AdapterContextMenuInfo info = (AdapterView.AdapterContextMenuInfo) item.getMenuInfo();
        idx = info.position;

        switch (item.getItemId()) {
            case 1://수정
                Intent intent = new Intent(this, EditActivity.class);
                intent.putExtra("m", list.get(idx));// 해당 Member 객체 전달
                startActivityForResult(intent, 2);// onActivityResult에서 쓰일 requestCode:2
                break;
            case 2://삭제
                list.remove(idx);
                pa.notifyDataSetChanged();
                break;
        }
        return true;
    }

```

## AddActivity.java
> 라디오버튼 설정
```java
types.setOnCheckedChangeListener(new RadioGroup.OnCheckedChangeListener() {
    @Override
    public void onCheckedChanged(RadioGroup radioGroup, int i) {
        int type = 0;
        switch (i) {
            case R.id.radioBtn1:
                type = 0;
                break;
            case R.id.radioBtn2:
                type = 1;
                break;
            case R.id.radioBtn3:
                type = 2;
                break;
        }
        member.setPhoneType(type);
    }
});
```

> spinner 이미지 선택
```java
imgIdx = new ArrayList<>();
      imgIdx.add(R.drawable.img1);
      imgIdx.add(R.drawable.img2);
      imgIdx.add(R.drawable.img3);
      imgIdx.add(R.drawable.img4);
      imgIdx.add(R.drawable.img5);
      imgIdx.add(R.drawable.img6);

      imgShow = new ArrayList<>();
      for (int i = 1; i < 7; i++) {
          imgShow.add("img" + i);
      }

      aa = new ArrayAdapter<>(this, android.R.layout.simple_spinner_item, imgShow);
      spinner.setAdapter(aa);
      spinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
          @Override
          public void onItemSelected(AdapterView<?> adapterView, View view, int i, long l) {
              imageView.setImageResource(imgIdx.get(i));
              member.setImgRes(imgIdx.get(i));
          }

          @Override
          public void onNothingSelected(AdapterView<?> adapterView) {
              Toast.makeText(AddActivity.this, "선택된 항목 없음", Toast.LENGTH_SHORT).show();
          }
      });
```

> '저장' 버튼에 대한 onClick 메서드
```java
public void onSave(View view) {
    String name = editName.getText().toString();
    String tel = editTel.getText().toString();
    if (name.equals("") || tel.equals("")) {
        Toast.makeText(AddActivity.this, "이름/전화번호 입력 필수", Toast.LENGTH_SHORT).show();
        return;
    }
    intent = new Intent();
    member.setName(name);
    member.setTel(tel);
    intent.putExtra("m", member);
    setResult(RESULT_OK, intent);
    finish();
}
```
