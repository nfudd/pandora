an android library for debugging what we care about directly in app.

 [[点击查看中文版]](https://github.com/whataa/pandora/blob/master/README_CN.md) 


# Pandora [![API](https://img.shields.io/badge/API-14%2B-brightgreen.svg?style=flat)](https://android-arsenal.com/api?level=14) [![platform](https://img.shields.io/badge/platform-android-brightgreen.svg)](https://developer.android.com/index.html)  [![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](https://github.com/whataa/pandora-no-op/blob/master/LICENSE) 

Pandora is a tool box that allows you to inspect and modify what includes networks, databases, UIs, etc. directly in your application. It is suitable for rapid position of various problems in the development and testing stages.

## Feature

- Inspect the detailed log of each network request, such as headers, response, etc.
- View the internal storage system of own app;

- View all databases, and support ADD, DELETE, UPDATE, QUERY operations;

- View and edit all Shared Preference;

- Preview the current view Hierarchy, and can view/modify the properties of widgets;

- Measure the distance between the views and detect whether the alignment is correct
- You can select any view on the Activity to move the position, get the size of itself, display the relative relationship;

- More features look forward to you exploring;

Some of the effects are as follows:

The display pictures are: network, database, UI, file

![image](https://note.youdao.com/yws/api/personal/file/WEB5d90fab5127f1cf2664a976380a89418?method=download&shareKey=a9f6caf76cc9abef7d17271b435ca030) ![image](https://note.youdao.com/yws/api/personal/file/WEB681b1401d6f40a7dcdf480b2aff33bef?method=download&shareKey=9e2596df7e42fad75ee3f4fe99766814)

![image](https://note.youdao.com/yws/api/personal/file/WEB46cceded39144f21327bbc113938eb42?method=download&shareKey=6a7a0a7e863a4c75a5f62fcd62d5092a) ![image](https://note.youdao.com/yws/api/personal/file/WEB710b73c107e189afab614b00428b4f7a?method=download&shareKey=d53c1f09302225d6aa293ae023f40d13)

## Set-up [![Release](https://jitpack.io/v/whataa/pandora-no-op.svg)](https://jitpack.io/#whataa/pandora)

1. Add the JitPack repository to your root build file：
```
allprojects {
    repositories {
        ...
        maven { url 'https://jitpack.io' }
    }
}
```
2. Add the dependency to your app build.gradle中：
```
dependencies {
    ...
    debugImplementation 'com.github.whataa:pandora:v1.0.0'
    releaseImplementation 'com.github.whataa:pandora-no-op:v1.0.0'
}  
```

## Usage

init Pandora in Application：

```
public class MyApp extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        Pandora.init(this).enableShakeOpen();
    }
}
```
`enableShakeOpen()` is optional，indicates to enable the "Shake On" feature，Once turned on, shaking the phone on any Activity in your app can open the function panel to start debugging. 

If this feature conflicts with your application, you can also implement your own trigger method and call the following method to open the function panel：

```
Pandora.get().open();
```

Close the function panel：

```
Pandora.get().close();
```

If your project uses OKHttp as the underlying network library, you can add the following interceptor to enable the function of the network debugging module：
```
new OkHttpClient.Builder()
    ...
    .addInterceptor(Pandora.get().getInterceptor())
    ...
    .build()
```

## Extended features

### Inspect the View property
Pandora supports viewing and partially modifying the properties of View, ViewGroup, and common TextView and ImageView by default. If you want to inspect more view attributes, you can expand them in the following ways:

1. implement `tech.linjiang.pandora.inspector.attribute.IParser` interface and specify the type of View that you are interested in. Here is an example of an already implemented ImageView：
```
public class ImageViewParser implements IParser<ImageView> {

    @Override
    public List<Attribute> getAttrs(ImageView view) {
        List<Attribute> attributes = new ArrayList<>();
        // Add the property of interest and return
        Attribute scaleTypeAttribute = new Attribute("scaleType", scaleTypeToStr(view.getScaleType()), Attribute.Edit.SCALE_TYPE);
        attributes.add(scaleTypeAttribute);
        return attributes;
    }
    ...
}
```
2. Add new Parser to Pandora：
```
Pandora.get().getAttrFactory().addParser(new ImageViewParser());
```
After this, every time you click on the ImageView, the property list will automatically enumerate the values of the properties we are interested in.。

### Inspect Shared Preference
Pandora reads by default the XML file in the default SP path in the application（`data/data/<package-name>/shared_prefs/`），If there exist other SP files that are not in the default path, they can be extended in the following ways: 

1. implement `tech.linjiang.pandora.preference.protocol.IProvider` interface，and return the corresponding file list： 

(Specific details can refer to the default implementation in the library`SharedPrefProvider`)

2. Add new Provider to Pandora：
```
Pandora.get().getSharedPref().addProvider(new XXProvider());
```



## Limit

- Minimum supported Android SDK version is **14** ；

- Network debugging module: only supports the network library with OKHttp 3.x  as the underlying network library;

- Database debugging module: Only SQLite-based databases are supported, and viewing encrypted databases is temporarily not supported；

- others;

## Thanks

Pandora was developed on the shoulders of giants. Thanks to the following open source projects:

- Inspired by Flipboard's open source iOS platform debugging tool [FLEX](https://github.com/Flipboard/FLEX)；

- Project database module ideas and part of the source code from Facebook's open source project [stetho](https://github.com/facebook/stetho)；

- The idea of selecting views in the UI module of the project and part of the source code from eleme's open source project [UETool](https://github.com/eleme/UETool)；

- The request API in the Demo module comes from jgilfelt's open source project [chuck](https://github.com/jgilfelt/chuck) ；

## License
[Apache-2.0](https://opensource.org/licenses/Apache-2.0)