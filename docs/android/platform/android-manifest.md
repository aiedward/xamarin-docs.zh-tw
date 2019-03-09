---
title: 使用 Android 資訊清單
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 5e354f8271257ab21a855bdf5d576ce3062fadc7
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668240"
---
# <a name="working-with-the-android-manifest"></a>使用 Android 資訊清單


## <a name="overview"></a>總覽

**AndroidManifest.xml**是功能強大的檔案，在 Android 平台，可讓您說明的功能與 Android 應用程式的需求。 不過，使用它並不容易。 Xamarin.Android 有助於降低這種困難狀況，可讓您將自訂屬性新增至您的類別，然後將用來自動產生您的資訊清單。 我們的目標是 99%的使用者應該永遠不需要以手動方式修改**AndroidManifest.xml**。 

**AndroidManifest.xml**會產生為建置流程，而且 XML 中找到的一部分**properties/Androidmanifest.xml**合併從自訂屬性產生的 xml。 產生的合併**AndroidManifest.xml**位於**obj**子目錄; 例如，它位於在**obj/Debug/android/AndroidManifest.xml**偵錯組建. 合併的程序很簡單： 它使用的程式碼內的自訂屬性來產生 XML 項目，並*插入*這些項目**AndroidManifest.xml**。 



## <a name="the-basics"></a>基本概念

