## 1. 初次使用 Getting start with PLDroidPlayer SDK
### 1.1 准备工作
* Android Studio 开发工具。官方[下载地址](http://developer.android.com/intl/zh-cn/sdk/index.html)
* 下载 Android SDK 。官方[下载地址](https://developer.android.com/intl/zh-cn/sdk/index.html#Other)。PLDroidPlayer 要求 Min API 9
* 在[这里](https://github.com/pili-engineering/PLDroidPlayer/releases)下载最新的 JAR 和 SO

### 1.2 空白范例
#### 第一次推流
* 通过 Android Studio 创建 Project
![](../screenshots/sbs/sbs_new_project.png)

* 设置 Application name, Company Domain, Package name, Project location
![](screenshots/sbs/sbs_configure_new_project.png)

* 选择 Target Android Devices
![](screenshots/sbs/sbs_target_android_devices.png)

* 选择 Empty Activity
![](../screenshots/sbs/sbs_empty_activity.png)

* 填写 Main Activity 信息，作为 android.intent.action.MAIN
![](../screenshots/sbs/sbs_set_main_activity.png)

* 完成创建
![](../screenshots/sbs/sbs_new_project_done.png)

* 加入 PLDroidCameraStreaming SDK 动态链接库和 JAR 依赖
![](screenshots/sbs/sbs_import_jar_so.png)

涉及的文件名如下：

```
// JAR
pldroid-player-1.1.5.5.jar

// SO
libpldroidplayer.so
```
* 在 AndroidManifest.xml 中增加 uses-permission ，并声明播放 Activity

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.pili.demo.playersdk" >

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme" >
        <activity android:name=".MainActivity" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>

```

* 在 app 目录下的 build.gradle :

```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 22
    buildToolsVersion "22.0.1"

    defaultConfig {
        applicationId "com.pili.demo.playersdk"
        minSdkVersion 16
        targetSdkVersion 22
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:22.0.0'
    compile files('libs/ijkmediaplayer.jar')
    compile files('libs/pldroid-player-1.1.5.5.jar')
}
```

注意：是 app 目录下的 build.gradle

* 编写 MainActivity 逻辑

为了演示方便，将 `MainActivity` 父类 `AppCompatActivity` 更改为 `Activity`。

MainActivity 主要做如下事情：
1. 初始化 VideoView
2. 设置 AVOptions
3. 传入播放地址，并开始播放

```
public class MainActivity extends Activity {

    private VideoView mVideoView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mVideoView = (VideoView) findViewById(R.id.video_view);

        AVOptions options = new AVOptions();
        options.setInteger(AVOptions.KEY_MEDIACODEC, 0); // 1 -> enable, 0 -> disable

//        if (mIsLiveStream) {
//            options.setInteger(AVOptions.KEY_BUFFER_TIME, 1000); // the unit of buffer time is ms
//            options.setInteger(AVOptions.KEY_GET_AV_FRAME_TIMEOUT, 10 * 1000); // the unit of timeout is ms
//            options.setString(AVOptions.KEY_FFLAGS, AVOptions.VALUE_FFLAGS_NOBUFFER); // "nobuffer"
//            options.setInteger(AVOptions.KEY_LIVE_STREAMING, 1);
//        }

        mVideoView.setAVOptions(options);
//
//        mVideoView.setOnErrorListener(this);
//        mVideoView.setOnCompletionListener(this);
//        mVideoView.setOnInfoListener(this);
//        mVideoView.setOnPreparedListener(this);
//        mVideoView.setOnVideoSizeChangedListener(this);

        // You should input the playback url
        mVideoView.setVideoPath("Your playback url");
        mVideoView.requestFocus();
    }
}
```

activity_layout.xml

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/background_material_light" >

    <com.pili.demo.playersdk.widget.AspectLayout
        android:id="@+id/aspect_layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <com.pili.pldroid.player.widget.VideoView
            android:id="@+id/video_view"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_centerInParent="true"
            />
    </com.pili.demo.playersdk.widget.AspectLayout>
</RelativeLayout>
```

AspectLayout.java

1. VideoView 的外层 layout
2. 可以实现输入弹框弹起不影响当前播放界面

```
public class AspectLayout extends RelativeLayout {
    private static final String TAG = "AspectLayout";

    private int mWidthMeasureSpec;

    private int mRootHeight = 0;
    private int mRootWidth = 0;

    public AspectLayout(Context context) {
        super(context);
        initialize(context);
    }

    public AspectLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
        initialize(context);
    }

    public AspectLayout(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        initialize(context);
    }

    private void initialize(Context ctx) {
    }

    @TargetApi(21)
    public AspectLayout(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        Log.d(TAG, "onMeasure" + " width=[" + MeasureSpec.toString(widthMeasureSpec) +
                "] height=[" + MeasureSpec.toString(heightMeasureSpec) + "]");

        Rect r = new Rect();
        getWindowVisibleDisplayFrame(r);
        Pair<Integer, Integer> screenSize = Util.getResolution(getContext());

        if (mRootWidth == 0 && mRootHeight == 0) {
            mRootWidth = getRootView().getWidth();
            mRootHeight = getRootView().getHeight();
        }
        int totalHeight = 0;

        if (screenSize.first > screenSize.second) {
            // land
            totalHeight = mRootWidth > mRootHeight ? mRootHeight : mRootWidth;
        } else {
            // port
            totalHeight = mRootWidth < mRootHeight ? mRootHeight : mRootWidth;
        }

        int nowHeight = r.bottom - r.top;

        if (totalHeight - nowHeight > totalHeight / 4) {
            // soft keyboard show
            super.onMeasure(mWidthMeasureSpec, MeasureSpec.makeMeasureSpec(nowHeight + totalHeight - nowHeight, MeasureSpec.EXACTLY));
            return;
        } else {
            // soft keyboard hide
        }

        mWidthMeasureSpec = widthMeasureSpec;

        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    }
}
```

至此，启动 APP 之后，就可以播放视频了。 Enjoy!
