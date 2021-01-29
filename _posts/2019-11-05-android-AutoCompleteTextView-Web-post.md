---
title: Android Web & AutoCompleteTextView
author: Yeonseo Nam
date: 2019-11-05 14:34:00 +0900
categories: [Android, Tutorial]
tags: [Android, Widget, Web, AutoCompleteTextView, Java, TIL, Today I Leaned]
comments : true
---

* Development Env.
* post date : 2019. 11. 05
* OS : macOS Catalina 64bit
* Java version : JDK 1.8.0 J_220 JRE8
* Android : Pie(9.0)
* Eclipse : 2019-06 (4.12.0)

# WebView

웹페이지를 구현할 수 있는 WebView입니다.
주의 할 점은 manifests에 있는 AndroidManifest.xml을 수정해 주어야 합니다. <application> 구문 위에 아래의 문장을 추가해 줍니다.

```java
<uses-permission android:name=“android.permission.INTERNET” />
```

## MainActivity.java

```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ...

        webView = findViewById(R.id.webView);

        //브러우저를 가지고 옵니다.
        webView.setWebViewClient(new CookWebViewClient());

        //웹 셋팅을 합니다.
        final WebSettings webSet = webView.getSettings();
        //크고 작게하는 기능을 줍니다.
        webSet.setBuiltInZoomControls(true);

   ...

   btnGo.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            webView.loadUrl("https://"+editText.getText().toString());
        }
    });

    btnBack.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            webView.goBack();
        }
    });
}
```


# AutoCompleteTextView

## MainActivity.java

```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ...

		autoCompleteTextView = findViewById(R.id.autoCompleteTextView);

        btnAdd.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                data.add(autoCompleteTextView.getText().toString());
                Toast.makeText(getApplicationContext(),autoCompleteTextView.getText().toString(),Toast.LENGTH_LONG).show();

                //새애 : update로 해볼것
                ArrayAdapter<String> adapter = new ArrayAdapter<String>(
                        MainActivity.this, android.R.layout.simple_dropdown_item_1line, data.toArray(new String[data.size()]));
                autoCompleteTextView.setAdapter(adapter);
            }
        });
```



### Context Menu를 구현할 시,
* Context Menu는 기본값으로 길게 터치할 시, 액션이 실행됩니다. 앱을 만들고 난 후, 짧게 터치해서 반응하지 않는다고 당황하지 마시기 바랍니다.


```java
	@Override
    public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
        super.onCreateContextMenu(menu, v, menuInfo);
        MenuInflater menuInflater = getMenuInflater();
        menuInflater.inflate(R.menu.context_menu, menu);

    }

    @Override
    public boolean onContextItemSelected(@NonNull MenuItem item) {
        handlerContextAction(item);
        return true;
    }
```


#### 함수화한 메뉴 구현부

```java
public void handlerContextAction(MenuItem item){
        switch (item.getItemId()) {
            case R.id.option1:
                rotValue = rotValue + 45f;
                iv1.setRotation(rotValue);
                break;
            case R.id.option2:
                ivSize = ivSize+1f;
                iv1.setScaleX(ivSize);
                iv1.setScaleY(ivSize);
                break;
            case R.id.option3:
                ivSize = ivSize-1f;
                iv1.setScaleX(ivSize);
                iv1.setScaleY(ivSize);
                break;
            default:
                break;
        }
    }
```


## res/menu

메뉴에 대한 xml 파일을 만들어 사용하면 됩니다.

```java
    <?xml version="1.0" encoding="utf-8"?>
    <menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/option1"
        android:title="회전" />
    <item
        android:id="@+id/option2"
        android:title="확대" />
    <item
        android:id="@+id/option3"
        android:title="축소" />
        
    //하위 메뉴그룹을 만들고 싶을 때,
    <item android:title="Button Change">

        <menu>
            <item
                android:id="@+id/subRotate"
                android:title="Rot 45">
            </item>
            <item
                android:id="@+id/subSize"
                android:title="View Size">
            </item>

        </menu>
    </item>
    
    
    //RadioButton으로 메뉴를 만들고 싶을 때, (참고로 멀티로 하면 체크박스가 됩니다.)
     <group android:checkableBehavior="single" >
        <item
            android:id="@+id/item1"
            android:checked="true"
            android:title="한라산">
        </item>
        <item
            android:id="@+id/item2"
            android:title="추자도">
        </item>
        <item
            android:id="@+id/item3"
            android:title="범섬">
        </item>
    </group>


    </menu>
```

