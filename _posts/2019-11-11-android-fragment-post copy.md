---
title: Android Fragment&(Web View, Canvas, Imgae Edit)
author: Yeonseo Nam
date: 2019-11-11 19:34:00 +0900
categories: [Android, Tutorial]
tags: [Android, Layout, Fragment,Web, View,Canvas,Imgae ,Java, TIL, Today I Leaned]
comments : true
---
* Development Env.
* post date : 2019. 11. 11
* OS : macOS Catalina 64bit
* Java version : JDK 1.8.0 J_223 JRE8
* Android : Pie(9.0)
* Eclipse : 2019-06 (4.12.0)

# Fragment View
Fragment View는 액티비티를 통해 구현한 것과 비슷하게 각 화면에서 동작하도록 설계할 수 있습니다. 하지만 액티비티와 다른 점은 부분화면을 독립적으로 모듈화 할 수 있다는 점이고, 이 부분이 Fragment View가 가지는 장점입니다.

Fragment View를 사용하면 여러명이 각각의 화면을 설계하여 합치기 좋습니다. 하나의 프로그램을 만들기 위해서 팀원들이 각각의 맡은 메뉴를 구현하고, 이후에 합치는 방식으로 개발을 합니다. 이때 팀장이 각각의 화면을 합쳐 모듈화 된 화면을 하나의 프로그램으로 완성합니다.

특히 시스템이 직접 관리하지 않기 때문에 가볍다는 이야기를 들었는데, 나중에 기회가 된다면 같은 기능을 액티비티와 Fragment View를 구현하여 비교하는 포스트를 써보겠습니다.

Fragment View View를 만드는 방법은 다른 블로그에서도 많이 다룬 내용이므로 생략하고, 배우면서 주의해야 했던 점들과 간단한 과제를 하면서 생긴 이슈에 대해서 다뤄보도록 하겠습니다. 여기에 사용된 코드는 GitHub에 올려두었으므로 자유롭게 참고하시면 됩니다.




## Fragment View 주의할 점 (MainActivity.java)

### 1.  Fragment 상속
* Mac 사용 시 : import androidx.fragment.app.Fragment;
* Windows 사용 시 : import android.support.v4.app.Fragment;

 상속 받을 때, 위의 속성으로 받아야 합니다. 다른 Fragment를 상속 받으면 오류도 뜨지 않으면서 실행이 되지 않습니다. 상속할 때 주의해서 작성합니다.

### 2. xml 파일은  FrameLayout으로 작성
다른 레이아웃을 사용해 보지는 않았지만, Fragment View에 대한 예제 및 과제를 할 때 모두 FrameLayout으로 뷰를 작성하였습니다. Fragment View를 사용할 시, FrameLayout를 컨테이너로 많이 사용되는데, FrameLayout이 가진 특성때문이라고 생각합니다.

### 3. 각각의 Fragment를 이벤트 발생 시, 연결
버튼을 클릭하는 이벤트 발생 시에 각각의 Fragment를 가진 액티비티를 불러오도록 코드를 작성하였습니다. 이때  commit(); 이라는 함수를 호출하여 작업을 완료하였습니다.  


### MainActivity.java

