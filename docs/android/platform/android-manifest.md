---
title: 使用 Android 清單
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: 1438c012608b367c21ebcc401c058b186b917f53
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027808"
---
# <a name="working-with-the-android-manifest"></a>使用 Android 清單

**AndroidManifest.xml**是 Android 平臺中一個強大的文件,允許您向 Android 描述應用程式的功能和要求。 然而,使用它並不容易。 Xamarin.Android 允許您向類添加自定義屬性,從而有助於將此困難降至最低,然後這些屬性將用於自動為您生成清單。 我們的目標是,99%的用戶永遠不需要手動修改**AndroidManifest.xml。** 

**AndroidManifest.xml**是生成過程的一部分,**屬性/AndroidManifest.xml**中的 XML 與從自定義屬性生成的 XML 合併。 生成的合併**的 AndroidManifest.xml**駐留在**obj**子目錄中;例如,它駐留在調試生成的**obj/調試/android/AndroidManifest.xml。** 合併過程微不足道:它使用代碼中的自定義屬性來生成XML元素,並將這些元素*插入*到**AndroidManifest.xml**中。 

## <a name="the-basics"></a>基本概念

在編譯時,會掃描從[活動](xref:Android.App.Activity)派生`abstract`並[`[Activity]`](xref:Android.App.ActivityAttribute)聲明 屬性的非類的程式集。 然後,它使用這些類和屬性來生成清單。 例如，請參考下列程式碼： 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

這導致在**AndroidManifest.xml**中產生什麼。 如果要產生`<activity/>`元素,則需要使用[`[Activity]`](xref:Android.App.Activity) 
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

此範例導致以下 xml 片段到**AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

該`[Activity]`屬性對類型沒有`abstract`影響 ;因此,該屬性對類型沒有影響。`abstract`類型將被忽略。

### <a name="activity-name"></a>活動名稱

