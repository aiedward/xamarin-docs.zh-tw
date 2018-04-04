---
title: KitKat 功能
description: Android 4.4 (KitKat) 會載入與 cornucopia 的使用者和開發人員的功能。 本指南會反白顯示這些功能，並提供程式碼範例和實作細節，以協助您充分利用 KitKat 進行。
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 3c3eafc8dc18113080dd6c906025556292c43e1c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="kitkat-features"></a>KitKat 功能

_Android 4.4 (KitKat) 會載入與 cornucopia 的使用者和開發人員的功能。本指南會反白顯示這些功能，並提供程式碼範例和實作細節，以協助您充分利用 KitKat 進行。_

## <a name="overview"></a>總覽

Android 4.4 (API Level 19)，也稱為"KitKat，「 已於 2013 落後發行。 KitKat 提供各種不同的新功能和增強功能，包括：

-  [使用者經驗](#user_experience)&ndash;轉換架構、 半透明的狀態和導覽列和全螢幕沉浸模式的簡單動畫幫助您建立的使用者更好的體驗。

-  [使用者內容](#user_content)&ndash;管理使用者檔案與儲存體存取 framework 簡化列印圖片、 網站和其他內容變得更容易改善列印應用程式開發介面。

-  [硬體](#hardware)&ndash;變成 NFC 主機型卡模擬 NFC 卡中的任何應用程式; 執行的低電源感應器`SensorManager`。

-  [開發人員工具](#developer_tools)&ndash;錄影畫面應用程式的動作與 Android Debug Bridge 用戶端，可以使用 Android SDK 的一部分。


本指南提供現有 Xamarin.Android 應用程式移轉至 KitKat，為概要 KitKat Xamarin.Android 開發人員的指引。

## <a name="requirements"></a>需求

若要開發使用 KitKat Xamarin.Android 應用程式，您需要*Xamarin.Android 4.11.0*或較高和 Android 4.4 (API Level 19) 安裝透過 Android SDK Manager 中，如下列螢幕擷取畫面所示：

[![在 Android SDK Manager 中選取 Android 4.4](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>將您的應用程式移轉至 KitKat

本節提供一些協助以 Android 4.4 轉換現有的應用程式的第一個回應項目。

### <a name="check-system-version"></a>檢查作業系統版本

如果必須相容於舊版的 Android 應用程式，請務必將任何 KitKat 專屬的程式碼包裝在系統版本檢查，如下列程式碼範例所示：

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>警示批次處理

Android 使用警示服務在指定的時間喚醒在背景中的應用程式。 KitKat 會進一步批次處理保留電源的警示。 這表示，就不需在確切的時間喚醒每個應用程式，KitKat 偏好在群組註冊喚醒在相同的時間間隔，並將其喚醒一次的數個應用程式。
若要告知 Android 喚醒應用程式指定的時間間隔內，呼叫`SetWindow`上[ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/)，並傳遞最小值和最大時間 （毫秒） 之前，可以經過事項應用程式，以及要執行的作業中在喚醒。
下列程式碼會提供以半小時之間的一小時，從視窗設定的時間會決定需要的應用程式的範例：

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

若要繼續在確切的時間喚醒應用程式，請使用`SetExact`，傳入的確切時間應該決定應用程式，以及要執行的作業：

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat 不再可讓您設定確切的重複警示。 使用應用程式[ `SetRepeating` ](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/) ，而且需要精確的警示，才能將現在必須手動觸發警示。

### <a name="external-storage"></a>外部儲存體

外部儲存體現在分成兩種類型的儲存體唯一您的應用程式和多個應用程式所共用的資料。 讀取和寫入至您的應用程式的特定位置，在外部儲存體不需要特殊權限。 現在與共用存放裝置上的資料互動需要`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`權限。 您可以在這種情況分類兩種類型：

-  如果您在上呼叫方法來取得檔案或目錄路徑`Context`-例如， [ `GetExternalFilesDir` ](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/)或 [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - 您的應用程式需要任何額外的權限。

-  如果您要存取屬性或上呼叫方法來取得檔案或目錄路徑`Environment`，例如[ `GetExternalStorageDirectory` ](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/)或[ `GetExternalStoragePublicDirectory` ](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/) ，應用程式需要`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`權限。

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` 表示`READ_EXTERNAL_STORAGE`權限，因此您應該永遠只需要設定一個權限。

### <a name="sms-consolidation"></a>SMS 彙總

KitKat 簡化使用者的訊息彙總使用者所選取的一個預設應用程式中的所有 SMS 內容。 開發人員負責讓應用程式的預設訊息的應用程式，可選取和其運作方式適當地在程式碼，並在生活中如果未選取應用程式。 如需有關轉換 KitKat SMS 應用程式的詳細資訊，請參閱[取得您 SMS 應用程式準備好進行 KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html)將來自 Google 的指南。

### <a name="webview-apps"></a>WebView 應用程式

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) KitKat 中取得的新裝。 最大的變更會加入載入至內容的安全性`WebView`。 大部分以較舊的 API 版本為目標的應用程式應該工作如預期般，使用的應用程式測試`WebView`強烈建議類別。 如需有關受影響 WebView 應用程式開發介面參考 Android[移轉至在 Android 4.4 WebView](http://developer.android.com/guide/webapps/migrating.html)文件。

<a name="user_experience" />

## <a name="user-experience"></a>使用者經驗

KitKat 隨附數個新的 Api，可增強使用者體驗，包括新的轉換架構來處理屬性動畫和佈景主題的半透明 UI 選項。 以下會涵蓋這些變更。

### <a name="transition-framework"></a>轉換架構

轉換架構可讓您更輕鬆地實作動畫。 KitKat 可讓您執行簡單的屬性動畫，其中只一行程式碼，或自訂轉換使用*場景*。

#### <a name="simple-property-animation"></a>簡單屬性動畫

新的 Android 轉換程式庫可簡化程式碼後置屬性動畫。 此架構可讓您執行最少程式碼的簡單動畫。 例如，下列程式碼範例會使用[ `TransitionManager.BeginDelayedTransition` ](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/)要製作動畫顯示和隱藏`TextView`:

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

上述範例會使用轉換 framework 建立的自動，預設值變更的屬性值之間的轉換。 因為動畫由單一行程式碼中處理，您可以輕鬆地進行這與舊版本的 Android 相容包裝`BeginDelayedTransition`呼叫中的系統版本檢查。 請參閱[移轉您的應用程式至 KitKat](#Migrating_Your_App_to_KitKat) > 一節，如需詳細資訊。

下列螢幕擷取畫面顯示動畫之前應用程式：

[![動畫啟動前的應用程式螢幕擷取畫面](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

下列螢幕擷取畫面顯示動畫之後的應用程式：

[![動畫完成之後的應用程式螢幕擷取畫面](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

您可以取得更充分掌控場景下, 一節涵蓋的轉換。

#### <a name="android-scenes"></a>Android 場景

[場景](https://developer.xamarin.com/api/type/Android.Transitions.Scene/)引進轉換架構，以讓開發人員更充分掌控動畫的一部分。 場景在 UI 中建立動態的區域： 您指定容器和數個版本，或是 「 場景"，容器內的 XML 內容而 Android 不其餘的工作要製作動畫場景之間的轉換。 Android 場景可讓您在開發端建立複雜的動畫，具有最少的工作。

靜態罩動態內容的 UI 項目稱為*容器*或*場景基底*。 下列範例會使用 Android 設計工具來建立`RelativeLayout`呼叫`container`:

[![若要建立 RelativeLayout 容器使用 Android 設計工具](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

範例配置也會定義按鈕呼叫`sceneButton`下方`container`。 此按鈕將會觸發轉換。

容器內的動態內容需要兩個新的 Android 配置。 這些配置指定的程式碼*內*容器。
下列範例程式碼定義版面配置，稱為*Scene1*分別包含兩個文字欄位，讀取 「 套件 」 和 「 Kat"，而且第二個配置呼叫*Scene2*包含反轉的相同文字欄位。 XML 如下所示：

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

以上範例使用`merge`縮短檢視程式碼，並簡化檢視階層。 您可以深入了解`merge`配置[這裡](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html)。

透過呼叫建立場景[ `Scene.GetSceneForLayout`](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/)容器物件，場景的版面配置檔案，以及目前資源識別碼傳遞給`Context`，如下列程式碼範例所示：

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

按一下按鈕翻轉之間的兩個場景，Android 以動畫方式顯示使用預設轉換值：

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

下列螢幕擷取畫面說明之前動畫場景：

[![動畫開始前的應用程式的螢幕擷取畫面](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

下列螢幕擷取畫面說明場景動畫之後：

[![在動畫完成之後，應用程式的螢幕擷取畫面](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> 沒有[已知的錯誤](https://code.google.com/p/android/issues/detail?id=62450)在 Android 轉換導致場景的程式庫會建立使用`GetSceneForLayout`使用者的瀏覽活動透過第二次時中斷。 Java 的因應措施會描述[這裡](http://www.doubleencore.com/2013/11/new-transitions-framework/)。


##### <a name="custom-transitions-in-scenes"></a>場景中的自訂轉換

可以在 xml 資源檔中定義自訂的轉換`transition`目錄下`Resources`，如以下螢幕擷取畫面所示：

[![資源/轉換目錄下的 transition.xml 檔案的位置](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

下列程式碼範例定義的動畫 5 秒鐘，並使用轉換來[超過 interpolator](http://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

轉換會在活動中建立使用[TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/)，如下列程式碼所示：

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

新的轉換就會加入至`Go`開始動畫的呼叫：

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>半透明的 UI

KitKat 可讓您更多控制主題設定應用程式與選擇性 transclucent 狀態和導覽列。 您可以變更您用來定義您的 Android 佈景主題相同的 XML 檔案中的系統 UI 項目的半透明。 KitKat 導入了下列屬性：

-  `windowTranslucentStatus` -當設定為 true，會使得頂端的狀態列半透明。

-  `windowTranslucentNavigation` -當設定為 true，會使得下方瀏覽列半透明。

-  `fitsSystemWindows` -設為頂端或底端列 transcluent 移位預設透明的 UI 項目底下的內容。 此屬性設定為`true`沒有簡易的方式，可防止與半透明的系統 UI 項目重疊的內容。


下列程式碼會定義具有半透明的狀態和導覽列佈景主題：

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

下列螢幕擷取畫面顯示上述半透明的狀態與導覽列的佈景主題：

[![使用半透明的狀態和導覽列的應用程式的範例螢幕擷取畫面](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>使用者內容

### <a name="storage-access-framework"></a>儲存體存取架構

儲存體存取架構 (SAF) 是與儲存的內容，例如影像、 影片和文件互動的使用者的新方式。 而不是對話方塊並選擇應用程式的內容呈現給使用者，KitKat 會開啟新的使用者介面，可讓使用者存取其資料在同一個彙總的位置。 一旦選擇的內容，使用者將會傳回給要求內容的應用程式和應用程式體驗將會繼續正常。

這項變更需要兩個動作開發人員端： 首先，需要提供者內容應用程式都必須更新為新的方式來 reqesting 內容。 第二個，應用程式將資料寫入`ContentProvider`需要修改成使用新的架構。 這兩個情況取決於新[ `DocumentsProvider` ](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/)應用程式開發介面。

#### <a name="documentsprovider"></a>DocumentsProvider

中的互動，KitKat`ContentProviders`會以抽象`DocumentsProvider`類別。 這表示，SAF 不在意資料所在實體，只要它是可透過存取`DocumentsProvider`應用程式開發介面。 本機的提供者，雲端服務和外部存放裝置所有使用相同的介面，並會以相同方式，處理使用者和開發人員提供一個位置可用以互動使用者的內容。

本章節涵蓋如何載入及儲存內容儲存體存取架構。

#### <a name="request-content-from-a-provider"></a>從提供者的要求內容

我們可以確定 KitKat 我們想要挑選使用以 SAF UI 內容`ActionOpenDocument`意圖，表示我們想要連接到該裝置可用的所有內容提供者。 您可以新增藉由指定此意圖篩選`CategoryOpenable`，這表示僅限內容，以便開啟 （也就是可存取，可使用內容） 將會傳回。 KitKat 亦能夠篩選內容`MimeType`。 例如，藉由指定映像的映像結果篩選條件將下列程式碼`MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

呼叫`StartActivityForResult`啟動 SAF UI 中，使用者可以接著瀏覽並選擇映像：

[![使用瀏覽至映像儲存體存取架構應用程式的範例螢幕擷取畫面](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

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

#### <a name="write-content-to-a-provider"></a>寫入提供者內容

除了從 SAF UI 載入內容時，KitKat 也可讓您將內容儲存至任何`ContentProvider`實作`DocumentProvider`應用程式開發介面。 儲存內容使用`Intent`與`ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

上述程式碼範例會載入 SAF UI，讓使用者變更的檔案名稱，並選取以容納新的檔案目錄：

[![[下載] 目錄中，將檔案名稱變更為 NewDoc 使用者的螢幕擷取畫面](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

當使用者按**儲存**，`OnActivityResult`傳遞`Android.Net.Uri`的新建檔案，可以使用存取`data.Data`。 Uri 可以用於資料流資料到新的檔案：

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

請注意， [ `ContentResolver.OpenOutputStream(Android.Net.Uri)` ](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri))傳回`System.IO.Stream`，因此整個資料流的程序可以使用.NET 撰寫。

如需有關載入的詳細資訊，建立及編輯內容的儲存體存取架構，請參閱[Android 文件的儲存體存取 Framework](http://developer.android.com/guide/topics/providers/document-provider.html)。

### <a name="printing"></a>列印

列印內容的介紹，已簡化中 KitKat[列印服務](https://developer.xamarin.com/api/namespace/Android.PrintServices/)和`PrintManager`。 KitKat 也是第一個應用程式開發介面版本完全利用[Google 雲端列印服務應用程式開發介面](https://developers.google.com/cloud-print/)使用[Google 雲端列印應用程式](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint)。
自動隨附 KitKat 的大部分裝置下載 Google 雲端列印應用程式和[HP 列印服務外掛程式](https://play.google.com/store/apps/details?id=com.hp.android.printservice)當他們第一次連線到 WiFi。 使用者可以檢查其裝置的列印設定，瀏覽至**設定 > System > 列印**:

[![範例螢幕擷取畫面的 [列印設定] 畫面](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> 列印應用程式開發介面設定為使用 Google 雲端列印依預設，雖然 Android 仍可讓開發人員準備使用新的 Api，列印的內容，並將它傳送給其他應用程式處理列印。



#### <a name="printing-html-content"></a>列印 HTML 內容

會自動建立 KitKat [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) web 檢視與`WebView.CreatePrintDocumentAdapter`。 列印網頁的內容是人員之間共同[ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) ，等候要載入的 HTML 內容，並可讓活動知道要在 [選項] 功能表中提供列印選項和 Actvity，其會等候使用者選取 [列印] 選項，然後呼叫`Print`上`PrintManager`。 此章節將涵蓋螢幕上列印所需的基本安裝 HTML 內容。

請注意，載入和列印網頁內容需要網際網路權限：

[![在應用程式選項中設定網際網路權限](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>列印功能表項目

列印選項通常會出現在活動的[選項功能表](http://developer.android.com/guide/topics/ui/menus.html#options-menu)。
[選項] 功能表可讓使用者執行動作的活動。 處於 右上角的畫面上，而且看起來像這樣：

[![列印功能表項目 dispalyed 螢幕的右上角中的範例螢幕擷取畫面](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


其他功能表項目中可以定義*功能表*目錄下*資源*。 下列程式碼定義範例功能表項目稱為[列印](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

透過在活動中的 [選項] 功能表的互動進行`OnCreateOptionsMenu`和`OnOptionsItemSelected`方法。
`OnCreateOptionsMenu` 在此加入新的功能表項目，例如 [列印] 選項，從*功能表*資源目錄。
`OnOptionsItemSelected` 接聽在使用者從功能表中，選取 [列印] 選項，並開始列印：

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

上述程式碼也會定義名為`dataLoaded`追蹤的 HTML 內容的狀態。 `WebViewClient`會設定這個變數設為 true 時載入的所有內容，讓活動知道列印功能表項目加入至 [選項] 功能表。

##### <a name="webviewclient"></a>WebViewClient

作業的`WebViewClient`可確保資料在`WebView`是完全載入之前列印選項會出現在功能表上，與`OnPageFinished`方法。 `OnPageFinished` 完成載入，web 內容會接聽並告知重新建立其選項功能表與活動`InvalidateOptionsMenu`:

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

`OnPageFinished` 也會設定`dataLoaded`值設定為`true`，因此`OnCreateOptionsMenu`可以重新建立具有位置中的 [列印] 選項的功能表。

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

`Print` 會當作引數: ("MyWebPage 「 在此範例中)，列印工作的名稱[ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/) ，來自內容，產生列印文件和[ `PrintAttributes` ](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/) (`null`中上述範例）。 您可以指定`PrintAttributes`協助雖然預設的屬性應該處理大部分 scenarions 配置列印的頁面上的內容。

呼叫`Print`載入列印的 UI 中，列出的列印工作選項。 UI 可讓使用者選擇列印或 HTML 內容儲存成 PDF，如以下螢幕擷取畫面所示：

[![螢幕擷取畫面的 PrintHtmlActivity，顯示 [列印] 功能表](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![螢幕擷取畫面的 PrintHtmlActivity，顯示 儲存成 PDF 功能表](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>硬體

KitKat 新增多個 Api，以配合新的裝置功能。 最值得注意的是主機型卡模擬與新`SensorManager`。

### <a name="host-based-card-emulation-in-nfc"></a>NFC 中的主機型卡模擬

主機型卡模擬 (HCE) 可讓應用程式的行為類似 NFC 卡或 NFC 卡讀取裝置，不需依賴 貨運公司的專屬安全性項目。 請先設定 HCE 時，請確認 HCE 已與裝置上的可用`PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE 要求 HCE 功能和`Nfc`向應用程式的權限`AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![在應用程式選項中設定 NFC 權限](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

HCE 必須為能夠在背景中執行工作，而且必須啟動使用者的 NFC 交易時，即使使用 HCE 的應用程式未執行。 我們可以完成此作業藉由撰寫 HCE 程式碼做為`Service`。 HCE 服務會實作`HostApduService`介面，它會實作下列方法：

-  *ProcessCommandApdu* -應用程式通訊協定資料單位 (APDU) 是什麼取得 NFC 讀取器和服務之間傳送 HCE。 這個方法會消耗 ADPU 從讀取器，並在回應中傳回的資料單位。

-  *OnDeactivated* - `HostAdpuService` HCE 服務不會再通訊 NFC 讀取器時停用。


HCE 服務也需要向應用程式的資訊清單，並使用適當的權限、 意圖的篩選器和中繼資料裝飾的成員。 下列程式碼是範例`HostApduService`向 Android 資訊清單使用`Service`屬性 (如需有關屬性的詳細資訊，請參閱 Xamarin[使用 Android 資訊清單](~/android/platform/android-manifest.md)指南):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("andorid.nfc.cardemulation.host.apdu_service",
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

上述的服務提供 NFC 讀取器與應用程式互動的方式，但 NFC 讀取器仍會有無從得知如果此服務會模擬需要掃描 NFC 卡片。 為了協助識別服務 NFC 讀取器，我們可以指派服務唯一*應用程式識別碼 （輔助）*。 我們指定協助，以及其他有關 HCE 服務的中繼資料為 xml 資源檔向`MetaData`屬性 （請參閱上述的程式碼範例）。 此資源檔案會指定一或多個輔助篩選-十六進位格式中唯一識別項字串對應至一或多個 NFC 讀取器裝置的輔助工具：

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

除了輔助篩選 xml 資源檔也會提供使用者對象 HCE 服務描述，指定的輔助工具群組 （與 「 其他 」 的付款應用程式），如果付款應用程式，以向使用者顯示為 260 x 96 dp 橫幅。

以上所述安裝程式會提供模擬 NFC 卡應用程式的基本建置組塊。 NFC 本身需要幾個步驟，以及進一步的測試設定。 如需有關主機型卡模擬的詳細資訊，請參閱[Android 文件入口網站](https://developer.android.com/guide/topics/connectivity/nfc/hce.html)。
如需有關使用 NFC 使用 Xamarin 的詳細資訊，請參閱[Xamarin NFC 範例](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample)。

### <a name="sensors"></a>感應器

KitKat 提供存取權的裝置感應器透過[ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/)。
`SensorManager`允許作業系統排程傳遞批次中的應用程式的感應器資訊保留電池壽命。

KitKat 也存隨附兩個新的感應器類型，用於追蹤使用者的步驟。 加速計為基礎，其中包含：

-  *StepDetector* -應用程式通知/事項時使用者採取的步驟，並偵測器步驟發生時提供時間值。

-  *StepCounter* -追蹤的使用者採取因為感應器已註冊的步驟數目*直到下一步 重新啟動裝置*。

下列螢幕擷取畫面說明的步驟中的計數器動作：

[![顯示步驟計數器 SensorsActivity 應用程式的螢幕擷取畫面](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

您可以建立`SensorManager`藉由呼叫`GetSystemService(SensorService)`和轉換結果當做`SensorManager`。 若要使用的步驟計數器，請呼叫`GetDeafultSensor`上`SensorManager`。 您可以註冊感應器及逐步執行計數的說明中的變更接聽[ `ISensorEventListener` ](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/)介面，如下列程式碼範例所示：

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

`OnSensorChanged` 如果步驟計數更新應用程式位於前景時，呼叫。 如果應用程式進入背景，或裝置處於睡眠模式，`OnSensorChanged`將不會呼叫; 不過，步驟將會繼續直到計算`UnregisterListener`呼叫。

請注意，*註冊感應器的所有應用程式步驟的 [計數] 值是累計*。 這表示即使您解除安裝並重新安裝您的應用程式，並初始化`count`變數 0 應用程式啟動時，感應器回報的值會保留已登錄感應器，所進行的步驟的總數是否由您應用程式或另一個。 您可以防止應用程式藉由呼叫加入至步驟計數器`UnregisterListener`上`SensorManager`，如下列程式碼所示：

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

重新開機裝置會逐步執行計數重設為 0。 您的應用程式將需要額外的程式碼，以確保它報告精確的計數，讓應用程式，不論其他應用程式使用感應器或裝置的狀態。


> [!NOTE]
> 步驟偵測和計算隨附 KitKat 的 API，而不是所有的電話被攜帶感應器。 您可以檢查感應器是否可以藉由執行`PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`，或檢查，以確保傳回的值為`GetDefaultSensor`不`null`。


<a name="developer_tools" />

## <a name="developer-tools"></a>開發人員工具

### <a name="screen-recording"></a>螢幕錄製

KitKat 包括記錄功能，讓開發人員可以在動作記錄的應用程式的新螢幕。 螢幕錄製是透過[Android 偵錯橋接器 (ADB)](http://developer.android.com/tools/help/adb.html)用戶端，可以下載 Android SDK 的一部分。

若要錄製螢幕，您的裝置; 的連線然後，找出您的 Android SDK 安裝，請瀏覽至**平台工具**目錄中，而且執行**adb**用戶端：

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

上述命令會記錄預設 3 分鐘的影片 4Mbps 預設解析度。 若要編輯的長度，新增*-時間限制*旗標。
若要變更解析度，新增*-位元速率*旗標。 下列命令會記錄在 8Mbps 分鐘長時間視訊：

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

您可以在裝置上找到您的視訊-完成錄製時，它會顯示在您的組件庫。


## <a name="other-kitkat-additions"></a>其他 KitKat 新增

除了上面所述的變更，KitKat 可讓您：

-  *使用全螢幕*-KitKat 導入了新[沉浸模式](http://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int))瀏覽內容、 玩遊戲，和執行其他應用程式無法從全螢幕經驗中獲益。

-  *自訂通知*-取得其他詳細資料與系統通知[ `NotificationListenerService` ](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/) 。 這可讓您在您的應用程式內不同的方式呈現資訊。

-  *鏡像 Drawable 資源*-Drawable 資源有新[ `autoMirrored` ](http://developer.android.com/reference/android/R.attr.html#autoMirrored)告訴系統的屬性建立左到右配置中需要 翻轉的影像的鏡像的版本。

-  *暫停的動畫*-以建立暫停及繼續動畫[ `Animator` ](https://developer.xamarin.com/api/type/Android.Animation.Animator/)類別。

-  *讀取以動態方式變更文字*-表示 UI 的動態更新與新的文字為 「 即時區域 」 與新的組件[ `accesibilityLiveRegion` ](http://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)屬性，讓將自動讀取新的文字，以協助工具模式。

-  *增強音訊經驗*-請大聲點會追蹤與[ `LoudnessEnhancer` ](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/) ，尋找尖峰和音訊資料流使用的 RMS [ `Visualizer` ](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/)類別，並從取得資訊[音訊的時間戳記](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/)協助音訊視訊同步處理。

-  *自訂的間隔同步 ContentResolver* -KitKat 將一些變化性加入至同步處理要求所執行的時間。 同步處理`ContentResolver`在自訂的時間或間隔藉由呼叫`ContentResolver.RequestSync`和傳入`SyncRequest`。

-  *控制器之間的區別*-在 KitKat 控制站都會指派唯一整數識別碼，可透過裝置的存取`ControllerNumber`屬性。 這可讓您更方便區分相距玩家的遊戲。

-  *遠端控制*-硬體和軟體端進行一些變更，KitKat 可讓您若要開啟紅外線傳送器攜帶至遠端控制使用的裝置`ConsumerIrService`，並與其互動週邊裝置與新[ `RemoteController` ](https://developer.xamarin.com/api/type/Android.Media.RemoteController/)應用程式開發介面。

如需有關上述 API 變更的詳細資訊，請參閱 Google [Android 4.4 Api](http://developer.android.com/about/versions/android-4.4.html)概觀。


## <a name="summary"></a>總結

本文章導入了一些新的 Api 可在 Android 4.4 (API Level 19)，並涵蓋轉換 KitKat 應用程式時的最佳作法。 遇到外框的變更影響使用者的 Api，包括的 it*轉換 framework*和新選項*主題*。 接下來，它導入*存放裝置存取 Framework*和`DocumentsProvider`類別，以及新*列印 Api*。 瀏覽它*NFC 主機型卡模擬*以及如何使用*低電源感應器*，包括兩個新感應器，用於追蹤使用者的步驟。 最後，示範擷取的應用程式的即時示範*螢幕錄製*，並提供 KitKat API 變更和新功能的詳細的清單。


## <a name="related-links"></a>相關連結

- [KitKat 範例](https://developer.xamarin.com/samples/KitKat/)
- [Android 4.4 應用程式開發介面](http://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](http://developer.android.com/about/versions/kitkat.html)