```java

package com.example.a9fregmentviewtest;

import androidx.appcompat.app.AppCompatActivity;
import androidx.fragment.app.Fragment;
import androidx.fragment.app.FragmentActivity;
import androidx.fragment.app.FragmentTransaction;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.FrameLayout;

public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    FrameLayout frameLayout;
    //합칠 때 팀장이 하는 것
    Button btnMenu1, btnMenu2, btnMenu3, btnMenu4;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        frameLayout = findViewById(R.id.frameLayout);
        btnMenu1 = findViewById(R.id.btnMenu1);
        btnMenu2 = findViewById(R.id.btnMenu2);
        btnMenu3 = findViewById(R.id.btnMenu3);
        btnMenu4 = findViewById(R.id.btnMenu4);

        btnMenu1.setOnClickListener(this);
        btnMenu2.setOnClickListener(this);
        btnMenu3.setOnClickListener(this);
        btnMenu4.setOnClickListener(this);

        btnMenu1.callOnClick(); //버튼을 부르는 함수
//        이렇게 해야하지만!!
//        FragmentTransaction ft =
//                getSupportFragmentManager().beginTransaction();
//        Fragment fragmentActivity = null;
//        fragmentActivity = new Fragment1Activity();
//        ft.replace(R.id.frameLayout, fragmentActivity);
//        ft.commit();

    }

    @Override
    public void onClick(View v) {
        FragmentTransaction ft =
                getSupportFragmentManager().beginTransaction();
        Fragment fragmentActivity = null;
        switch (v.getId()) {
            case R.id.btnMenu1:
                fragmentActivity = new Fragment1Activity();
                break;
            case R.id.btnMenu2:
                fragmentActivity = new Fragment2Activity();
                break;
            case R.id.btnMenu3:
                fragmentActivity = new Fragment3Activity();
                break;
            case R.id.btnMenu4:
                fragmentActivity = new Fragment4Activity();
                break;
        }
        ft.replace(R.id.frameLayout, fragmentActivity);
        ft.commit();
    }
}
```



## Fragment View 주의할 점 (FragmentActivity.java)

### 1.  생성자 만들어 주기
밑의 코드를 보면 아무 내용을 적지 않고 생성자를 만들어 주었습니다. 생성자를 만들지 않아도 에러가 나는 것은 아니지만, 위의 MainActivity.java에서 fragmentActivity 를 구현하기 위해 Fragment1Activity()를 객체 생성에 사용해서 생성자를 만들어 주었습니다.

```java
 …
getSupportFragmentManager().beginTransaction();
        Fragment fragmentActivity = null;
        switch (v.getId()) {
            case R.id.btnMenu1:
                fragmentActivity = new Fragment1Activity();
                break;
 …
```


### 2. public View onCreateView(…) (return view!)
액티비티에서 onCreate()가 return값이 void였던 것과 비교하면 onCreateView() 는 return값이 view인 것을 확인할 수 있습니다. 개인적으로 Fragment의 독립적인 면이 많이 보였다고 생각합니다. 
돌려줄 필요가 없이 스스로 구현했던 액티비티와 달리 Fragment는 독립적으로 구현되기 때문에 자신의 view 정보를 넘겨주어야 합니다. return된 view를 받아 replace(), commit()을 통해 구현합니다. 이 부분이 눈으로 보이는 차이라고 생각합니다.
리턴되는 view는 여러가지 방법으로 서술할 수 있었습니다. 때문에 잘 이해해야 Fragment를 잘 활용할 수 있습니다. Fragment도 생명주기를 가지고 있기 때문에 또 복잡합니다만, 제가 이해한 부분까지만 정리해 보겠습니다.
찾아본 바로는 여러가지 구현방법이 있는데, 제가 사용한 3가지에 대해서 좀 더 정리해보겠습니다.




## Web View
웹 뷰에서는 사실 특별히 return에 신경쓰지 않았습니다. 웹 뷰라는 것 자체가 기능을 다 하고 있으므로 레이아웃에 대한 정보를 담고 있는 view를 그대로 돌려주었습니다.

```java
view = inflater.inflate(R.layout.fragment1, container, false);
```


### Fragment1Activity. Java