從 Xamarin.Android 5.1 開始,活動的類型名稱基於要匯出的類型程式集限定名稱的 MD5SUM。 這允許從兩個不同的程式集提供相同的完全限定的名稱,並且不會收到打包錯誤。 (在 Xamarin.Android 5.1 之前,活動的預設類型名稱是從小寫名和類名稱創建的。 

如果要重寫此預設值並顯式指定活動的名稱,請使用[`Name`](xref:Android.App.ActivityAttribute.Name)以下 屬性: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

此範例產生以下 xml 片段:

```xml
<activity android:name="awesome.demo.activity" />
```

> [!NOTE]
> 應僅出於向後`Name`相容性原因使用 該屬性,因為重命名可能會降低運行時的類型查找速度。 如果您有舊代碼,希望活動的預設類型名稱基於小寫名空間和類名稱,請參閱[Android 可調用包裝器命名,](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming)瞭解有關維護相容性的提示。 

### <a name="activity-title-bar"></a>作用標題列

默認情況下,Android 在應用程式運行時會為應用程式提供標題列。 此數值為[`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label)。 在大多數情況下,此值將不同於您的類名稱。 要在標題列上指定應用的標籤,請使用該[`Label`](xref:Android.App.ActivityAttribute.Label)屬性。
例如： 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

此範例產生以下 xml 片段:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

### <a name="launchable-from-application-chooser"></a>選擇應用程式

默認情況下,您的活動不會顯示在 Android 的應用程式啟動器螢幕中。 這是因為應用程式中可能有許多活動,並且您不希望每個活動都使用圖示。 要指定從應用程式啟動器啟動哪一個,請使用屬性[`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher)。 例如： 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

此範例產生以下 xml 片段:

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

默認情況下,您的活動將被授予系統提供的預設啟動器圖示。 要使用自訂圖示,請先將 **.png**添加到**資源/可繪製**,將其生成操作設置為**AndroidResource,** 然後使用[`Icon`](xref:Android.App.ActivityAttribute.Icon)該屬性指定要使用的圖示。 例如： 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

此範例產生以下 xml 片段:

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

當您向 Android 清單添加許可權時(如[向 Android 清單添加許可權](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)中所述),這些許可權將記錄在**屬性/AndroidManifest.xml**中。 例如,如果設定權限`INTERNET`, 則以下元素將新增到**屬性/AndroidManifest.xml**中: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

除錯產生會自動設定一些許可權,以使除錯更容易(如`INTERNET``READ_EXTERNAL_STORAGE`和&ndash;), 這些設定僅在生成的**obj/調試/Android/AndroidManifest.xml**中設定,並且未在 **"必需的權限**設定"中顯示為啟用。 

例如,如果您在**obj/除錯/android/AndroidManifest.xml**上檢查產生的清單檔,您可能會看到以下新增的權限元素: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

在清單的發佈版本版本中(在**obj/調試/android/AndroidManifest.xml),** 這些許可權*不會*自動配置。 如果您發現切換到發佈版本會導致應用丟失調試版本中可用的許可權,請驗證您是否在應用的 **"必需許可權**設置"中顯式設置了此許可權(請參閱 Mac 可視化工作室中的 **"構建> Android 應用程式**;請參閱可視化工作室中**的屬性> Android 清單**)。 

## <a name="advanced-features"></a>進階功能

### <a name="intent-actions-and-features"></a>意圖操作與功能

Android 清單為您提供了一種描述活動功能的方法。 這是通過[意圖](https://developer.android.com/guide/topics/manifest/intent-filter-element.html)和[`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
自定義屬性。 您可以指定哪些操作適合您的活動與[`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
建構函數,以及哪些類別與[`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
屬性中找到的值而定。 必須至少提供一個活動(這就是為什麼在構造函數中提供活動的原因)。 `[IntentFilter]`可以多次提供,並且每次使用都會在`<intent-filter/>``<activity/>`中產生一個單獨的元素。 例如：

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

此範例產生以下 xml 片段:

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

Android 清單還提供了一種聲明整個應用程式屬性的方法。 這是通過`<application>`元素及其對應項[應用程式](xref:Android.App.ApplicationAttribute)自定義屬性完成的。 請注意,這些是應用程式範圍(程式集範圍)設置,而不是每個活動設置。 通常,您聲明`<application>`整個應用程式的屬性,然後(根據需要)根據每個活動覆蓋這些設置。 

例如,將`Application`以下屬性添加到**AssemblyInfo.cs**以指示可以調試應用程式,其使用者可讀名稱為 **"我的應用程式**",並且它將`Theme.Light`該樣式用作所有活動的預設主題: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

此聲明導致在**obj/除錯/android/AndroidManifest.xml**中產生以下 XML 片段:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```

在此範例中,應用中的所有活動都將預設為`Theme.Light`樣式。 如果將`Theme.Dialog`活動的主題設置為 ,則只有該活動`Theme.Dialog`將使用 該樣式,而應用中的所有其他活動將預設為元素`Theme.Light``<application>`中設置的 樣式。 

該`Application`元素不是`<application>`配置 屬性的唯一方法。 或者,可以直接將屬性插入屬性`<application>`**/AndroidManifest.xml**的元素。 這些設置被合併到位於`<application>`**obj/調試/android/AndroidManifest.xml**中的最後元素中。 請注意,**屬性/AndroidManifest.xml**的內容始終覆蓋自定義屬性提供的數據。 

有許多應用程式範圍的屬性可以在`<application>`元素中配置;有關這些設置的詳細資訊,請參閱[應用程式屬性](xref:Android.App.ApplicationAttribute)[的公共屬性](xref:Android.App.ApplicationAttribute)部分。 

## <a name="list-of-custom-attributes"></a>自訂屬性清單

- [Android.App.活動屬性](xref:Android.App.ActivityAttribute): 產生[/清單/應用程式/活動](https://developer.android.com/guide/topics/manifest/activity-element.html)XML 片段 
- [Android.App.應用程式屬性](xref:Android.App.ApplicationAttribute): 產生[/清單/應用程式](https://developer.android.com/guide/topics/manifest/application-element.html)XML 片段 
- [Android.App.儀器屬性](xref:Android.App.InstrumentationAttribute): 產生[/清單/儀器](https://developer.android.com/guide/topics/manifest/instrumentation-element.html)XML 片段 
- [Android.App.IntentFilter屬性](xref:Android.App.IntentFilterAttribute):生成[/意圖篩選](https://developer.android.com/guide/topics/manifest/intent-filter-element.html)XML片段 
- [Android.App.MetaData屬性](xref:Android.App.MetaDataAttribute):生成[//元數據](https://developer.android.com/guide/topics/manifest/meta-data-element.html)XML 片段 
- [Android.App.權限屬性](xref:Android.App.PermissionAttribute): 產生[/權限](https://developer.android.com/guide/topics/manifest/permission-element.html)XML 片段 
- [Android.App.權限組屬性](xref:Android.App.PermissionGroupAttribute):生成[//權限組](https://developer.android.com/guide/topics/manifest/permission-group-element.html)XML 片段 
- [Android.App.權限樹屬性](xref:Android.App.PermissionTreeAttribute): 產生[//權限樹](https://developer.android.com/guide/topics/manifest/permission-tree-element.html)XML 片段 
- [Android.App.服務屬性](xref:Android.App.ServiceAttribute): 產生[/清單/應用程式/服務](https://developer.android.com/guide/topics/manifest/service-element.html)XML 片段 
- [Android.App.uses函式庫屬性](xref:Android.App.UsesLibraryAttribute):產生[/清單/應用程式/使用函式庫](https://developer.android.com/guide/topics/manifest/uses-library-element.html)XML 片段 
- [Android.App.uses 權限屬性](xref:Android.App.UsesPermissionAttribute):產生[/清單/使用權限](https://developer.android.com/guide/topics/manifest/uses-permission-element.html)XML 片段 
- [Android.Content.廣播接收器屬性](xref:Android.Content.BroadcastReceiverAttribute): 產生[/清單/應用程式/接收器](https://developer.android.com/guide/topics/manifest/receiver-element.html)XML 片段 
- [Android.Content.Content.ContentProvider屬性](xref:Android.Content.ContentProviderAttribute):產生[/清單/應用程式/提供程式](https://developer.android.com/guide/topics/manifest/provider-element.html)XML 片段 
- [Android.Content.GrantUri權限屬性](xref:Android.Content.GrantUriPermissionAttribute):生成[/清單/應用程式/提供程式/授予權限](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html)XML 片段