在編譯時期，組件中掃描非`abstract`類別衍生自[活動](https://developer.xamarin.com/api/type/Android.App.Activity/)還有[ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/)在其上宣告的屬性。 它接著會使用這些類別和屬性來建置資訊清單。 例如，請參考下列程式碼： 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

這會導致執行任何動作中產生**AndroidManifest.xml**。 如果您想`<activity/>`項目來產生，您必須使用 [`[Activity]`](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute) 
自訂屬性： 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

此範例會產生下列 xml 片段加入至**AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

`[Activity]`屬性並不會影響`abstract`類型;`abstract`類型會被忽略。



### <a name="activity-name"></a>活動名稱

從 Xamarin.Android 5.1 開始，活動的型別名稱根據組件限定名稱的匯出型別 MD5SUM。 這可讓您提供從兩個不同的組件，並取得封裝錯誤相同完整名稱。 （之前 Xamarin.Android 5.1 中，活動的預設型別名稱已從建立小寫的命名空間和類別名稱。） 

如果您想要覆寫此預設值，並明確指定您的活動，使用名稱[ `Name` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)屬性： 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

此範例會產生下列 xml 片段：

```xml
<activity android:name="awesome.demo.activity" />
```

*附註*： 您應該使用`Name`僅基於回溯相容性，因此重新命名的屬性可能會降低在執行階段的型別查閱。 如果您有舊版的程式碼預期小寫的命名空間是根據活動的預設型別名稱和類別名稱，請參閱[Android 可呼叫包裝函式命名](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming)維持相容性的秘訣。 


### <a name="activity-title-bar"></a>活動標題列

根據預設，Android 可讓您的應用程式標題列會在執行時。 使用這個值是[ `/manifest/application/activity/@android:label` ](https://developer.android.com/guide/topics/manifest/activity-element.html#label)。 在大部分情況下，這個值會與您的類別名稱不同。 若要指定您的應用程式標籤標題列上，使用[ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/)屬性。
例如:  

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

此範例會產生下列 xml 片段：

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>從 應用程式選擇器

根據預設，活動才會顯示在 Android 的應用程式啟動程式螢幕。 這是因為在您的應用程式，可能會有許多活動，而您不要針對每個圖示。 若要指定哪一個應該是可從應用程式啟動程式啟動，請使用[ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/)屬性。 例如:  

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

此範例會產生下列 xml 片段：

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

根據預設，您的活動將會提供系統所提供的預設啟動程式圖示。 若要使用自訂圖示，請先將您 **.png**要**資源/drawable**，將其建置動作設定為**AndroidResource**，然後使用[ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/)屬性來指定要使用的圖示。 例如:  

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

此範例會產生下列 xml 片段：

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```


### <a name="permissions"></a>權限

當您將權限加入 Android 資訊清單 (如中所述[新增至 Android 資訊清單的權限](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest))，這些權限會記錄在**properties/Androidmanifest.xml**。 例如，如果您設定`INTERNET`權限，下列項目加入至**properties/Androidmanifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

偵錯組建會自動設定一些權限以方便偵錯 (例如`INTERNET`並`READ_EXTERNAL_STORAGE`)&ndash;這些設定只在產生**obj/Debug/android/AndroidManifest.xml** ，而不顯示為已在中啟用**必要的權限**設定。 

例如，如果您檢查產生的資訊清單檔案，在**obj/Debug/android/AndroidManifest.xml**，您可能會看到下列新增權限項目： 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

在發行組建版本的資訊清單 (在**obj/Debug/android/AndroidManifest.xml**)，這些權限*不*自動設定。 如果您發現切換至發行組建會導致應用程式失去偵錯組建中可用的權限，請確認您已明確設定設為此權限**必要的權限**設定您的應用程式 （請參閱**建置 > Android 應用程式**在 Visual Studio for Mac，請參閱**屬性 > Android 資訊清單**Visual Studio 中)。 




## <a name="advanced-features"></a>進階的功能


### <a name="intent-actions-and-features"></a>意圖動作與功能

Android 資訊清單可讓您描述您的活動的功能。 這透過完成[意圖](https://developer.android.com/guide/topics/manifest/intent-filter-element.html)和 [`[IntentFilter]`](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) 
自訂屬性。 您可以指定哪些動作適用於您使用的活動 [`IntentFilter`](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/) 
建構函式，並與適合哪一個類別目錄 [`Categories`](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/) 
屬性。 至少一個活動必須提供 （這就是為什麼建構函式中所提供的活動）。 `[IntentFilter]` 可提供很多次，並每次使用會導致個別`<intent-filter/>`內的項目`<activity/>`。 例如: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

此範例會產生下列 xml 片段：

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


### <a name="application-element"></a>應用程式項目

Android 資訊清單也可讓您宣告的整個應用程式的屬性。 這透過完成`<application>`項目和其對應的[應用程式](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/)自訂屬性。 請注意，這些是整個應用程式 （組件範圍） 設定，而不是每個活動設定。 通常，您會宣告`<application>`整個應用程式的屬性，然後覆寫這些設定 （如有需要） 以每個活動為基礎。 

例如，下列`Application`屬性新增至**AssemblyInfo.cs**指出可偵錯應用程式，其使用者可讀的名稱是**我的應用程式**，而且它會使用`Theme.Light`為所有活動的預設佈景主題樣式： 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

這個宣告會導致下列 XML 片段中產生**obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
在此範例中，應用程式中的所有活動會都預設為`Theme.Light`樣式。 如果您設定活動的佈景主題`Theme.Dialog`，只有該活動會使用`Theme.Dialog`您的應用程式中的所有其他活動會預設為同時`Theme.Light`中所設定的樣式`<application>`項目。 

`Application`項目不是唯一的方式來設定`<application>`屬性。 或者，您可以在其中插入屬性直接`<application>`項目**properties/Androidmanifest.xml**。 這些設定會合併成最終`<application>`項目，位於**obj/Debug/android/AndroidManifest.xml**。 請注意，內容**properties/Androidmanifest.xml**一律覆寫自訂屬性所提供的資料。 

有許多您可以在設定的整個應用程式的屬性`<application>`項目; 如需有關這些設定的詳細資訊，請參閱 <<c2> [ 的公用屬性](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties)一節[ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 



## <a name="list-of-custom-attributes"></a>自訂屬性清單

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) :會產生[/manifest/application/activity](https://developer.android.com/guide/topics/manifest/activity-element.html) XML 片段 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) :會產生[應用程式資訊清單/](https://developer.android.com/guide/topics/manifest/application-element.html) XML 片段 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) :會產生[資訊清單/檢測](https://developer.android.com/guide/topics/manifest/instrumentation-element.html)XML 片段 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) :會產生[//intent-filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) XML 片段 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) :會產生[//meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) XML 片段 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) :會產生[//permission](https://developer.android.com/guide/topics/manifest/permission-element.html) XML 片段 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) :會產生[//permission-group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) XML 片段 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) :會產生[//permission-tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) XML 片段 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) :會產生[/manifest/application/service](https://developer.android.com/guide/topics/manifest/service-element.html) XML 片段 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) :會產生[/manifest/application/uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) XML 片段 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) :會產生[/manifest/uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) XML 片段 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) :會產生[/manifest/application/receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) XML 片段 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) :會產生[/manifest/application/provider](https://developer.android.com/guide/topics/manifest/provider-element.html) XML 片段 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) :會產生[/manifest/application/provider/grant-uri-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) XML 片段

