---
title: KitKat 功能
description: Android 4.4 (KitKat) 會載入與很多個使用者和開發人員的功能。 本指南會反白顯示這些功能，並提供程式碼範例和實作詳細資料，可協助您充分利用 KitKat。
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 7b7fa2ea99a58b875bc1dc579455511aa9bf72d1
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172089"
---
# <a name="kitkat-features"></a>KitKat 功能

_Android 4.4 (KitKat) 會載入與很多個使用者和開發人員的功能。本指南會反白顯示這些功能，並提供程式碼範例和實作詳細資料，可協助您充分利用 KitKat。_

## <a name="overview"></a>總覽

延遲的 2013年發行 android 4.4 (API Level 19)，也稱為"KitKat，"。 KitKat 提供各式各樣的新功能和增強功能，包括：

-  [使用者體驗](#user_experience)&ndash;轉換架構、 半透明的狀態和導覽列與全螢幕的沈浸式模式的簡單動畫幫助您建立的使用者更好的體驗。

-  [使用者的內容](#user_content)&ndash;儲存體存取架構透過簡化的管理使用者檔案; 列印圖片、 網站和其他內容，會比較容易改善列印 Api。

-  [硬體](#hardware)&ndash;變成使用 NFC 主機型卡模擬 NFC 卡片中的任何應用程式，執行低電源感應器與`SensorManager`。

-  [開發人員工具](#developer_tools)&ndash;螢幕錄製影片中使用 Android Debug Bridge 用戶端，可以使用動作的應用程式的 Android SDK 的一部分。


本指南提供指引 Xamarin.Android 開發人員移轉現有的 Xamarin.Android 應用程式 KitKat，以及 KitKat 的高階概觀。

## <a name="requirements"></a>需求

若要開發使用 KitKat 的 Xamarin.Android 應用程式，您需要*Xamarin.Android 4.11.0*或更高版本與 Android 4.4 (API Level 19) 安裝透過 Android SDK 管理員 中，如下列螢幕擷取畫面所示：

[![選取 Android SDK 管理員中的 Android 4.4](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>將應用程式移轉至 KitKat

本章節提供一些協助以 Android 4.4 轉換現有的應用程式的第一個回應項目。

### <a name="check-system-version"></a>檢查作業系統版本

如果必須相容於舊版的 Android 應用程式，請務必將任何 KitKat 專屬的程式碼包裝在系統版本檢查，，如下列程式碼範例所示：

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>警示批次處理

Android 會在指定的時間喚醒應用程式在背景中的使用警示服務。 KitKat 能更進一步批次處理保留電源的警示。 這表示，特定時間喚醒每個應用程式，您就不用 KitKat 偏好群組註冊在相同的時間間隔內，線上醒機，並將其喚醒一次的數個應用程式。
要告訴嵽指定的時間間隔內的應用程式的 Android，呼叫`SetWindow`上[ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/)，傳遞最小和最大時間 （毫秒） 之前，可以經過應用程式事項，以及要執行的作業在喚醒。
下列程式碼提供應用程式需要半小時之間的一小時，從視窗設定的時間技術的範例：

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

若要繼續在確切的時間喚醒應用程式，請使用`SetExact`，並傳入確切的時間應該事項應用程式，以及要執行的作業：

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat 不再可讓您設定的確切的重複警示。 使用應用程式 [`SetRepeating`](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/)
而且需要完全運作會現在必須手動觸發警示的警示。

### <a name="external-storage"></a>外部儲存體

外部儲存體現在分成兩種類型的唯一應用程式和資料的多個應用程式共用的儲存體。 讀取和寫入外部儲存體上的應用程式的特定位置需要任何特殊權限。 需要共用存放裝置上的資料互動的現在`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`權限。 因此可以分類的兩種類型：

-  如果您收到的檔案或目錄的路徑上呼叫方法`Context`-例如， [`GetExternalFilesDir`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/)
   或 [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - 您的應用程式需要任何額外的權限。

-  如果您收到的檔案或目錄的路徑存取的屬性或上呼叫方法`Environment`，例如 [`GetExternalStorageDirectory`](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/)
   或 [`GetExternalStoragePublicDirectory`](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/)
   您的應用程式需要`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`權限。

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` 表示`READ_EXTERNAL_STORAGE`權限，因此您應該只需要設定一個權限。

### <a name="sms-consolidation"></a>SMS 彙總

KitKat 簡化傳訊使用者藉由彙總使用者所選取的一個預設應用程式中的所有 SMS 內容。 開發人員負責讓應用程式的可選取為預設郵件應用程式，並且具有適當地在程式碼，並在生活中如果未選取的應用程式。 如需有關如何轉換您的 KitKat SMS 應用程式的詳細資訊，請參閱[Getting Your SMS 應用程式準備好進行 KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html)來自 Google 的指南。

### <a name="webview-apps"></a>WebView 應用程式

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) KitKat 中取得改造。 最大的變更會新增載入內容載入的安全性`WebView`。 雖然大部分以較舊的 API 版本為目標的應用程式能如預期般運作，測試使用的應用程式`WebView`強烈建議類別。 如需受影響的 WebView Api 的詳細資訊，請參閱 android[移轉到 WebView 中 Android 4.4](http://developer.android.com/guide/webapps/migrating.html)文件。

<a name="user_experience" />

## <a name="user-experience"></a>使用者經驗

KitKat 隨附數個新的 Api，以增強使用者體驗，包括新的轉換架構，用於處理屬性動畫和佈景主題的半透明 UI 選項。 下文涵蓋這些變更。

### <a name="transition-framework"></a>轉換架構

轉換架構可讓動畫更容易實作。 KitKat 可讓您執行簡單的屬性動畫，只要一行程式碼，或自訂使用的轉換*場景*。

#### <a name="simple-property-animation"></a>簡單的屬性動畫

新的 Android 轉換程式庫可簡化程式碼後置屬性動畫。 此架構可讓您執行簡單的動畫，以最少的程式碼。 例如，下列程式碼範例會使用 [`TransitionManager.BeginDelayedTransition`](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/)
若要建立動畫顯示和隱藏`TextView`:

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

上述範例會使用轉換架構來建立自動，預設值變更的屬性值之間的轉換。 因為動畫由一行程式碼，您可以輕鬆地進行這相容於舊版的 Android 藉由包裝`BeginDelayedTransition`呼叫中的系統版本檢查。 請參閱[移轉您的應用程式到 KitKat](#Migrating_Your_App_to_KitKat)區段，如需詳細資訊。

以下螢幕擷取畫面顯示動畫之前的應用程式：

[![動畫開始之前的應用程式螢幕擷取畫面](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

以下螢幕擷取畫面會顯示應用程式後動畫：

[![動畫完成後的應用程式螢幕擷取畫面](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

您可以取得更充分掌控與下一節所述的場景轉換。

#### <a name="android-scenes"></a>Android 的場景

[場景](https://developer.xamarin.com/api/type/Android.Transitions.Scene/)導入做為轉換架構，以便讓開發人員更充分掌控動畫的一部分。 場景在 UI 中建立動態的區域： 您指定的容器和數個版本中或 「 場景 」，在容器內的 XML 內容，而 Android 會以動畫顯示場景之間的轉換的工作。 Android 的場景，可讓您在開發端建置複雜的動畫，最少的工作。

裝載的動態內容的靜態 UI 元素稱為*容器*或是*場景基底*。 下列範例會使用 Android Designer 來建立`RelativeLayout`稱為`container`:

[![若要建立 RelativeLayout 容器使用 Android 設計工具](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

範例版面配置也會定義按鈕，叫做`sceneButton`以下`container`。 此按鈕將會觸發轉換。

在容器內的動態內容需要兩個新的 Android 配置。 這些配置指定的程式碼*內*容器。
下列範例程式碼定義的配置，稱為*Scene1*分別包含兩個文字欄位讀取 「 套件 」 和 「 Kat"，而第二個配置稱為*Scene2*包含相同的文字欄位反轉。 XML 如下所示：

 **Scene1.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

上述範例使用`merge`縮短檢視程式碼，並簡化的檢視階層。 您可以深入了解`merge`版面配置[這裡](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html)。

透過呼叫建立場景[ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/)，並在容器物件中，資源識別碼場景的版面配置檔，以及目前傳遞`Context`，如下列程式碼範例所示：

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

按一下這個按鈕會在兩個場景，Android 會使用預設轉換值的展示動畫之間翻轉：

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

以下螢幕擷取畫面說明 system.windows.media.animation.repeatbehavior> 的動畫場景：

[![動畫開始之前的應用程式的螢幕擷取畫面](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

以下螢幕擷取畫面說明動畫之後的場景：

[![在動畫完成之後的應用程式的螢幕擷取畫面](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> 沒有[已知的 bug](https://code.google.com/p/android/issues/detail?id=62450)在 Android 的轉換會導致場景的程式庫會建立使用`GetSceneForLayout`使用者完成某項活動的第二個時間導覽時中斷。 描述 java 因應措施[此處](http://www.doubleencore.com/2013/11/new-transitions-framework/)。


##### <a name="custom-transitions-in-scenes"></a>場景中的自訂轉換

可以在將 xml 資源檔中定義自訂的轉換`transition`目錄下`Resources`，如以下螢幕擷取畫面所示：

[![資源/轉換目錄下的 transition.xml 檔案的位置](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

下列程式碼範例定義的轉換，以動畫顯示的 5 秒，並使用[衝過 interpolator](http://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

轉換會建立在活動中使用[TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/)，如下列程式碼所示：

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

新的轉換就會加入至`Go`開始動畫的呼叫：

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>半透明的 UI

KitKat 可讓您更充分掌控佈景主題使用選擇性的半透明狀態和導覽列的應用程式。 您可以變更您用來定義您的 Android 佈景主題相同的 XML 檔案中的系統 UI 項目透明度。 KitKat 導入了下列屬性：

-  `windowTranslucentStatus` -設定為 true，可頂端的狀態列半透明。

-  `windowTranslucentNavigation` -設定為 true，可下方瀏覽列半透明。

-  `fitsSystemWindows` -預設設為頂端或底端列 transcluent 右移透明的 UI 項目底下的內容。 將此屬性設定為`true`是簡單的方式來防止與半透明的系統 UI 項目重疊的內容。


下列程式碼會定義半透明的狀態和導覽列與佈景主題：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

以下螢幕擷取畫面顯示上述半透明狀態與導覽列的佈景主題：

[![使用半透明的狀態和導覽列的應用程式的範例螢幕擷取畫面](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>使用者內容

### <a name="storage-access-framework"></a>儲存體存取架構

儲存體存取 Framework (SAF) 是使用者與儲存的內容，例如影像、 影片和文件互動的新方式。 而不是向使用者顯示對話方塊並選擇 應用程式的內容，KitKat 會開啟新的 UI，可讓使用者存取其資料放在一個彙總的位置。 內容已選擇後，使用者將會傳回給要求內容的應用程式和應用程式體驗將會繼續如往常。

這項變更需要在開發人員端上的兩個動作： 首先，需要來自提供者的內容的應用程式需要更新的要求內容的新方式。 第二個，應用程式將資料寫入`ContentProvider`需要修改成使用新的架構。 這兩個情況取決於新 [`DocumentsProvider`](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/)
API。

#### <a name="documentsprovider"></a>DocumentsProvider

在 KitKat，互動`ContentProviders`與提取`DocumentsProvider`類別。 這表示，SAF 不在乎資料所在實體而言，只要它是可透過存取`DocumentsProvider`API。 本機的提供者，雲端服務和外部存放裝置所有使用相同的介面，和處理相同的方式，使用者和開發人員提供一個使用者的內容互動的地方。

本節說明如何載入和儲存體存取架構儲存內容。

#### <a name="request-content-from-a-provider"></a>從提供者的要求內容

我們可以告訴 KitKat 我們想要挑選使用 SAF UI 使其具備內容`ActionOpenDocument`意圖，這表示我們想要連接到提供給裝置的所有內容提供者。 您可以新增藉由指定此意圖篩選`CategoryOpenable`，這表示您可以開啟 （也就是可供存取的可用內容） 的唯一內容將會傳回。 KitKat 亦能夠篩選內容`MimeType`。 例如，以下的程式碼藉由指定映像的映像結果篩選`MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

呼叫`StartActivityForResult`啟動 SAF UI 中，使用者可以再瀏覽 選擇映像：

[![使用適用於瀏覽至映像的儲存體存取架構的應用程式的範例螢幕擷取畫面](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

使用者已選擇映像之後,`OnActivityResult`傳回`Android.Net.Uri`的所選的檔案。 下列程式碼範例會顯示使用者的映像選取項目：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>將內容寫入至提供者

除了從 SAF UI 載入內容，KitKat 也可讓您將內容儲存至任何`ContentProvider`實作`DocumentProvider`API。 正在儲存內容的用法`Intent`與`ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

上述程式碼範例會載入 SAF UI，讓使用者變更的檔案名稱，然後選取要存放新檔案的目錄：

[![將檔案名稱變更為 NewDoc 下載目錄中的使用者的螢幕擷取畫面](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

當使用者按下**儲存**，`OnActivityResult`傳遞`Android.Net.Uri`的新建立的檔案，可以使用存取`data.Data`。 Uri 可用來將資料串流到新的檔案：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

請注意， [`ContentResolver.OpenOutputStream(Android.Net.Uri)`](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri))
傳回`System.IO.Stream`，因此可以在以.NET 撰寫整串流程序。

如需有關載入的詳細資訊，建立和編輯內容，以儲存體存取架構，請參閱[Android 文件儲存體存取架構](http://developer.android.com/guide/topics/providers/document-provider.html)。

### <a name="printing"></a>列印

列印內容採用簡化 KitKat[列印服務](https://developer.xamarin.com/api/namespace/Android.PrintServices/)和`PrintManager`。 KitKat 也是第一個 API 版本，以充分運用[Google 雲端列印服務 Api](https://developers.google.com/cloud-print/)使用[Google 雲端列印應用程式](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint)。
大部分的裝置會自動隨附 KitKat 下載 Google 雲端列印應用程式和[HP 列印服務外掛程式](https://play.google.com/store/apps/details?id=com.hp.android.printservice)當他們第一次連線至 WiFi。 使用者可以檢查其裝置的列印設定瀏覽至**設定 > System > 列印**:

[![列印設定 畫面的範例螢幕擷取畫面](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> 雖然列印 Api 會使用 Google 雲端列印預設設定，則 Android 仍可讓開發人員準備列印的內容，使用新的 Api，並將它傳送給其他應用程式處理列印。



#### <a name="printing-html-content"></a>列印 HTML 內容

會自動建立 KitKat [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) web 檢視與`WebView.CreatePrintDocumentAdapter`。 列印網頁的內容是人員之間[ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) ，等候要載入的 HTML 內容，並可讓活動知道要在 [選項] 功能表中提供的列印選項和活動，會等候使用者選取 [列印] 選項，然後呼叫`Print`上`PrintManager`。 本節涵蓋螢幕上列印所需的基本設定的 HTML 內容。

請注意，載入和列印的網頁內容都需要網際網路權限：

[![在 應用程式的選項中設定網際網路權限](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>列印功能表項目

[列印] 選項通常會出現在活動的[選項功能表](http://developer.android.com/guide/topics/ui/menus.html#options-menu)。
[選項] 功能表可讓使用者在活動上執行的動作。 它是在螢幕的右上角中，並看起來像這樣：

[![範例螢幕擷取畫面顯示在畫面的右上角的列印功能表項目](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


其他功能表項目可以在定義 *功能表* 目錄下 *資源*。 下列程式碼定義範例功能表項目呼叫[列印](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

在活動中的 [選項] 功能表的互動會透過`OnCreateOptionsMenu`和`OnOptionsItemSelected`方法。
`OnCreateOptionsMenu` 可供新增新的功能表項目，例如 列印 選項中，從 * 功能表* 資源目錄。
`OnOptionsItemSelected` 會接聽在使用者從功能表中，選取 [列印] 選項，並開始列印：

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

上述程式碼也會定義名為`dataLoaded`來追蹤的 HTML 內容的狀態。 `WebViewClient`會設定這個變數設為 true 時載入的所有內容，讓活動知道要將列印功能表項目新增至 [選項] 功能表。

##### <a name="webviewclient"></a>來處理

作業`WebViewClient`是為了確保資料`WebView`列印選項出現在功能表中，它會使用前是否滿載`OnPageFinished`方法。 `OnPageFinished` 會接聽 web 內容以載入結束，並告知要重新建立其與 [選項] 功能表的活動`InvalidateOptionsMenu`:

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished` 也會設定`dataLoaded`值加入`true`，因此`OnCreateOptionsMenu`可以重新建立與位置中的 [列印] 選項功能表。

##### <a name="printmanager"></a>PrintManager

下列程式碼範例會列印的內容`WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` 會當作引數: ("MyWebPage 」 在此範例中)，列印工作的名稱 [`PrintDocumentAdapter`](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/)
從內容中，產生列印文件和 [`PrintAttributes`](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/)
(`null`在上述範例中)。 您可以指定`PrintAttributes`協助配置內容在列印的頁面上，雖然預設的屬性應該處理大部分的情況。

呼叫`Print`載入列印的 UI 中，列出列印工作的選項。 UI 可讓使用者選擇列印或 HTML 內容儲存成 PDF，如以下螢幕擷取畫面所示：

[![螢幕擷取畫面的 PrintHtmlActivity，顯示 [列印] 功能表](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![螢幕擷取畫面的 PrintHtmlActivity，顯示另存成 PDF 功能表](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>硬體

KitKat 加入多個 Api，以容納新的裝置功能。 其中最顯著的是主機型卡模擬和新`SensorManager`。

### <a name="host-based-card-emulation-in-nfc"></a>NFC 中的主機型卡模擬

主機型卡模擬 (HCE) 可讓應用程式，使其行為類似 NFC 卡或 NFC 卡讀取裝置，而無需依賴電訊廠商專屬安全項目。 在之前設定 HCE，請確定 HCE 可在裝置上使用`PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE 要求 HCE 功能及`Nfc`註冊應用程式的權限`AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![在 應用程式的選項中設定 NFC 權限](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

若要運作，HCE 必須能夠在背景中執行，而且具有時要啟動的使用者進行 NFC 交易，即使使用 HCE 的應用程式未執行。 我們可以撰寫為 HCE 程式碼來達成此`Service`。 HCE 服務會實作`HostApduService`介面，它會實作下列方法：

-  *ProcessCommandApdu* -應用程式通訊協定資料單位 (APDU) 是取得傳送 NFC 讀取器和 HCE 服務之間。 這個方法會取用 ADPU 從讀取器，並在回應中傳回的資料單位。

-  *OnDeactivated* - `HostAdpuService` HCE 服務不會再通訊使用 NFC 讀取器時停用。


HCE 服務也必須向應用程式的資訊清單中，並使用適當的權限、 意圖篩選和中繼資料裝飾。 下列程式碼是範例`HostApduService`向 Android 資訊清單使用`Service`屬性 (如需有關屬性的詳細資訊，請參閱 Xamarin[使用 Android 資訊清單](~/android/platform/android-manifest.md)指南):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("android.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

上述服務可讓 NFC 讀者互動應用程式，但 NFC 讀取器仍有沒有辦法知道如果此服務會模擬需要掃描 NFC 卡片。 為了協助識別服務的 NFC 讀取器，我們可以將服務指派的唯一*應用程式識別碼 (AID)*。 我們指定協助，HCE 服務時，相關的其他中繼資料 xml 資源檔中註冊`MetaData`屬性 （請參閱上述的程式碼範例）。 這個資源檔會指定一或多個輔助篩選-對應至一或多個 NFC 讀取器裝置的輔助工具的唯一識別項字串以十六進位格式：

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

除了協助篩選 xml 資源檔也會提供使用者面向服務的描述 HCE，指定協助群組 （與 「 其他 」 的付款應用程式），在 「 付款 」 應用程式的 260 x 96 dp 橫幅，以向使用者顯示。

上述安裝程式會提供模擬 NFC 卡應用程式的基本建置組塊。 NFC 本身需要幾個步驟，並進一步測試，以設定。 如需有關主機型卡模擬的詳細資訊，請參閱[Android 文件入口網站](https://developer.android.com/guide/topics/connectivity/nfc/hce.html)。
如需有關如何搭配 Xamarin 使用 NFC 的詳細資訊，請參閱[Xamarin NFC 範例](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample)。

### <a name="sensors"></a>感應器

KitKat 提供存取裝置的感應器，透過[ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/)。
`SensorManager`允許 OS 在排程傳遞到批次中的應用程式的感應器資訊保留電池壽命。

KitKat 也會隨附兩個新的感應器類型用於追蹤使用者的步驟。 類型為基礎的加速計，其中包含：

-  *StepDetector* -應用程式會收到通知/事項時使用者採取的步驟，並偵測器步驟發生時提供的時間值。

-  *StepCounter* -記錄的感應器已註冊以來，使用者已經採取的步驟數目*直到下次裝置重新開機*。

以下螢幕擷取畫面顯示作用中的步驟計數器：

[![顯示步驟計數器 SensorsActivity 應用程式的螢幕擷取畫面](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

您可以建立`SensorManager`藉由呼叫`GetSystemService(SensorService)`並將轉換結果當做`SensorManager`。 若要使用的步驟計數器，請呼叫`GetDefaultSensor`上`SensorManager`。 您可以註冊的感應器，並接聽中的協助步驟計數的變更 [`ISensorEventListener`](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/)
介面，如下列程式碼範例所示：

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

`OnSensorChanged` 如果逐步執行計數會更新應用程式在前景時，會呼叫。 如果應用程式進入背景，或裝置會進入睡眠狀態`OnSensorChanged`將不會呼叫; 不過，步驟會繼續直到要計算`UnregisterListener`呼叫。

請記住*註冊感應器的所有應用程式，步驟計數值會累計*。 這表示即使您解除安裝並重新安裝您的應用程式，並初始化`count`變數 0 在應用程式啟動時，感應器回報的值會維持已註冊的感應器，而採取的步驟總數是否由您應用程式或另一個。 您可以防止應用程式加入步驟計數器，藉由呼叫`UnregisterListener`上`SensorManager`，如下列程式碼所示：

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

將裝置重新開機逐步計數將重設為 0。 您的應用程式將會需要額外的程式碼，以確保它會報告準確的計數，讓應用程式，不論其他應用程式使用的感應器或裝置的狀態。


> [!NOTE]
> 步驟偵測和計算 KitKat 隨附的 API，而不是所有的電話被科系與感應器。 您可以檢查感應器是否可用，執行`PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`，或檢查，以確保傳回的值`GetDefaultSensor`不是`null`。


<a name="developer_tools" />

## <a name="developer-tools"></a>開發人員工具

### <a name="screen-recording"></a>螢幕錄影

KitKat 包含新的螢幕錄製功能，以便開發人員可以在動作記錄應用程式。 螢幕錄製已可透過[Android Debug Bridge (ADB)](http://developer.android.com/tools/help/adb.html)用戶端，可以下載 Android SDK 的一部分。

若要錄製您的畫面，連接您的裝置;然後，找出您的 Android SDK 安裝中，瀏覽至**平台工具**目錄，然後執行**adb**用戶端：

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

上述命令會記錄的預設 3 分鐘影片 4Mbps 預設解析度。 若要編輯的長度，新增 *-時間限制*旗標。
若要變更解析度，請新增 *-位元速率*旗標。 下列命令會記錄在 8Mbps 長時間的分鐘影片：

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

您可以在裝置上找到您的影片-完成錄製時，它會顯示在您的資源庫。


## <a name="other-kitkat-additions"></a>其他 KitKat 新增項目

除了上面所述的變更，KitKat 可讓您：

-  *使用全螢幕*-KitKat 導入了新[沈浸式模式](http://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int))瀏覽內容、 玩遊戲，及執行其他應用程式可從全螢幕體驗幫助。

-  *自訂通知*-取得系統通知有關的其他詳細資料 [`NotificationListenerService`](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/)
   。 這可讓您在不同的方式，在您的應用程式中呈現的資訊。

-  *鏡像可繪製資源*-有新的可繪製資源 [`autoMirrored`](http://developer.android.com/reference/android/R.attr.html#autoMirrored)
   告訴系統的屬性建立鏡像的版本，需要由左到右配置翻轉的影像。

-  *暫停動畫*-暫停和繼續與所建立的動畫 [`Animator`](https://developer.xamarin.com/api/type/Android.Animation.Animator/)
   類別的新執行個體。

-  *讀取動態變更文字*-表示 UI 的與新的 「 即時地區 」 的新文字以動態方式更新的組件 [ `accessibilityLiveRegion`](http://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
   因此會自動讀取新的文字，以協助工具模式的屬性。

-  *增強音訊體驗*-請大聲點會追蹤與 [`LoudnessEnhancer`](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/)
   找到的尖峰與音訊資料流使用的 RMS [`Visualizer`](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/)
   類別，並取得資訊[音訊的時間戳記](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/)來協助進行音訊視訊同步處理。

-  *自訂間隔同步 ContentResolver* -KitKat 執行同步處理要求的時間將有些不同。 同步處理`ContentResolver`在自訂時間或藉由呼叫的間隔`ContentResolver.RequestSync`並傳入`SyncRequest`。

-  *控制器之間的區別*-在 KitKat 控制站指派，您可以透過裝置的唯一整數識別碼`ControllerNumber`屬性。 這可讓您告訴相隔玩家在遊戲中的工作變得更容易。

-  *遙控器*-硬體和軟體端上進行一些變更，KitKat 可讓您開啟裝置的遠端控制使用科系與 IR 傳輸器`ConsumerIrService`，並與其互動與新的周邊裝置 [`RemoteController`](https://developer.xamarin.com/api/type/Android.Media.RemoteController/)
   Api。

如需有關上述 API 變更的詳細資訊，請參閱 Google [Android 4.4 Api](http://developer.android.com/about/versions/android-4.4.html)概觀。


## <a name="summary"></a>總結

本文會介紹一些 Android 4.4 (API Level 19) 中提供的新 Api，並涵蓋轉換 KitKat 應用程式時的最佳作法。 It 所述的 Api 會影響使用者體驗的變更，包括*轉換架構*和新選項*佈景主題*。 接下來，它引進*儲存體存取架構*並`DocumentsProvider`類別，以及新*列印 Api*。 瀏覽它*NFC 主機型卡模擬*，以及如何使用*低電源感應器*，包括兩個新的感應器，用於追蹤使用者的步驟。 最後，課程中示範了擷取的應用程式的即時示範*螢幕錄製*，並提供 KitKat API 變更和新功能的詳細的清單。


## <a name="related-links"></a>相關連結

- [KitKat 範例](https://developer.xamarin.com/samples/KitKat/)
- [Android 4.4 的 Api](http://developer.android.com/about/versions/android-4.4.html)
- [Android 的 KitKat](http://developer.android.com/about/versions/kitkat.html)
