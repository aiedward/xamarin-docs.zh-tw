---
title: 適用于 Xamarin 的 android 可呼叫包裝函式
ms.prod: xamarin
ms.assetid: C33E15FA-1E2B-819A-C656-CA588D611492
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: fb00bf4a817ad8188d5a127b23b4a910dd4f23a7
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69524139"
---
# <a name="android-callable-wrappers-for-xamarinandroid"></a>適用于 Xamarin 的 android 可呼叫包裝函式

每當 Android 執行時間叫用 managed 程式碼時, 都需要 android 可呼叫包裝函式 (ACWs)。 這些包裝函式是必要的, 因為沒有任何方法可以在執行時間以美工 (Android 執行時間) 註冊類別。 (具體而言, Android 執行時間不支援[JNI DefineClass () 函數](http://docs.oracle.com/javase/1.5.0/docs/guide/jni/spec/functions.html#wp15986)。} 因此, Android 可呼叫包裝函式會產生不足的執行時間類型註冊支援。 

*每次*Android 程式碼必須執行`virtual`或介面方法, 或是在 managed 程式碼中實做`overridden` , Xamarin。 Android 必須提供 JAVA proxy, 此方法才會分派至適當的 managed 類型。 這些 JAVA proxy 類型是 JAVA 程式碼, 其具有「相同」基類和 JAVA 介面清單做為 managed 類型, 它會執行相同的函式, 並宣告任何覆寫的基類和介面方法。 

Android 可呼叫包裝函式會在[建立](~/android/deploy-test/building-apps/build-process.md)程式期間由**monodroid**所產生: 它們會針對 (直接或間接) 繼承[JAVA. lang.ini 物件](xref:Java.Lang.Object)的所有類型產生。 



## <a name="android-callable-wrapper-naming"></a>Android 可呼叫包裝函式命名

Android 可呼叫包裝函式的封裝名稱是根據所匯出之類型的元件限定名稱的 CHECK MD5SUM。 這種命名技巧可以讓相同的完整型別名稱提供給不同的元件, 而不會產生封裝錯誤。 

由於此 CHECK MD5SUM 命名配置, 您無法依名稱直接存取您的類型。 例如, 下列`adb`命令將無法使用, 因為預設不會產生`my.ActivityType`類型名稱: 

```shell
adb shell am start -n My.Package.Name/my.ActivityType
```

此外, 如果您嘗試依名稱參考類型, 您可能會看到類似下面的錯誤:

```shell
java.lang.ClassNotFoundException: Didn't find class "com.company.app.MainActivity"
on path: DexPathList[[zip file "/data/app/com.company.App-1.apk"] ...
```

如果您需要依名稱存取類型, 您可以在屬性宣告中宣告該類型的名稱。 例如, 以下程式碼會宣告具有完整名稱`My.ActivityType`的活動:

```csharp
namespace My {
    [Activity]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

`ActivityAttribute.Name`屬性可以設定為明確宣告此活動的名稱: 

```csharp
namespace My {
    [Activity(Name="my.ActivityType")]
    public partial class ActivityType : Activity {
        /* ... */
    }
}
```

加入這個屬性設定之後, `my.ActivityType`就可以從外部程式碼`adb`和腳本中的名稱存取。 `Service` `Application` `Activity` `ContentProvider` `BroadcastReceiver`屬性可以針對許多不同的類型來設定, 包括、、、和: `Name` 

- [ActivityAttribute.Name](xref:Android.App.ActivityAttribute.Name)
- [ApplicationAttribute.Name](xref:Android.App.ApplicationAttribute.Name)
- [ServiceAttribute.Name](xref:Android.App.ServiceAttribute.Name)
- [BroadcastReceiverAttribute.Name](xref:Android.Content.BroadcastReceiverAttribute.Name)
- [ContentProviderAttribute.Name](xref:Android.Content.ContentProviderAttribute.Name)

CHECK MD5SUM 為基礎的 ACW 命名是在 Xamarin. Android 5.0 中引進。 如需屬性命名的詳細資訊, 請參閱[RegisterAttribute](xref:Android.Runtime.RegisterAttribute)。 



## <a name="implementing-interfaces"></a>實作介面

有時候, 您可能需要實作為 android 介面, 例如[IComponentCallbacks](xref:Android.Content.IComponentCallbacks)。 由於所有 Android 類別和介面都會擴充[IJAVAObject](xref:Android.Runtime.IJavaObject)介面, 因此會發生問題: 我們要如何實行`IJavaObject`？ 

上述問題已獲得解答: 所有 Android 型別都需要`IJavaObject`實作為的原因, 是為了讓 Xamarin 擁有可提供給 android 的 android 可呼叫包裝函式, 亦即指定類型的 JAVA proxy。 由於**monodroid**只會尋找`Java.Lang.Object`子`IJavaObject,`類別, 而`Java.Lang.Object`實作為答案很明顯: 子類別`Java.Lang.Object`: 

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

*此頁面的其餘部分提供可能變更的執行詳細資料, 恕不另行通知*(這只會在這裡提供, 因為開發人員會想知道發生什麼情況)。 

例如, 假設有下列C#來源:

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

**Mandroid**程式將會產生下列 Android 可呼叫包裝函式: 

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

請注意, 系統會保留基類, 並`native`針對在 managed 程式碼中覆寫的每個方法提供方法宣告。 
