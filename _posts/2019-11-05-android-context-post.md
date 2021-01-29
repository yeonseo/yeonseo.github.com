---
title: Android 위젯(Context)
author: Yeonseo Nam
date: 2019-11-05 19:34:00 +0900
categories: [Android, Tutorial]
tags: [Android, Widget, Context, Java, TIL, Today I Leaned]
comments : true
---

* Development Env.
* post date : 2019. 11. 05
* OS : macOS Catalina 64bit
* Java version : JDK 1.8.0 J_220 JRE8
* Android : Pie(9.0)
* Eclipse : 2019-06 (4.12.0)


# Context

## MainActivity.java

```java
	public class MainActivity extends AppCompatActivity {

	/*변수선언한 부분*/
	...

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        iv1 = findViewById(R.id.iv1);
        iv1.setImageResource(R.mipmap.iv1);
        
		//Context Menu 이용할 시에 액션을 받을 객체를 셋팅해준다.
        registerForContextMenu(iv1);


    }

   ...

}
```

### Option Menu를 구현할 시,

```java
	@Override
    public boolean onCreateOptionsMenu(Menu menu) {
        MenuInflater menuInflater = getMenuInflater();
        menuInflater.inflate(R.menu.context_menu, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        handlerContextAction(item);

        return true;
    }
```

### Context Menu를 구현할 시,
* 기본으로 길게 터치할 시, 액션이 실행됨으로 앱을 만들고 클릭해서 반응하지 않는다고 당황하지 말자.


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

메뉴에 대한 xml 파일을 만든다.

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
    
    
    //RadioButton으로 메뉴를 만들고 싶을 때, (참고로 멀티로 하면 체크박스가 된다.)
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

