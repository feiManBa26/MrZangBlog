---
title: 探究 Java Builder 模式
date: 2019-08-15 09:38:59
tags:
- Android
- Builder
categories:
- Android
- Java
description: java Android 参数传递 Builder 模式 
---

## # Java 设计模式Builder 模式
> Java Builder模式主要是用一个内部类去实例化一个对象，避免一个类出现过多构造函数，而且构造函数如果出现默认参数的话，很容易出错

### Android Builder 模式 实践的第三方库
- Glide `图片加载库`
- UCrop `图片裁剪`
- 等 、、、

以UCrop 构建类 UCrop.java 为例


![](\bulider_01.png)


```java
   UCrop.Options options = new UCrop.Options();
        options.setHideBottomControls(true);
        options.setFreeStyleCropEnabled(clipBoxMove);
        //设置裁剪的图片质量，取值0-100
        options.setCompressionQuality(compress);
        UCrop.of(Uri.parse(sourceFilePath), Uri.parse(outPutFilePath))
                .withAspectRatio(scaleX, scaleY)
                .withOptions(options)
                .start(activity);
```

参数构造集 内部类

![](\bulider_02.png)

构造函数不对外开放 内部逻辑调用

```java
private UCrop(@NonNull Uri source, @NonNull Uri destination) {
    mCropIntent = new Intent();
    mCropOptionsBundle = new Bundle();
    mCropOptionsBundle.putParcelable(EXTRA_INPUT_URI, source);
    mCropOptionsBundle.putParcelable(EXTRA_OUTPUT_URI, destination);
}
```
公开 静态方法 供外部调用

```java
 /**
     * This method creates new Intent builder and sets both 
     * source and destination image URIs.
     *
     * @param source      Uri for image to crop
     * @param destination Uri for saving the cropped image
     */
    public static UCrop of(@NonNull Uri source, @NonNull Uri destination) {
        return new UCrop(source, destination);
    }
```
参数集合 赋值给 Ucrop 构造类
```java
public UCrop withOptions(@NonNull Options options) {
        mCropOptionsBundle.putAll(options.getOptionBundle());
        return this;
    }
```

```java
 /**
     * Get Intent to start {@link UCropActivity}
     *
     * @return Intent for {@link UCropActivity}
     */
    public Intent getIntent(@NonNull Context context) {
        mCropIntent.setClass(context, UCropActivity.class);
        mCropIntent.putExtras(mCropOptionsBundle);
        return mCropIntent;
    }

    /**
     * Send the crop Intent from an Activity with a custom request code
     *
     * @param activity    Activity to receive result
     * @param requestCode requestCode for result
     */
    public void start(@NonNull Activity activity, int requestCode) {
        activity.startActivityForResult(getIntent(activity), requestCode);
    }
```