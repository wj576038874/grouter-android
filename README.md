# AndroidRouter
通过注解方式来实现URL打开Activity功能，并支持在WebView和外部浏览器使用，支持多级Activity跳转，支持Bundle、Uri参数注入并转换参数类型。
### 特点
1. 支持注解方式、手动方式注册Activity。
2. 支持注入Bundle、Uri的参数并转换格式。
3. 支持多级跳转。
4. 支持外部浏览器打开。
5. 支持HTTP协议。

### 简单例子
```
@RouterActivity("second")
public class SecondActivity extends Activity {
    @RouterField("uid")
    private int uid;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);
        Routers.inject(this);
        Log.e("uid", String.valueOf(uid));
    }
}
```
```
Routers.init("joyrun");//设置Scheme
Routers.startActivity(context, "joyrun://second?uid=233");
```

### 多级跳转

```
@RouterActivity("third")
public class ThirdActivity extends BaseActivity {
    @RouterField("uid")
    private int uid;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_third);
        Routers.inject(this);
        Log.e("uid", String.valueOf(uid));
    }
}
```
```
// 先打开SecondActivity，再打开ThirdActivity
Routers.startActivity(context, "joyrun://second/third?uid=233");
```
### Bundle、Uri参数注入（支持单独使用）
Routers.inject(this)方法可以`单独使用`，可以实现注入Bundle、Uri的参数，由于Uri的参数是String类型，所以该框架还支持把String格式的类型转换为目标类型。目前该方法支持double、float、int、boolean、String数据类型。
```
// Bundle
Intent intent = new Intent(this, SecondActivity.class);
intent.putExtra("uid", "233");
startActivity(intent);
// Uri
Intent intent = new Intent(this, SecondActivity.class);
intent.setData(Uri.parse("joyrun://second?uid=233"));
startActivity(intent);
// Bundle、Uri
Intent intent = new Intent(this, SecondActivity.class);
intent.setData(Uri.parse("joyrun://second?uid=233"));
intent.putExtra("name", "Wiki");
startActivity(intent);
```
### 从外部浏览器、其它APP打开
只要在AndroidManifest.xml注册了RouterCenterActivity，即可变成经典的Uri打开，可以支持外部浏览器、其它APP打开内部的Activity。
```
<activity android:name="com.thejoyrun.router.RouterCenterActivity">
    <intent-filter>
        <category android:name="android.intent.category.DEFAULT" />
        <action android:name="android.intent.action.VIEW" />
        <data android:scheme="joyrun" />
    </intent-filter>
</activity>
```
```
// Java代码调用
startActivity(new Intent(Intent.ACTION_VIEW, Uri.parse("joyrun://second?uid=233&name=Wiki")));
```
```
// HTML方式，系统浏览器（不支持微信）
<a href="joyrun://second?uid=233&name=Wiki">打开JoyrunApp的SecondActivity</a>
```

### 支持HTTP协议
```
<activity android:name="com.thejoyrun.router.RouterCenterActivity">
    <intent-filter>
        <category android:name="android.intent.category.DEFAULT" />
        <action android:name="android.intent.action.VIEW" />
        <data android:scheme="joyrun" />
    </intent-filter>
    <intent-filter>
        <category android:name="android.intent.category.DEFAULT" />
        <action android:name="android.intent.action.VIEW" />
        <data android:host="www.thejoyrun.com" android:scheme="http" />
    </intent-filter>
</activity>
```
如果支持HTTP协议，那么URL的结构就要做些修改
```
// joyrun://second?uid=233
// =>
http://www.thejoyrun.com/second?uid=233
```