```java
package com.example.a9report_fragment_web_file_;

import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.webkit.WebSettings;
import android.webkit.WebView;
import android.webkit.WebViewClient;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;

public class Fragment1Activity extends Fragment{
    EditText frag1EdtText;
    Button frag1BtnGo, frag1BtnBack;
    WebView frag1WebView;

    View view;

    //Fragment import할 시 import androidx.fragment.app.Fragment;로 할 것. 조심!
    public Fragment1Activity() {
    }

    @Nullable
    @Override
    //setContentView와 같다.
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        view = inflater.inflate(R.layout.fragment1, container, false);
        frag1EdtText = view.findViewById(R.id.frag1EdtText);
        frag1BtnGo = view.findViewById(R.id.frag1BtnGo);
        frag1BtnBack = view.findViewById(R.id.frag1BtnBack);
        frag1WebView = view.findViewById(R.id.frag1WebView);

        //브러우저를 가지고 옴
        frag1WebView.setWebViewClient(new CookWebViewClient());

        //웹 셋팅을 함
        final WebSettings webSet = frag1WebView.getSettings();
        //크고 작게하는 기능을 줌
        webSet.setBuiltInZoomControls(true);
        webSet.setJavaScriptEnabled(true); // 웹페이지 자바스클비트 허용 여부
        webSet.setSupportMultipleWindows(false); // 새창 띄우기 허용 여부
        webSet.setJavaScriptCanOpenWindowsAutomatically(false); // 자바스크립트 새창 띄우기(멀티뷰) 허용 여부
        webSet.setLoadWithOverviewMode(true); // 메타태그 허용 여부
        webSet.setUseWideViewPort(true); // 화면 사이즈 맞추기 허용 여부
        webSet.setSupportZoom(false); // 화면 줌 허용 여부
        webSet.setBuiltInZoomControls(false); // 화면 확대 축소 허용 여부
        webSet.setLayoutAlgorithm(WebSettings.LayoutAlgorithm.SINGLE_COLUMN); // 컨텐츠 사이즈 맞추기
        webSet.setCacheMode(WebSettings.LOAD_NO_CACHE); // 브라우저 캐시 허용 여부
        webSet.setDomStorageEnabled(true); // 로컬저장소 허용 여부


        frag1BtnGo.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                frag1WebView.loadUrl(“https://“ + frag1EdtText.getText().toString());
            }
        });

        frag1BtnBack.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                frag1WebView.goBack();
            }
        });

        return view;
    }

    private void displayToast(String message) {
        Toast.makeText(view.getContext(), message, Toast.LENGTH_SHORT).show();
    }

    private class CookWebViewClient extends WebViewClient {
        @Override
        public boolean shouldOverrideUrlLoading(WebView view, String url) {

            return super.shouldOverrideUrlLoading(view, url);
        }
    }
}


```

