---
title: "使用 Android 資訊清單"
ms.topic: article
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 0f270111ad4e10f035a3cb695564d789f40097fd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-the-android-manifest"></a>使用 Android 資訊清單

<a name="Overview" />

## <a name="overview"></a>總覽

**AndroidManifest.xml**是功能強大的檔案在 Android 平台，可讓您說明的功能與 Android 應用程式的需求。 不過，使用它並不容易。 Xamarin.Android 有助於降低這種困難狀況可讓您新增至您的類別，然後將用來自動產生您的資訊清單的自訂屬性。 我們的目標是 99%的使用者應該永遠不需要手動修改**AndroidManifest.xml**。 

**AndroidManifest.xml**產生為建置流程中，與 XML 中找到的一部分**Properties/AndroidManifest.xml**合併以產生從自訂屬性的 XML。 產生的合併**AndroidManifest.xml**位於**obj**子目錄; 例如，它位於在**obj/Debug/android/AndroidManifest.xml**偵錯組建. 合併處理序是 trivial： 它來產生 XML 項目，使用程式碼內的自訂屬性和*插入*這些項目**AndroidManifest.xml**。 


<a name="The_Basics" />

## <a name="the-basics"></a>基本概念

在編譯時期，組件會受到掃描非`abstract`類別衍生自[活動](https://developer.xamarin.com/api/type/Android.App.Activity/)，而且有[ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/)屬性在其上宣告。 它接著會使用這些類別和屬性來建置資訊清單。 例如，請參考下列程式碼： 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

這會導致執行任何動作中正在產生**AndroidManifest.xml**。 如果您想`<activity/>`要產生的項目需要使用[ `[Activity]` ](https://developer.xamarin.com/api/type/Android.App.Activity/Attribute)自訂屬性： 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

這個範例會產生下列 xml 片段加入至**AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

`[Activity]`屬性沒有任何作用`abstract`類型;`abstract`類型會被忽略。


<a name="Activity_Name" />

### <a name="activity-name"></a>活動名稱

從開始 Xamarin.Android 5.1，活動的型別名稱根據正在匯出之型別的組件限定名稱的 MD5SUM。 這可讓相同完整名稱提供從兩個不同的組件，因此無法封裝時發生錯誤。 （之前 Xamarin.Android 5.1 中，活動的預設型別名稱被建立從小寫的命名空間和類別名稱）。 

如果您想要覆寫此預設值，並明確指定的名稱，您的活動使用[ `Name` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Name/)屬性： 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

這個範例會產生下列 xml 片段：

```xml
<activity android:name="awesome.demo.activity" />
```

*請注意*： 您應該使用`Name`僅基於回溯相容性，因此重新命名的屬性可能會降低在執行階段的型別查閱。 如果您有舊版的程式碼預期小寫的命名空間是根據活動的預設型別名稱和類別名稱，請參閱[Android 可呼叫包裝函式命名](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming)維護相容性的秘訣。 

<a name="Activity_Title_Bar" />

### <a name="activity-title-bar"></a>活動標題列

根據預設，Android 可讓您的應用程式標題列時執行。 使用此值是[ `/manifest/application/activity/@android:label` ](http://developer.android.com/guide/topics/manifest/activity-element.html#label)。 在大部分情況下，這個值會與您的類別名稱的不同。 若要指定您的應用程式標籤標題列上，使用[ `Label` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Label/)屬性。
例如:  

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

這個範例會產生下列 xml 片段：

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

<a name="Launchable_from_Application_Chooser" />

### <a name="launchable-from-application-chooser"></a>從 應用程式選擇器

根據預設，活動會不顯示在 Android 的應用程式啟動器畫面。 這是因為應用程式中可能會有許多活動，而您不要針對每個圖示。 若要指定哪一個應該是可從應用程式啟動程式啟動，請使用[ `MainLauncher` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.MainLauncher/)屬性。 例如:  

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

這個範例會產生下列 xml 片段：

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```


<a name="Activity_Icon" />

### <a name="activity-icon"></a>活動圖示

根據預設，活動將會提供系統提供的預設啟動程式圖示。 若要使用自訂圖示，請先加入您**.png**至**資源/drawable**，其 建置動作設定為**AndroidResource**，然後使用[ `Icon` ](https://developer.xamarin.com/api/property/Android.App.ActivityAttribute.Icon/)屬性來指定使用的圖示。 例如:  

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

這個範例會產生下列 xml 片段：

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```

<a name="Permissions" />

### <a name="permissions"></a>權限

當您將權限加入 Android 資訊清單 (如所述[新增 Android 資訊清單的權限](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest/))，這些權限會記錄在**Properties/AndroidManifest.xml**。 例如，如果您設定`INTERNET`權限，下列項目加入至**Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

偵錯組建自動設定某些權限，可更容易偵錯 (例如`INTERNET`和`READ_EXTERNAL_STORAGE`)&ndash;這些設定會設定只有在所產生**obj/Debug/android/AndroidManifest.xml**且不顯示為已啟用在**必要的權限**設定。 

例如，如果您檢查在產生資訊清單檔案**obj/Debug/android/AndroidManifest.xml**，您可能會看到下列新增的權限項目： 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

在發行組建資訊清單的版本 (在**obj/Debug/android/AndroidManifest.xml**)，這些權限*不*自動設定。 如果您發現切換至發行組建會導致您的應用程式遺失，無法在偵錯組建中的權限，確認 確認您已明確設定此權限**必要的權限**（請參閱您應用程式設定**建置 > Android 應用程式**在 Visual Studio for Mac; 請參閱**屬性 > Android 資訊清單**Visual Studio 中)。 



<a name="Advanced_Features" />

## <a name="advanced-features"></a>進階的功能

<a name="Intent_Actions_and_Features" />

### <a name="intent-actions-and-features"></a>意圖動作與功能

Android 資訊清單可讓您描述活動的功能。 這是透過[對應方式](http://developer.android.com/guide/topics/manifest/intent-filter-element.html)和[ `[IntentFilter]` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)自訂屬性。 您可以指定哪些動作適用於您的活動，具有[ `IntentFilter` ](https://developer.xamarin.com/api/constructor/Android.App.IntentFilterAttribute.IntentFilterAttribute/p/System.String[]/)建構函式，而哪些類別目錄是以適當[ `Categories` ](https://developer.xamarin.com/api/property/Android.App.IntentFilterAttribute.Categories/)屬性。 至少一個活動，必須提供 （這就是為什麼建構函式中所提供的活動）。 `[IntentFilter]` 可以是提供了多次，且每次使用導致個別`<intent-filter/>`內的項目`<activity/>`。 例如: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

這個範例會產生下列 xml 片段：

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

<a name="Application_Element" />

### <a name="application-element"></a>應用程式項目

Android 資訊清單也可讓您宣告為整個應用程式的屬性。 這是透過`<application>`項目和其對應的[應用程式](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/)自訂屬性。 請注意，這些都是整個應用程式 （組件範圍） 設定，而不是每個活動設定。 通常，您會宣告`<application>`整個應用程式的內容，然後覆寫這些設定 （如有需要） 以每個活動為基礎。 

例如，下列`Application`屬性加入至**AssemblyInfo.cs**來表示應用程式可以是偵錯，請確定使用者可讀的名稱是**My App**，並使用`Theme.Light`樣式的所有活動的預設佈景主題： 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

這個宣告會造成下列 XML 片段中產生**obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
在此範例中，應用程式中的所有活動將會都預設為`Theme.Light`樣式。 如果您設定活動的佈景主題`Theme.Dialog`，只有該活動將會使用`Theme.Dialog`樣式時您的應用程式中的所有其他活動將會預設為`Theme.Light`中所設定的樣式`<application>`項目。 

`Application`元素不是唯一的方式來設定`<application>`屬性。 或者，您可以將屬性直接插入`<application>`元素**Properties/AndroidManifest.xml**。 這些設定會合併成最終`<application>`項目，位於**obj/Debug/android/AndroidManifest.xml**。 請注意，內容**Properties/AndroidManifest.xml**一律會覆寫的自訂屬性所提供的資料。 

有許多您可以在設定的全應用程式屬性`<application>`項目; 如需有關這些設定的詳細資訊，請參閱[公用屬性](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/#Public_Properties)區段[ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/). 


<a name="List_of_Custom_Attributes" />

## <a name="list-of-custom-attributes"></a>自訂屬性的清單

-   [Android.App.ActivityAttribute](https://developer.xamarin.com/api/type/Android.App.ActivityAttribute/) ： 產生[/manifest/application/activity](http://developer.android.com/guide/topics/manifest/activity-element.html) XML 片段 
-   [Android.App.ApplicationAttribute](https://developer.xamarin.com/api/type/Android.App.ApplicationAttribute/) ： 產生[/資訊清單/應用程式](http://developer.android.com/guide/topics/manifest/application-element.html)XML 片段 
-   [Android.App.InstrumentationAttribute](https://developer.xamarin.com/api/type/Android.App.InstrumentationAttribute/) ： 產生[/資訊清單/檢測](http://developer.android.com/guide/topics/manifest/instrumentation-element.html)XML 片段 
-   [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) ： 產生[//intent-filter](http://developer.android.com/guide/topics/manifest/intent-filter-element.html) XML 片段 
-   [Android.App.MetaDataAttribute](https://developer.xamarin.com/api/type/Android.App.MetaDataAttribute/) ： 產生[//meta-data](http://developer.android.com/guide/topics/manifest/meta-data-element.html) XML 片段 
-   [Android.App.PermissionAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionAttribute/) ： 產生[//permission](http://developer.android.com/guide/topics/manifest/permission-element.html) XML 片段 
-   [Android.App.PermissionGroupAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionGroupAttribute/) ： 產生[//permission-group](http://developer.android.com/guide/topics/manifest/permission-group-element.html) XML 片段 
-   [Android.App.PermissionTreeAttribute](https://developer.xamarin.com/api/type/Android.App.PermissionTreeAttribute/) ： 產生[//permission-tree](http://developer.android.com/guide/topics/manifest/permission-tree-element.html) XML 片段 
-   [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/) ： 產生[/manifest/application/service](http://developer.android.com/guide/topics/manifest/service-element.html) XML 片段 
-   [Android.App.UsesLibraryAttribute](https://developer.xamarin.com/api/type/Android.App.UsesLibraryAttribute/) ： 產生[/manifest/application/uses-library](http://developer.android.com/guide/topics/manifest/uses-library-element.html) XML 片段 
-   [Android.App.UsesPermissionAttribute](https://developer.xamarin.com/api/type/Android.App.UsesPermissionAttribute/) ： 產生[/manifest/uses-permission](http://developer.android.com/guide/topics/manifest/uses-permission-element.html) XML 片段 
-   [Android.Content.BroadcastReceiverAttribute](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiverAttribute/) ： 產生[/manifest/application/receiver](http://developer.android.com/guide/topics/manifest/receiver-element.html) XML 片段 
-   [Android.Content.ContentProviderAttribute](https://developer.xamarin.com/api/type/Android.Content.ContentProviderAttribute/) ： 產生[/manifest/application/provider](http://developer.android.com/guide/topics/manifest/provider-element.html) XML 片段 
-   [Android.Content.GrantUriPermissionAttribute](https://developer.xamarin.com/api/type/Android.Content.GrantUriPermissionAttribute/) ： 產生[/manifest/application/provider/grant-uri-permission](http://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) XML 片段

