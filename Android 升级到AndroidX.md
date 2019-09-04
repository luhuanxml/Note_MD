## Android 升级到AndroidX

#### 一、库的转变

```java
 //implementation 'com.android.support:support-v4:28.0.0'

 //implementation 'com.android.support:appcompat-v7:28.0.0'
   
 implementation 'androidx.appcompat:appcompat:1.0.2'

 //implementation 'com.android.support:cardview-v7:28.0.0'
 
 implementation 'androidx.cardview:cardview:1.0.0'

 //implementation 'com.android.support:recyclerview-v7:28.0.0'
   
 implementation 'androidx.recyclerview:recyclerview:1.0.0'

 //implementation 'com.android.support:design:28.0.0'
   
 implementation 'com.google.android.material:material:1.0.0'
   
 //implementation 'com.android.support:multidex:1.0.3'
   
 implementation 'androidx.multidex:multidex:2.0.1'
   
 //implementation 'com.android.support.constraint:constraint-layout:1.1.3'
 
 implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
```

#### 二、gradle.properties改变

```java
# https://developer.android.com/topic/libraries/support-library/androidx-rn
android.useAndroidX=true
# Automatically convert third-party libraries to use AndroidX
android.enableJetifier=true
```