위에서 사용한 Web View 옵션은 아래의 포스팅을 참고하였습니다.
[WebView 안드로이드 웹뷰 생성하기 :: MAKE SOMETHING](https://web-inf.tistory.com/34)

그리고 Web View를 구현할 때는, AndroidManifest.xml에서 꼭 아래의 설정을 해주도록 합니다.
```java
<uses-permission android:name=“android.permission.INTERNET” />
```





## Canvas 구현
Canvas에 마우스 좌표에 따라 graphic을 그려보았습니다. 이 예제부터는 두가지의 view를 표현해야 합니다. 첫번째는 레이아웃의 정보를 담고 있는 view와 graphics의 정보를 담고 있는  view가 필요합니다. 


```java
public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        view = inflater.inflate(R.layout.fragment3, container, false);
        setHasOptionsMenu(true);
        return new MyGraphicView(view.getContext());
    }
```
onCreateView()를 살펴보겠습니다.  위의 웹뷰인 경우와 같이 view = inflater.inflate(R.layout.fragment3, container, false);를 통해 레이아웃에 대한 정보를 주었습니다.

옵션메뉴를 만들기 위해서 setHasOptionsMenu();라는 함수를 사용하였는데, 액티비테에서는 onCreateOptionsMenu() 함수를 만들면 되지만, Fragment에서 옵션메뉴를 활성화 하기 위해서 true값을 주어야 합니다. 옵션메뉴를 만드는 방법은 동일합니다.

마지막으로 리턴값인데, View가 아닌 MyGraphicView를 주었습니다. MyGraphicView 내부클래스는 그림을 그리기 위해 만든 클래스입니다. 때문에 Canvas와 Graphics 등의 정보는 레이아웃(View)에는 담겨있지 않습니다. 그 정보들은 MyGraphicView가 가지고 있는 정보입니다.

이 정보들을 값을 넘겨주기 위해서는 return이 MyGraphicView에 대한 것어여야 합니다.


### Fragment3Activity. Java

```java
package com.example.a9report_fragment_web_file_;

import android.Manifest;
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.graphics.RectF;
import android.os.Bundle;
import android.util.AttributeSet;
import android.view.LayoutInflater;
import android.view.Menu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.view.MotionEvent;
import android.view.SubMenu;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import androidx.fragment.app.Fragment;

public class Fragment3Activity extends Fragment {
    final static int LINE = 1, CIRCLE = 2, RECTANGLE = 3;
    public int curShape = LINE;
    public int COLOR = Color.BLACK;
    View view;

    public Fragment3Activity() {
    }

    @Nullable
    @Override
    //setContentView와 같다.
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        view = inflater.inflate(R.layout.fragment3, container, false);
        setHasOptionsMenu(true);
        return new MyGraphicView(view.getContext());
    }


    @Override
    public void onCreateOptionsMenu(@NonNull Menu menu, @NonNull MenuInflater inflater) {
        super.onCreateOptionsMenu(menu, inflater);
        menu.add(0, 1, 0, “선그리기”);
        menu.add(0, 2, 0, “원그리기”);
        menu.add(0, 3, 0, “사각형그리기”);
        SubMenu sMenu = menu.addSubMenu(“버튼 변경 >>”);
        sMenu.add(0, 4, 0, “빨강”);
        sMenu.add(0, 5, 0, “파랑”);
        sMenu.add(0, 6, 0, “노랑”);
    }

    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        switch (item.getItemId()) {
            case 1:
                curShape = LINE;
                break;
            case 2:
                curShape = CIRCLE;
                break;
            case 3:
                curShape = RECTANGLE;
                break;
            case 4:
                COLOR = Color.RED;
                break;
            case 5:
                COLOR = Color.BLUE;
                break;
            case 6:
                COLOR = Color.YELLOW;
                break;
        }
        return true;
    }

    private void displayToast(String message) {
        Toast.makeText(view.getContext(), message, Toast.LENGTH_SHORT).show();
    }

    private class MyGraphicView extends View {
        public float startX = -1, startY = -1, stopX = -1, stopY = -1;

        public MyGraphicView(Context context) {
            super(context);
        }

        public MyGraphicView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
            super(context, attrs, defStyleAttr);
        }


        //선을 그릴지 원을 그릴지 메뉴에서 선택정보가 있어야 함.
        //디폴트: 선
        //터치하는 좌표정보가 넘어와야 함.

        //화면에서 이벤트 정보를 받는 함수
        @Override
        public boolean onTouchEvent(MotionEvent event) {
            switch (event.getAction()) {
                case MotionEvent.ACTION_DOWN:
                    startX = event.getX();
                    startY = event.getY();
                    break;
                case MotionEvent.ACTION_UP:
                    stopX = event.getX();
                    stopY = event.getY();

                    //무효화영역 처리
                    invalidate();
                    break;
                case MotionEvent.ACTION_MOVE:
                    break;
                case MotionEvent.ACTION_CANCEL:
                    break;
            }
            return true;
        }

        @Override
        protected void onDraw(Canvas canvas) {
            super.onDraw(canvas);
            Paint paint = new Paint();
            paint.setAntiAlias(true);
            paint.setColor(Color.GREEN);
            paint.setStrokeWidth(5);
            paint.setColor(COLOR);
            paint.setStyle(Paint.Style.FILL);

            switch (curShape) {
                case LINE:
                    canvas.drawLine(startX, startY, stopX, stopY, paint);
                    break;
                case CIRCLE:
                    int radius = (int) Math.sqrt(Math.pow(stopX - startX, 2)
                            + Math.pow(stopY - startY, 2));
                    canvas.drawCircle(startX, startY, radius, paint);
                    break;
                case RECTANGLE:
                    RectF rectF = new RectF();
                    rectF.set(startX, startY, stopX, stopY);
                    canvas.drawRect(rectF, paint);
                    break;
            }

        }
    }


}

```




## Fragment를 사용하여 이미지 영상처리 구현

이번에는 MyGraphicView에서 canvas에 graphics를 그려내는 것이 아닌, 그림을 불러와 영상처리를 하도록 만들었습니다. Fragment도 생명주기를 가지고 있기 때문에 위에서 했던 방식으로 return을 해 준다면, 영상처리에 셋팅값을 변경해주어도 당장 변화를 보여주지 않습니다. 위에서 그리는 동작을 통해 마우스의 움직임에 따라 이벤트를 구현했다면,  이번엔 화면 전환이 이뤄지는 등의 화면이 다시 불려지는 상황이 발생해야 변경된 설정값을 구현합니다.
즉, MyGraphicView의 화면을 새로 그리도록 명령해야 하는데 그러기 위해서는 invalidate() 함수 처리를 할 수 있어야 하고, 때문에 MyGraphicView 타입의 변수를 선언할 필요가 있습니다. 

```java
public Fragment4Activity() {
    }

    @Nullable
    @Override
    //setContentView와 같다.
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        view = inflater.inflate(R.layout.fragment3, container, false);
        setHasOptionsMenu(true);
        gView = new MyGraphicView(view.getContext());

        return gView;
    }
```



### Fragment4Activity. Java

```java
package com.example.a9report_fragment_web_file_;

import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.BlurMaskFilter;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.ColorMatrix;
import android.graphics.ColorMatrixColorFilter;
import android.graphics.EmbossMaskFilter;
import android.graphics.Paint;
import android.os.Bundle;
import android.util.AttributeSet;
import android.view.LayoutInflater;
import android.view.Menu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.view.SubMenu;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.Fragment;

public class Fragment4Activity extends Fragment {
    final static int ROTATION = 1, TRANCELATE = 2, SCALE = 3, SKEW = 4,
            NORMAL = 5, INNER = 6, OUTER = 7, SOLID = 8,
            EM1 = 9, EM2 = 10, EM3 = 11, EM4 = 12, EM5 = 13;
    public static int func = ROTATION;
    public int COLOR = Color.BLACK;
    View view;
    MyGraphicView gView;

    public Fragment4Activity() {
    }

    @Nullable
    @Override
    //setContentView와 같다.
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        view = inflater.inflate(R.layout.fragment3, container, false);
        setHasOptionsMenu(true);
        gView = new MyGraphicView(view.getContext());

        return gView;
    }


    @Override
    public void onCreateOptionsMenu(@NonNull Menu menu, @NonNull MenuInflater inflater) {
        super.onCreateOptionsMenu(menu, inflater);
        SubMenu sMenu1 = menu.addSubMenu("기하학적 변경 >>");
        sMenu1.add(0, 1, 0, "회전");
        sMenu1.add(0, 2, 0, "변경");
        sMenu1.add(0, 3, 0, "크기변환");
        sMenu1.add(0, 4, 0, "비틀기");
        SubMenu sMenu2 = menu.addSubMenu("블러 변경 >>");
        sMenu2.add(0, 5, 0, "노말");
        sMenu2.add(0, 6, 0, "이너");
        sMenu2.add(0, 7, 0, "아우터");
        sMenu2.add(0, 8, 0, "솔리드");
        menu.add(0, 9, 0, "마스크");
        SubMenu sMenu3 = menu.addSubMenu("엠보싱 변경 >>");
        sMenu3.add(0, 10, 0, "1");
        sMenu3.add(0, 11, 0, "2");
        sMenu3.add(0, 12, 0, "3");
        sMenu3.add(0, 12, 0, "4");

    }

    @Override
    public boolean onOptionsItemSelected(@NonNull MenuItem item) {
        switch (item.getItemId()) {
            case 1:
                func = ROTATION;
                gView.invalidate();
                break;
            case 2:
                func = TRANCELATE;
                gView.invalidate();
                break;
            case 3:
                func = SCALE;
                gView.invalidate();
                break;
            case 4:
                func = SKEW;
                gView.invalidate();
                break;
            case 5:
                func = NORMAL;
                gView.invalidate();
                break;
            case 6:
                func = INNER;
                gView.invalidate();
                break;
            case 7:
                func = OUTER;
                gView.invalidate();
                break;
            case 8:
                func = SOLID;
                gView.invalidate();
                break;
            case 9: //마스크변경
                func = EM1;
                gView.invalidate();
                break;
            case 10: //엠보싱변경
                func = EM2;
                gView.invalidate();
                break;
            case 11: //엠보싱변경
                func = EM3;
                gView.invalidate();
                break;
            case 12: //엠보싱변경
                func = EM4;
                gView.invalidate();
                break;
            case 13: //엠보싱변경
                func = EM5;
                gView.invalidate();
                break;
        }
        return true;
    }


    private void displayToast(String message) {
        Toast.makeText(view.getContext(), message, Toast.LENGTH_SHORT).show();
    }

    public static class MyGraphicView extends View {
        public float startX = -1, startY = -1, stopX = -1, stopY = -1;
        BlurMaskFilter bMask;
        EmbossMaskFilter eMask;

        public MyGraphicView(Context context) {
            super(context);
        }

        public MyGraphicView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
            super(context, attrs, defStyleAttr);
        }

        @Override
        public void onDraw(Canvas canvas) {
            super.onDraw(canvas);
            Bitmap picture = BitmapFactory.decodeResource(getResources(), R.drawable.pic1);
            Paint paint = new Paint();


            int cenX = this.getWidth() / 2;
            int cenY = this.getHeight() / 2;
            int picX = (this.getWidth() - picture.getWidth()) / 2;
            int picY = (this.getHeight() - picture.getHeight()) / 2;

            switch (func) {
                case 1:
                    canvas.rotate(45, cenX, cenY);
                    displayToast("1");
                    break;
                case 2:
                    canvas.translate(-150, 200);
                    displayToast("2");
                    break;
                case 3:
                    canvas.scale(2, 2, cenX, cenY);
                    displayToast("3");
                    break;
                case 4:
                    canvas.skew(0.3f, 0.3f);
                    displayToast("4");
                    break;
                case 5:
                    bMask = new BlurMaskFilter(30, BlurMaskFilter.Blur.NORMAL);
                    break;
                case 6:
                    bMask = new BlurMaskFilter(30, BlurMaskFilter.Blur.INNER);
                    break;
                case 7:
                    bMask = new BlurMaskFilter(30, BlurMaskFilter.Blur.OUTER);
                    break;
                case 8:
                    bMask = new BlurMaskFilter(30, BlurMaskFilter.Blur.SOLID);
                    break;
                case 9:
                    float[] array = {2, 0, 0, 0, -25, 0, 2, 0, 0, -25, 0, 0, 2, 0, -25, 0, 0, 0, 1, 0};
                    ColorMatrix cm = new ColorMatrix(array);
                    paint.setColorFilter(new ColorMatrixColorFilter(cm));
                    break;
                case 10:
                    eMask = new EmbossMaskFilter(new float[]{3, 3, 3}, 0.2f, 1, 1);
                    break;
                case 11:
                    eMask = new EmbossMaskFilter(new float[]{10, 3, 3}, 0.2f, 1, 1);
                    break;
                case 12:
                    eMask = new EmbossMaskFilter(new float[]{3, 10, 3}, 0.2f, 1, 1);
                    break;
                case 13:
                    eMask = new EmbossMaskFilter(new float[]{3, 3, 10}, 0.2f, 1, 1);
                    break;
            }

            if (func > 9) {
                paint.setMaskFilter(eMask);
                canvas.drawCircle(cenX, cenY, 10, paint);
            } else if (func > 4 | func <= 9) {
                paint.setMaskFilter(bMask);
                canvas.drawBitmap(picture, picX, picY, paint);
            } else {
                canvas.drawBitmap(picture, picX, picY, null);
            }
            picture.recycle();

        }

        private void displayToast(String message) {
            Toast.makeText(getContext(), message, Toast.LENGTH_SHORT).show();
        }
    }

}

```


Fragment를 사용하는 방법은 다양합니다. 그 중의 하나의 방법을 이야기해보았습니다. 예를 들어 새로운 레이아웃을 사용하여 add하는 방법도 생각할 수 있습니다. 물론 그렇게 된다면 또 하나의 액티비티를 구현해야 할 것 입니다. 
이미지 처리를 위해서 내장 클래스를 사용했고, 때문에 내장 클래스를 위한  View를 다뤄보는 방법으로 구현해 본 코드입니다. Fragmant를 사용해 이미지를 그리거나 그래픽을 그리는 기능 구현에 어려움이 있었다면 이 포스트가 도움이 되셨길 바랍니다.

