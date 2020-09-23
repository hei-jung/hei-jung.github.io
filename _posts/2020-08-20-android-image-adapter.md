---
title: "Android Image Adapter Application"
categories: java android
---

(2020-08-18 수업 내용)

## Image Adapter를 활용해보자!

> Grid View로 이미지 여러 개를 표시하고, 두 이미지를 클릭하여 서로 위치를 바꿔보자.

1. 레이아웃 xml

```xml
<!-- vertical linear layout에 grid view를 추가 -->
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity8">

    <GridView
        android:id="@+id/gridView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:columnWidth="90dp"
        android:gravity="center"
        android:horizontalSpacing="10dp"
        android:numColumns="auto_fit"
        android:stretchMode="columnWidth"
        android:verticalSpacing="10dp" />
</LinearLayout>
```

2. Main Activity

```java
public class MainActivity8 extends AppCompatActivity {

    private GridView gridView;
    private ImageAdapter imageAdapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main8);

        gridView = findViewById(R.id.gridView);
        imageAdapter = new ImageAdapter(this);
        gridView.setAdapter(imageAdapter);    // 어댑터 생성하여 그리드뷰와 연결

        // 그리드뷰에 출력된 이미지를 클릭하면 실행될 클릭 리스너 실행
        AdapterView.OnItemClickListener m = new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> adapterView, View view, int i, long l) {
                // 아이템 하나를 선택하면 Toast로 위치(인덱스) 출력
                Toast.makeText(MainActivity8.this, i + "", Toast.LENGTH_SHORT).show();
                imageAdapter.set_idx(i);
            }
        };

        gridView.setOnItemClickListener(m); // 그리드뷰에 생성한 클릭 리스너 설정

    }
}
```

3. Image Adapter

```java
class ImageAdapter extends BaseAdapter {

    private Context mContext;
    private int idx1;
    private int idx2;
    private Integer[] Idata = {R.drawable.img1, R.drawable.img2, R.drawable.img3, R.drawable.img4, R.drawable.img5, R.drawable.img6};

    public ImageAdapter(Context c) {
        mContext = c;
        init_idx();
    }

    public void init_idx() {
        idx1 = -1;
        idx2 = -1;
    }

    public void set_idx(int idx) {
        if (idx1 == -1) {
            idx1 = idx;
        } else {
            idx2 = idx;
            changeImg();
        }
    }

    public void changeImg() {
        Integer temp;
        temp = Idata[idx1];
        Idata[idx1] = Idata[idx2];
        Idata[idx2] = temp;
        init_idx();
        notifyDataSetChanged();
    }

    // 어댑터로 처리된 데이터 개수 반환
    @Override
    public int getCount() {
        return Idata.length;
    }

    // 특정 위치에 있는 데이터 반환
    @Override
    public Object getItem(int i) {
        return null;
    }

    // 특정 위치에 있는 데이터의 id 반환
    @Override
    public long getItemId(int i) {
        return 0;
    }

    // 특정 위치의 데이터를 출력할 뷰를 얻는다.
    @Override
    public View getView(int i, View view, ViewGroup viewGroup) {
        ImageView imageView;
        if (view == null) {
            imageView = new ImageView(mContext);

            // 그리드뷰에 이미지 뷰가 출력될 때 정렬 방법 지정 (세부설정)
            imageView.setLayoutParams(new GridView.LayoutParams(200, 200));
            imageView.setScaleType(ImageView.ScaleType.CENTER_CROP);
            imageView.setPadding(8, 8, 8, 8);
        } else {
            imageView = (ImageView) view;
        }
        imageView.setImageResource(Idata[i]);
        return imageView;
    }
}
```
