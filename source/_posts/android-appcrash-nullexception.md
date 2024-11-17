---
title: android-appcrash-nullexception
date: 2024-11-17 22:10:35
tags: [计算机, 学习]
categories:
- [计算机, Android]
---
# NullableException导致的app crash
记录遇到的一个Bug:

在写Android App时出现了一个app crash，
异常信息是`java.lang.NullPointerException`

因为新项目用的Kotlin，而同时需要用到老的Java代码

原本Java代码有一个Interface：
```java
public interface MetaDataRefreshCallback {
    void onComplete();

    void onError(Exception error);
}
```

在新的Kotlin代码里实现了这个接口：
```kotlin
val callback = object : MetadataRefreshCallback {
    override fun onComplete() {
        continuation.resume(Unit)
    }

    override fun onError(error: Exception) {
        continuation.resumeWithException(error)
    }
}
```

问题就出在Java的Exception类型可能是null，而Kotlin如果没有声明类型为Nullable，默认不会是null的。如果传了null进去，就会抛异常

而这里的异常不会被外层的`try-catch`捕获，从而导致app crash

Fix的代码如下：
```kotlin
val callback = object : MetadataRefreshCallback {
    override fun onComplete() {
        continuation.resume(Unit)
    }

    override fun onError(error: Exception?) {
        continuation.resumeWithException(error ?: Exception("Error in refreshing items"))
    }
}
```

# 总结
在把Java代码转化成Kotlin时，要注意对象的null值，Java里没有Nullable类型，如果Kotlin不设置成Nullable，会认为该对象不可能为null，如果运行时出现了null，则会抛`java.lang.NullableException`

