---
title: "Connecting Android App to Web Server(2)"
categories: java android
---

이번엔 restful 웹에서 만들었던 쇼핑몰 데이터를 가져와보자.<br>
Eclipse에서 전에 만들었던 app_restful을 Java Application으로 실행<br>

## 코드

### Product 클래스 생성

app_restful의 Product 복사해와서 고치면 됨.<br>
Annotation 지우고 seller 타입은 문자열로 수정.

```java
public class Product {
    private Integer num;
    private String name;
    private Integer price;
    private Integer amount;
    private String info;
    private String img;
    private String seller;
    // 생성자, getter와 setter, toString,...
}    
```

### PListActivity 액티비티 생성

레이아웃<br>
- Vertical Linear Layout에 List View

```java
public class PListActivity extends AppCompatActivity {

    private WebViewModel model;
    private MutableLiveData<String> res;
    private ListView listView;
    private ArrayList<Product> list;
    private ArrayAdapter<Product> adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_p_list);

        listView = findViewById(R.id.listView1);
        list = new ArrayList<>();
        res = new MutableLiveData<>();
        adapter = new ArrayAdapter<>(this, android.R.layout.simple_list_item_1, list);
        listView.setAdapter(adapter);

        model = new ViewModelProvider(this).get(WebViewModel.class);
        model.setRes(res);

        res.observe(this, new Observer<String>() {
            @Override
            public void onChanged(String s) {
                list.clear();
                try {
                    JSONObject obj = new JSONObject(s);
                    if (!obj.getBoolean("result")) {
                        Toast.makeText(PListActivity.this, "result fail", Toast.LENGTH_SHORT).show();
                        return;
                    }

                    JSONArray data = obj.getJSONArray("data");
                    JSONObject o = null;
                    for (int i = 0; i < data.length(); i++) {
                        o = data.getJSONObject(i);
                        list.add(new Product(o.getInt("num"),
                                o.getString("name"),
                                o.getInt("price"),
                                o.getInt("amount"),
                                o.getString("info"),
                                o.getString("img"),
                                o.getJSONObject("seller").getString("id")));
                    }
                    adapter.notifyDataSetChanged();
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }
        });

        model.req("http://서버IP주소:8888/products", "get", null);
    }
}
```

### MainActivity에 PListActivity로 넘어가도록 하는 ```PRODUCT LIST``` 버튼 추가

```java
public void onPList(View view) {
    Intent intent = new Intent(this, PListActivity.class);
    startActivity(intent);
}
```

└ 버튼 누르면 예전에 쇼핑몰 실습할 때 넣었던 product 목록이 나타난다.
