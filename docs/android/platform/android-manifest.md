---
title: 使用 Android 資訊清單
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 1aaacae8cebed2396661a28c189af44c25238e7b
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887834"
---
# <a name="working-with-the-android-manifest"></a>使用 Android 資訊清單

**Androidmanifest.xml**是 android 平臺中功能強大的檔案, 可讓您向 android 描述應用程式的功能和需求。 不過, 使用它並不容易。 Xamarin 可讓您將自訂屬性新增至您的類別, 藉此將此困難降到最低, 然後用來自動為您產生資訊清單。 我們的目標是, 99% 的使用者永遠都不需要手動修改**androidmanifest.xml**。 

**Androidmanifest.xml**是在組建程式中產生的, 而且在**Properties/androidmanifest.xml**中找到的 xml 會與自訂屬性產生的 xml 合併。 產生的合併**androidmanifest.xml**會位於**obj**子目錄中;例如, 它位於**obj/debug/android/androidmanifest.xml** , 用於 Debug 組建。 合併程式很簡單: 它會使用程式碼內的自訂屬性來產生 XML 專案, 並將這些元素*插入* **androidmanifest.xml**中。 



## <a name="the-basics"></a>基本概念

在編譯時期, 系統會掃描元件中是否`abstract`有衍生自[活動](xref:Android.App.Activity)的非類別, [`[Activity]`](xref:Android.App.ActivityAttribute)並在其中宣告屬性。 然後, 它會使用這些類別和屬性來建立資訊清單。 例如，請參考下列程式碼： 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

這會導致**androidmanifest.xml**不會產生任何內容。 如果您想`<activity/>`要產生元素, 則需要使用[`[Activity]`](xref:Android.App.Activity) 
自訂屬性: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

這個範例會將下列 xml 片段加入至**androidmanifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

屬性對`abstract`類型沒有任何影響; `[Activity]``abstract`已忽略類型。



### <a name="activity-name"></a>活動名稱

從 Xamarin 5.1 開始, 活動的型別名稱是以匯出之型別的元件限定名稱的 CHECK MD5SUM 為基礎。 這可讓您從兩個不同的元件中提供相同的完整名稱, 而不會收到封裝錯誤。 (在 Xamarin. Android 5.1 之前, 活動的預設類型名稱是從小寫命名空間和類別名稱所建立)。 

如果您想要覆寫此預設值, 並明確指定活動的名稱, 請[`Name`](xref:Android.App.ActivityAttribute.Name)使用屬性: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

這個範例會產生下列 xml 片段:

```xml
<activity android:name="awesome.demo.activity" />
```

*注意*: 您應該只針對`Name`回溯相容性的原因使用屬性, 因為這類重新命名可能會在執行時間減緩類型查詢的速度。 如果您的舊版程式碼預期活動的預設型別名稱是根據小寫命名空間和類別名稱, 請參閱 Android 可呼叫[包裝](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming)函式命名, 以取得維護相容性的秘訣。 


### <a name="activity-title-bar"></a>活動標題列

根據預設, Android 會在應用程式執行時提供標題列。 用於此的值為[`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label)。 在大部分情況下, 此值會與您的類別名稱不同。 若要在標題列上指定應用程式的標籤, [`Label`](xref:Android.App.ActivityAttribute.Label)請使用屬性。
例如： 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

這個範例會產生下列 xml 片段:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>從應用程式選擇器可啟動

根據預設, 您的活動不會顯示在 Android 的應用程式啟動器畫面中。 這是因為您的應用程式中可能有多個活動, 而且您不想要每個活動都有一個圖示。 若要指定應該從應用程式啟動器可啟動哪一個, 請使用[`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher)屬性。 例如： 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

這個範例會產生下列 xml 片段:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```



### <a name="activity-icon"></a>活動圖示

根據預設, 系統會為您的活動提供預設的啟動器圖示。 若要使用自訂圖示, 請先將您的 **.png**新增至**資源/可繪製**, 將其組建動作設定為[`Icon`](xref:Android.App.ActivityAttribute.Icon) **AndroidResource**, 然後使用屬性來指定要使用的圖示。 例如： 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

這個範例會產生下列 xml 片段:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```


### <a name="permissions"></a>Permissions

當您將許可權新增至 Android 資訊清單時 (如[將許可權新增至 Android 資訊清單](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)中所述), 這些許可權會記錄在**Properties/androidmanifest.xml**中。 例如, 如果您設定`INTERNET`許可權, 下列元素會新增至**Properties/androidmanifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Debug build 會自動設定一些許可權, 讓您更輕鬆地`INTERNET`進行`READ_EXTERNAL_STORAGE`調試&ndash; (例如和)。這些設定只會在產生的**obj/debug/android/androidmanifest.xml**中設定, 而且不會顯示為 [已啟用]。**必要的許可權**設定。 

例如, 如果您在**obj/Debug/android/androidmanifest.xml**上檢查產生的資訊清單檔, 您可能會看到下列新增的許可權元素: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

