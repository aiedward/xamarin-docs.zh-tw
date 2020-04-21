---
title: Xamarin.安卓的安卓可調用包裝器
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: ef2f8e0375786ba7b627fdf75545cbb48318c1aa
ms.sourcegitcommit: 854798de42566750d9c70b6d0539b7ee73ff6ddc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/20/2020
ms.locfileid: "81646594"
---
# <a name="android-callable-wrappers-for-xamarinandroid"></a>Xamarin.安卓的安卓可調用包裝器

每當 Android 執行時調用託管代碼時,都需要 Android 可呼叫包裝器 (ACWs)。 這些包裝器是必需的,因為無法在運行時向 ART(Android 運行時)註冊類。 (具體來說,Android 運行時不支援[JNI 定義類() 功能](https://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986)。 因此,Android 可調用包裝器彌補運行時類型註冊支援的不足。 

*每次*Android 代碼需要執行`virtual`在託管代碼中`overridden`實現或實現的或介面方法,Xamarin.Android 必須提供 JAVA 代理,以便將此方法發送到相應的託管類型。 這些 Java 代理類型是 JAVA 代碼,具有與託管類型相同的基類和 Java 介面清單,實現相同的構造函數並聲明任何重寫的基類和介面方法。 

Android 可呼叫包裝器由**單體.exe**程式[在生成過程中](~/android/deploy-test/building-apps/build-process.md)生成:它們為(直接或間接)繼承[JAva.Lang.Object.](xref:Java.Lang.Object). 

## <a name="android-callable-wrapper-naming"></a>安卓可呼叫包裝器命名

Android 可調用包裝器的包名稱基於要匯出類型的程式集限定名稱的 MD5SUM。 這種命名技術使不同的程式集能夠使用相同的完全限定類型名稱,而不會引入打包錯誤。 

由於此 MD5SUM 命名方案,因此您不能按名稱直接存取類型。 例如,以下`adb`指令將不起作用,因為預設情況下不會產生類型`my.ActivityType`名稱 : 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

此外,如果您嘗試按名稱引用類型,則可能會看到如下所示的錯誤:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

如果*確實需要*按名稱訪問類型,則可以在屬性聲明中聲明該類型的名稱。 例如,下面是宣告具有完全限定名稱`My.ActivityType`的活動的代碼:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

可以將`ActivityAttribute.Name`該屬性設定為顯式宣告此活動的名稱: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

新增此屬性設定後,`my.ActivityType`可以透過外部`adb`代碼和文稿的名稱進行訪問。 可以為`Name`許多不同類型的屬性`Activity`設定,包括、、、`Application``Service``BroadcastReceiver`與`ContentProvider`: 

- [ActivityAttribute.Name](xref:Android.App.ActivityAttribute.Name)
- [ApplicationAttribute.Name](xref:Android.App.ApplicationAttribute.Name)
- [ServiceAttribute.Name](xref:Android.App.ServiceAttribute.Name)
- [BroadcastReceiverAttribute.Name](xref:Android.Content.BroadcastReceiverAttribute.Name)
- [ContentProviderAttribute.Name](xref:Android.Content.ContentProviderAttribute.Name)

基於MD5SUM的ACW命名在Xamarin.Android 5.0中引入。 有關屬性命名的詳細資訊,請參閱[註冊屬性](xref:Android.Runtime.RegisterAttribute)。 

## <a name="implementing-interfaces"></a>實作介面

有時您可能需要實現 Android 介面,如[Android.Content.I 元件回撥](xref:Android.Content.IComponentCallbacks)。 由於所有Android類和介面都擴展了[Android.Runtime.IJavaObject](xref:Android.Runtime.IJavaObject)介面,問題就出現了:我們如何實現`IJavaObject`? 

上面回答了這個問題:所有 Android 類型`IJavaObject`都需要實現 的原因是使 Xamarin.Android 具有一個 Android 可調用包裝器提供給 Android,即給定類型的 Java 代理。 由於**單體.exe**只`Java.Lang.Object`查`Java.Lang.Object`找子`IJavaObject`類 和實現 ,因此答案顯而易`Java.Lang.Object`見:子類: 

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

*本頁的其餘部分提供實現詳細資訊,恕不另行通知即可更改*(此處僅介紹,因為開發人員會對正在發生的事情感到好奇)。 

例如,給定以下 C# 源:

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

**mandroid.exe**程式將生成以下 Android 可呼叫包裝器: 

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

請注意,基類已保留,並且為`native`託管代碼中重寫的每個方法提供方法聲明。 
