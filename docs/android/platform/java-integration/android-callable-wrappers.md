---
title: Android 可呼叫包裝函式
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 7edbdaa5a690a641523cb5baad7909ed01992aa5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61090857"
---
# <a name="android-callable-wrappers"></a>Android 可呼叫包裝函式

每當 Android 執行階段會叫用 managed 程式碼需要 android 可呼叫包裝函式 (ACWs)。 這些包裝函式是必要的因為沒有任何方法，以向圖案 （Android 執行階段） 中的類別，在執行階段。 (具體而言， [JNI DefineClass() 函式](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986)Android 執行階段不支援。} Android 可呼叫包裝函式因此構成缺乏執行階段型別註冊支援。 

*每次*Android 程式碼必須執行`virtual`介面的方法或`overridden`或 managed 程式碼中實作時，Xamarin.Android 必須提供 Java proxy，使這個方法會分派至適當的 managed 型別。 這些 Java proxy 型別會實作相同的建構函式和宣告任何覆寫的基底類別和介面方法的 managed 類型會有 「 相同 」 的基底類別和 Java 介面清單中的 Java 程式碼。 

Android 可呼叫包裝函式會產生**monodroid.exe**程式期間[建置程序](~/android/deploy-test/building-apps/build-process.md): （直接或間接） 繼承的所有類型產生[Java.Lang.Object](https://developer.xamarin.com/api/type/Java.Lang.Object/)。 



## <a name="android-callable-wrapper-naming"></a>Android 可呼叫包裝函式命名

Android 可呼叫包裝函式的封裝名稱根據組件限定名稱的匯出型別 MD5SUM。 這個命名的技術，可讓相同的完整類型名稱，可供使用不同的組件且不會造成封裝時發生錯誤。 

這個 MD5SUM 命名配置，因為您無法直接透過名稱存取您的型別。 例如，下列`adb`命令將無法運作，因為型別名稱`my.ActivityType`預設不會產生： 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

此外，您可能會看到類似下列的錯誤，如果您嘗試依名稱參考的型別：

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

如果您*請勿*需要存取，您可以依名稱的類型，宣告屬性宣告中該類型的名稱。 例如，以下是宣告的完整限定名稱的活動的程式碼`My.ActivityType`:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

`ActivityAttribute.Name`屬性可以設為明確宣告此活動的名稱： 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

加入此屬性設定之後，`my.ActivityType`可以依名稱從外部程式碼和存取`adb`指令碼。 `Name`屬性可以設定的許多不同的型別，包括`Activity`， `Application`， `Service`， `BroadcastReceiver`，和`ContentProvider`: 

-   [ActivityAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)
-   [ApplicationAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ApplicationAttribute.Name/)
-   [ServiceAttribute.Name](https://developer.xamarin.com/api/property/Android.App.ServiceAttribute.Name/)
-   [BroadcastReceiverAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.BroadcastReceiverAttribute.Name/)
-   [ContentProviderAttribute.Name](https://developer.xamarin.com/api/property/Android.Content.ContentProviderAttribute.Name/)

MD5SUM 型 ACW 命名是在 Xamarin.Android 5.0 中引進。 如需有關屬性命名的詳細資訊，請參閱[RegisterAttribute](https://developer.xamarin.com/api/type/Android.Runtime.RegisterAttribute/)。 



## <a name="implementing-interfaces"></a>實作介面

有些的時候您可能需要實作 Android 的介面，例如[Android.Content.IComponentCallbacks](https://developer.xamarin.com/api/type/Android.Content.IComponentCallbacks/)。 因為所有 Android 類別和介面的擴充[Android.Runtime.IJavaObject](https://developer.xamarin.com/api/type/Android.Runtime.IJavaObject/)介面，會發生這個問題： 我們要如何實作`IJavaObject`？ 

上面已解答的問題： 原因需要實作所有的 Android 類型`IJavaObject`以便 Xamarin.Android 有 Android，也就是指定類型的 Java proxy 提供給 Android 可呼叫包裝函式。 由於**monodroid.exe**只會尋找`Java.Lang.Object`子類別，以及`Java.Lang.Object`實作`IJavaObject,`答案很明顯： 子類別`Java.Lang.Object`: 

```csharp
class MyComponentCallbacks : Java.Lang.Object, Android.Content.IComponentCallbacks {

    public void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
    {
        // implementation goes here...
    } 

    public void OnLowMemory ()
    {
        // implementation goes here...
    }
}
```


## <a name="implementation-details"></a>實作詳細資料

*此頁面的其餘部分提供實作詳細資料，有變更恕不另行通知*（和呈現以下只是因為開發人員將會了解發生什麼情況的時候）。 

例如，假設有下列C#來源：

```csharp
using System;
using Android.App;
using Android.OS;

namespace Mono.Samples.HelloWorld
{
    public class HelloAndroid : Activity
    {
        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);
            SetContentView (R.layout.main);
        }
    }
}
```

**Mandroid.exe**程式將會產生下列 Android 可呼叫包裝函式： 

```java
package mono.samples.helloWorld;

public class HelloAndroid
    extends android.app.Activity
{
    static final String __md_methods;
    static {
        __md_methods = "n_onCreate:(Landroid/os/Bundle;)V:GetOnCreate_Landroid_os_Bundle_Handler\n" + "";
        mono.android.Runtime.register (
            "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
            Culture=neutral, PublicKeyToken=null", HelloAndroid.class, __md_methods);
    }

    public HelloAndroid ()
    {
        super ();
        if (getClass () == HelloAndroid.class)
            mono.android.TypeManager.Activate (
                "Mono.Samples.HelloWorld.HelloAndroid, HelloWorld, Version=1.0.0.0, 
                Culture=neutral, PublicKeyToken=null", "", this, new java.lang.Object[] {  });
    }

    @Override
    public void onCreate (android.os.Bundle p0)
    {
        n_onCreate (p0);
    }

    private native void n_onCreate (android.os.Bundle p0);
}
```

請注意，會保留基底類別，以及`native`方法宣告會提供給 managed 程式碼中覆寫每個方法。 