在資訊清單的發行組建版本中 (在**obj/Debug/android/androidmanifest.xml**),*不*會自動設定這些許可權。 如果您發現切換至發行組建會導致您的應用程式遺失 Debug 組建中可用的許可權, 請確認您已在應用程式的**必要許可權**設定中明確設定此許可權 (請參閱**build > Android**Visual Studio for Mac 中的應用程式;請參閱 Visual Studio 中 **> Android 資訊清單的屬性**)。 




## <a name="advanced-features"></a>Advanced 功能


### <a name="intent-actions-and-features"></a>意圖動作和功能

Android 資訊清單提供一種方式來描述活動的功能。 這是透過[意圖](https://developer.android.com/guide/topics/manifest/intent-filter-element.html)和[`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
自訂屬性。 您可以使用, 指定哪些動作適合您的活動[`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
和適當的類別,[`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
屬性。 至少必須提供一個活動 (這就是在此函式中提供活動的原因)。 `[IntentFilter]`可以多次提供, 而每個使用都會在中產生個別`<intent-filter/>`的元素。 `<activity/>` 例如：

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

這個範例會產生下列 xml 片段:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.SAMPLE_CODE" />
    <category android:name="my.custom.category" />
  </intent-filter>
</activity>
```


### <a name="application-element"></a>Application 元素

Android 資訊清單也提供一種方式, 讓您宣告整個應用程式的屬性。 這項作業是透過`<application>`專案及其對應的[應用程式](xref:Android.App.ApplicationAttribute)自訂屬性來完成。 請注意, 這些是全應用程式 (全元件) 設定, 而不是每個活動設定。 一般來說, 您會`<application>`宣告整個應用程式的屬性, 然後根據每個活動來覆寫這些設定 (視需要)。 

例如, 下列`Application`屬性會新增至**AssemblyInfo.cs** , 以指出應用程式可以被調試、其使用者可讀名稱是**My App** `Theme.Light` , 而且它會使用樣式做為所有的預設主題工作 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

此宣告會導致在**obj/Debug/android/androidmanifest.xml**中產生下列 xml 片段:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```

在此範例中, 應用程式中的所有活動都會預設`Theme.Light`為樣式。 如果您將活動的主題設定為`Theme.Dialog`, 則只有該活動會`Theme.Dialog`使用樣式, 而應用程式中的所有其他活動都會預設`Theme.Light`為專案中`<application>`設定的樣式。 

元素不是設定`<application>`屬性的唯一方法。 `Application` 或者, 您也可以將屬性直接插入`<application>` **Properties/androidmanifest.xml**的元素中。 這些設定會合並到位於`<application>` **obj/Debug/android/androidmanifest.xml**中的最後一個元素。 請注意, **Properties/androidmanifest.xml**的內容一律會覆寫自訂屬性所提供的資料。 

您可以在`<application>`元素中設定許多應用程式範圍的屬性; 如需這些設定的詳細資訊, 請參閱[ApplicationAttribute](xref:Android.App.ApplicationAttribute)的[公用屬性](xref:Android.App.ApplicationAttribute)一節。 



## <a name="list-of-custom-attributes"></a>自訂屬性清單

- [ActivityAttribute](xref:Android.App.ActivityAttribute) :產生[/Manifest/application/activity](https://developer.android.com/guide/topics/manifest/activity-element.html) XML 片段 
- [ApplicationAttribute](xref:Android.App.ApplicationAttribute) :產生[/Manifest/application](https://developer.android.com/guide/topics/manifest/application-element.html) XML 片段 
- [InstrumentationAttribute](xref:Android.App.InstrumentationAttribute) :產生[/Manifest/instrumentation](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) XML 片段 
- [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) :產生[//Intent-filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) XML 片段 
- [MetaDataAttribute](xref:Android.App.MetaDataAttribute) :產生[//Meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) XML 片段 
- [PermissionAttribute](xref:Android.App.PermissionAttribute) :產生[//Permission](https://developer.android.com/guide/topics/manifest/permission-element.html) XML 片段 
- [PermissionGroupAttribute](xref:Android.App.PermissionGroupAttribute) :產生[//Permission-group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) XML 片段 
- [PermissionTreeAttribute](xref:Android.App.PermissionTreeAttribute) :產生[//Permission-tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) XML 片段 
- [ServiceAttribute](xref:Android.App.ServiceAttribute) :產生[/Manifest/application/service](https://developer.android.com/guide/topics/manifest/service-element.html) XML 片段 
- [UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute) :產生[/Manifest/application/uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) XML 片段 
- [UsesPermissionAttribute](xref:Android.App.UsesPermissionAttribute) :產生[/Manifest/uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) XML 片段 
- [BroadcastReceiverAttribute](xref:Android.Content.BroadcastReceiverAttribute) :產生[/Manifest/application/receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) XML 片段 
- [ContentProviderAttribute](xref:Android.Content.ContentProviderAttribute) :產生[/Manifest/application/provider](https://developer.android.com/guide/topics/manifest/provider-element.html) XML 片段 
- [GrantUriPermissionAttribute](xref:Android.Content.GrantUriPermissionAttribute) :產生[/Manifest/application/provider/grant-uri-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) XML 片段

