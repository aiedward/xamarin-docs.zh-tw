---
title: KitKat 功能
description: Android 4.4 (KitKat) 已載入供使用者和開發人員使用的功能多。 本指南將重點放在其中幾項功能, 並提供程式碼範例和執行詳細資料, 以協助您充分利用 KitKat。
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: efb55b060243950640978ae2cfc3fe6998efdb87
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643466"
---
# <a name="kitkat-features"></a>KitKat 功能

_Android 4.4 (KitKat) 已載入供使用者和開發人員使用的功能多。本指南將重點放在其中幾項功能, 並提供程式碼範例和執行詳細資料, 以協助您充分利用 KitKat。_

## <a name="overview"></a>總覽

Android 4.4 (API 層級 19) (也稱為 "KitKat") 已于晚期2013發行。 KitKat 提供各種新功能和改進, 包括:

-  [使用者體驗](#user_experience)&ndash;使用轉換架構、半透明狀態和流覽列, 以及全螢幕沉浸式模式的簡單動畫, 有助於為使用者建立更好的體驗。

-  [使用者內容](#user_content)&ndash;使用存放裝置存取架構簡化使用者檔案管理; 列印圖片、網站和其他內容, 都能更輕鬆地使用改良的列印 api。

-  [硬體](#hardware)使用 nfc 主機型插卡模擬, 將任何應用程式轉換為 nfc 記憶卡; 執行具有的`SensorManager`低電源感應器。 &ndash;

-  [開發人員工具](#developer_tools)&ndash;使用 Android Debug Bridge 用戶端來螢幕錄製影片應用程式, 可做為 Android SDK 的一部分。


本指南提供將現有的 Xamarin Android 應用程式遷移至 KitKat, 以及適用于 Xamarin. Android 開發人員的高階 KitKat 總覽的指引。

## <a name="requirements"></a>需求

若要使用 KitKat 開發 Xamarin 應用程式, 您需要透過 Android SDK Manager 安裝的*4.11.0*或更高版本, 以及 Android 4.4 (API 層級 19), 如下列螢幕擷取畫面所示:

[![在 Android SDK 管理員中選取 Android 4。4](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>將您的應用程式遷移至 KitKat

本節提供一些第一個回應專案, 可協助您將現有的應用程式轉換成 Android 4.4。

### <a name="check-system-version"></a>檢查系統版本

如果應用程式必須與舊版的 Android 相容, 請務必將任何 KitKat 特有的程式碼包裝在系統版本檢查中, 如下列程式碼範例所示:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>鬧鐘批次處理

Android 使用警示服務在指定的時間內, 于背景喚醒應用程式。 KitKat 會藉由批次處理警示來繼續執行, 以保留電源。 這表示, 而不是在確切的時間喚醒每個應用程式, KitKat 偏好將數個已註冊要喚醒的應用程式群組在相同的時間間隔內, 並同時喚醒它們。
要告訴嵽指定的時間間隔內的應用程式的 Android，呼叫`SetWindow`上[`AlarmManager`](xref:Android.App.AlarmManager)，傳遞最小和最大時間 （毫秒） 之前，可以經過應用程式事項，以及要執行的作業在喚醒。
下列程式碼提供的範例是需要在半小時之間喚醒, 以及從視窗設定時間開始的一小時:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

若要在確切的時間繼續喚醒應用程式, `SetExact`請使用, 傳入應用程式應該喚醒的確切時間, 以及要執行的作業:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat 不再讓您設定完全重複的警示。 使用的應用程式[`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*)
而且需要確切的警示才能正常工作, 現在需要手動觸發每個警示。

### <a name="external-storage"></a>外部儲存體

外部儲存體現在分成兩種類型: 您的應用程式特有的儲存體, 以及多個應用程式所共用的資料。 讀取和寫入您的應用程式在外部儲存體上的特定位置不需要任何特殊許可權。 與共享儲存體上的資料互動現在需要`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`許可權。 這兩種類型可以分類為:

-  如果您是藉由呼叫上`Context`的方法取得檔案或目錄路徑-例如,[`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*)
   或[`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs)
   - 您的應用程式不需要額外的許可權。

-  如果您是藉由存取屬性或在上`Environment`呼叫方法來取得檔案或目錄路徑, 例如[`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory)
   或[`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*)
   , 您的`READ_EXTERNAL_STORAGE`應用程式需要`WRITE_EXTERNAL_STORAGE`或許可權。

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE``READ_EXTERNAL_STORAGE`意指許可權, 因此您應該只需要設定一個許可權。

### <a name="sms-consolidation"></a>SMS 匯總

KitKat 會將使用者所選取之一個預設應用程式中的所有 SMS 內容匯總在一起, 以簡化使用者的訊息處理。 開發人員會負責將應用程式設為預設的訊息應用程式, 並在程式碼中適當地運作, 如果未選取應用程式, 則會在生命中正常運作。 如需將您的 SMS 應用程式轉換為 KitKat 的詳細資訊, 請參閱 Google 的[準備好要 KitKat 的 Sms 應用程式](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html)指南。

### <a name="webview-apps"></a>Web 視圖應用程式

[Web](xref:Android.Webkit.WebView)工作的 KitKat 中有改造。 最大的變更是新增將內容載入至`WebView`的安全性。 雖然大部分以舊版 API 為目標的應用程式應該如預期般運作, 但`WebView`強烈建議使用類別的測試應用程式。 如需受影響的 Web 工作應用程式開發介面的詳細資訊, 請參閱 android 4.4 檔中的 Android[遷移至 web](https://developer.android.com/guide/webapps/migrating.html)工作。

<a name="user_experience" />

## <a name="user-experience"></a>使用者經驗

KitKat 隨附數個新的 Api 來增強使用者體驗, 包括用來處理屬性動畫的新轉換架構, 以及主題的透明 UI 選項。 這些變更涵蓋如下。

### <a name="transition-framework"></a>轉換架構

轉換架構可讓動畫更容易執行。 KitKat 可讓您只需一行程式碼就能執行簡單的屬性動畫, 或使用*場景*來自訂轉換。

#### <a name="simple-property-animation"></a>簡單屬性動畫

新的 Android 轉換程式庫簡化了屬性動畫背後的程式碼後置。 此架構可讓您以最少的程式碼執行簡單的動畫。 例如, 下列程式碼範例會使用[`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*)
若要以動畫顯示和`TextView`隱藏:

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

上述範例使用轉換架構, 在變更的屬性值之間建立自動的預設轉換。 因為動畫是由一行程式碼處理, 所以您可以輕鬆地使用舊版的 Android 進行相容性, 方法是在系統`BeginDelayedTransition`版本檢查中包裝呼叫。 如需詳細資訊, 請參閱將[您的應用程式遷移至 KitKat](#Migrating_Your_App_to_KitKat)一節。

下列螢幕擷取畫面顯示動畫前的應用程式:

[![動畫開始前的應用程式螢幕擷取畫面](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

下列螢幕擷取畫面顯示動畫之後的應用程式:

[![動畫完成後的應用程式螢幕擷取畫面](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

您可以使用幕後取得更多的轉換控制權, 如下一節所述。

#### <a name="android-scenes"></a>Android 場景

[幕後](xref:Android.Transitions.Scene)已引進為轉換架構的一部分, 讓開發人員能夠更充分掌控動畫。 場景會在 UI 中建立動態區域: 您為容器內的 XML 內容指定一個容器和數個版本或「場景」, 而 Android 則會執行其餘工作, 以動畫顯示場景之間的轉換。 Android 場景可讓您以最少的開發端工作來建立複雜的動畫。

裝載動態內容的靜態 UI 元素稱為*容器*或*場景基底*。 下列範例會使用 Android Designer 來建立名`RelativeLayout` `container`為的:

[![使用 Android Designer 建立 RelativeLayout 容器](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

範例配置也會在下方定義名`sceneButton`為的`container`按鈕。 此按鈕將會觸發轉換。

容器內的動態內容需要兩個新的 Android 版面配置。 這些版面配置只會指定容器*內*的程式碼。
下面的範例程式碼會定義名為*Scene1*的版面配置, 其中包含兩個文字欄位分別讀取「套件」和「Kat」, 另一個則稱為*Scene2*的第二個版面配置, 其中包含相同的文字欄位反轉。 XML 如下所示:

 **Scene1. axml**:

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

 **Scene2. axml**:

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

上述範例使用`merge` , 讓視圖程式碼更短, 並簡化視圖階層。 您可以在[這裡](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html)閱讀`merge`更多有關版面配置的資訊。

場景的建立方式是呼叫[`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*)、傳入容器物件、場景配置檔案的資源識別碼, 以及目前`Context`的, 如下列程式碼範例所示:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

按一下按鈕會在兩個場景之間切換, Android 會使用預設轉換值來進行動畫:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

下列螢幕擷取畫面說明動畫前面的場景:

[![動畫啟動前應用程式的螢幕擷取畫面](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

下列螢幕擷取畫面說明動畫後的場景:

[![動畫完成後的應用程式螢幕擷取畫面](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> Android 轉換程式庫中有[已知的 bug](https://code.google.com/p/android/issues/detail?id=62450) , 會導致使用`GetSceneForLayout`建立的場景在第二次流覽活動時中斷。 [這裡](http://www.doubleencore.com/2013/11/new-transitions-framework/)會說明 java 因應措施。


##### <a name="custom-transitions-in-scenes"></a>場景中的自訂轉換

自訂轉換可以在下`transition` `Resources`目錄中的 xml 資源檔中定義, 如下列螢幕擷取畫面所示:

[![資源/轉換目錄下轉換 .xml 檔案的位置](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

下列程式碼範例會定義以動畫呈現5秒的轉換, 並使用[下限的插](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html)轉程式:

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

轉換會使用[TransitionInflater](xref:Android.Transitions.TransitionInflater)在活動中建立, 如下列程式碼所示:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

然後, 新的轉換會新增至`Go`開始動畫的呼叫:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>半透明 UI

KitKat 可讓您更充分掌控應用程式與選擇性透明狀態和導覽列的主題。 您可以在用來定義 Android 主題的相同 XML 檔案中, 變更系統 UI 元素的半透明度。 KitKat 導入了下列屬性:

-  `windowTranslucentStatus`-設定為 true 時, 讓頂端狀態列變成半透明。

-  `windowTranslucentNavigation`-設定為 true 時, 讓底部導覽列變成半透明。

-  `fitsSystemWindows`-根據預設, 將頂端或底部列設定為 [transcluent] 會將內容移至透明 UI 元素下。 將這個屬性設定`true`為, 是防止內容與半透明系統 UI 元素重迭的簡單方式。


下列程式碼會定義具有半透明狀態和導覽列的主題:

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

下列螢幕擷取畫面顯示具有半透明狀態和導覽列的上方主題:

[![具有半透明狀態和導覽列之應用程式的範例螢幕擷取畫面](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>使用者內容

### <a name="storage-access-framework"></a>儲存體-存取架構

儲存體存取架構 (SAF) 是一種新的方式, 可讓使用者與影像、影片和檔等儲存的內容進行互動。 KitKat 會開啟新的 UI, 讓使用者能夠在一個匯總位置存取其資料, 而不是向使用者呈現選擇應用程式來處理內容的對話方塊。 一旦選擇內容之後, 使用者會回到要求內容的應用程式, 而應用程式體驗會如往常般繼續。

這項變更在開發人員端需要兩個動作: 首先, 需要提供者內容的應用程式必須更新為新的要求內容方式。 第二, 將資料寫入的`ContentProvider`應用程式必須經過修改, 才能使用新的架構。 這兩種案例都取決於新的[`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
API。

#### <a name="documentsprovider"></a>DocumentsProvider

在 KitKat 中, 與`ContentProviders`的互動會`DocumentsProvider`使用類別進行抽象化。 這表示 SAF 不在意資料實際的位置, 只要可以透過`DocumentsProvider` API 存取即可。 本機提供者、雲端服務和外部存放裝置全都使用相同的介面, 並以相同的方式處理, 提供使用者和開發人員一個可與使用者內容互動的位置。

本節涵蓋如何使用儲存體存取架構來載入和儲存內容。

#### <a name="request-content-from-a-provider"></a>從提供者要求內容

我們可以告訴 KitKat, 我們想要使用 SAF UI 與`ActionOpenDocument`意圖來挑選內容, 這表示我們想要連接到裝置可用的所有內容提供者。 您可以藉由指定`CategoryOpenable`來對此意圖新增一些篩選, 這表示只會傳回可開啟的內容 (也就是可存取的可用內容)。 KitKat 也可讓您使用`MimeType`篩選內容。 例如, 下列程式碼會藉由指定映射`MimeType`來篩選影像結果:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

呼叫`StartActivityForResult`會啟動 SAF UI, 然後使用者可以流覽來選擇影像:

[![使用儲存體存取架構流覽至影像的應用程式的範例螢幕擷取畫面](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

在使用者選擇影像之後, `OnActivityResult` `Android.Net.Uri`會傳回所選檔案的。 下列程式碼範例會顯示使用者的影像選取範圍:

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

#### <a name="write-content-to-a-provider"></a>將內容寫入提供者

除了從 SAF UI 載入內容之外, KitKat 也可讓您將內容儲存至任何`ContentProvider`可執行`DocumentProvider` API 的。 儲存內容會將`Intent`與`ActionCreateDocument`搭配使用:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

上述程式碼範例會載入 SAF UI, 讓使用者變更檔案名, 並選取目錄來存放新檔案:

[![使用者在下載目錄中將檔案名變更為 NewDoc 的螢幕擷取畫面](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

當使用者按下  [儲存`OnActivityResult` ] 時, `Android.Net.Uri`會傳遞新建立之檔案的 (可透過存取`data.Data`)。 Uri 可以用來將資料串流至新檔案:

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

請注意,[`ContentResolver.OpenOutputStream(Android.Net.Uri)`](xref:Android.Content.ContentResolver.OpenOutputStream*)
會傳回`System.IO.Stream`, 因此可以在 .net 中撰寫整個串流處理常式。

如需有關使用儲存體存取架構來載入、建立和編輯內容的詳細資訊, 請參閱[適用于儲存體存取架構的 Android 檔](https://developer.android.com/guide/topics/providers/document-provider.html)。

### <a name="printing"></a>列印

隨著[列印服務](xref:Android.PrintServices)和`PrintManager`的引進, KitKat 中的列印內容已經過簡化。 KitKat 也是第一個 API 版本, 可使用[Google 雲端列印應用程式](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint)完全利用[Google 的雲端列印服務 api](https://developers.google.com/cloud-print/) 。
隨附于 KitKat 的大部分裝置會在第一次連線到 WiFi 時, 自動下載 Google 雲端列印應用程式和[HP 列印服務外掛程式](https://play.google.com/store/apps/details?id=com.hp.android.printservice)。 使用者可以藉由流覽至 [設定] [>] [**系統 > 列印**] 來檢查其裝置的列印設定:

[![[列印設定] 畫面的範例螢幕擷取畫面](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> 雖然列印應用程式開發介面預設會設定為使用 Google Cloud 列印, 但 Android 仍可讓開發人員使用新的 Api 來準備列印內容, 並將它傳送給其他應用程式來處理列印。



#### <a name="printing-html-content"></a>列印 HTML 內容

KitKat 會自動使用[`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter) `WebView.CreatePrintDocumentAdapter`建立 web view 的。 列印 web 內容是在等候 HTML 內容載入[`WebViewClient`](xref:Android.Webkit.WebViewClient) , 並讓活動知道在 [選項] 功能表中提供 [列印] 選項, 以及 [活動] (等待使用者選取列印選項並呼叫) 的協調工作。`Print`在`PrintManager`上。 本節涵蓋列印螢幕上 HTML 內容所需的基本設定。

請注意, 載入和列印 web 內容需要「網際網路」許可權:

[![在應用程式選項中設定網際網路許可權](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>列印功能表項目

[列印] 選項通常會出現在活動的 [[選項] 功能表](https://developer.android.com/guide/topics/ui/menus.html#options-menu)中。
[選項] 功能表可讓使用者對活動執行動作。 它位於畫面的右上角, 看起來像這樣:

[![顯示在畫面右上角的 [列印] 功能表項目的範例螢幕擷取畫面](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


其他功能表項目可以在定義 *功能表* 目錄下 *資源*。 下列程式碼會定義名為[Print](xref:Android.Print.PrintManager)的範例功能表項目:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

與活動中的 [選項] 功能表互動會透過`OnCreateOptionsMenu`和`OnOptionsItemSelected`方法進行。
`OnCreateOptionsMenu` 可供新增新的功能表項目，例如 [列印] 選項中，從 *功能表* 資源目錄。
`OnOptionsItemSelected`接聽使用者從功能表中選取 [列印] 選項, 並開始列印:

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

上述程式碼也會定義名`dataLoaded`為的變數, 以追蹤 HTML 內容的狀態。 當所有內容都已載入時,會將此變數設定為true,讓活動知道要將[列印]功能表項目新增至[選項]功能表。`WebViewClient`

##### <a name="webviewclient"></a>WebViewClient

的工作`WebViewClient`是確保`WebView`中的資料在 [列印] 選項出現在功能表中之前完全載入, 它會使用`OnPageFinished`方法來執行。 `OnPageFinished`會接聽 web 內容以完成載入, 並告知活動重新建立其 [選項] 功能表`InvalidateOptionsMenu`:

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

`OnPageFinished`也會將`dataLoaded`值設定`true`為, `OnCreateOptionsMenu`因此可以就地重新建立具有列印選項的功能表。

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

`Print`當做引數使用: 列印工作的名稱 (在此範例中為 "MyWebPage")、a[`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)
這會從內容產生列印檔案, 而[`PrintAttributes`](xref:Android.Print.PrintAttributes)
(`null`在上述範例中)。 您可以指定`PrintAttributes` , 協助在列印的頁面上配置內容, 雖然預設屬性應該處理大部分的案例。

呼叫`Print`會載入列印 UI, 其中會列出列印工作的選項。 UI 可讓使用者選擇將 HTML 內容列印或儲存至 PDF, 如下列螢幕擷取畫面所示:

[![顯示 [列印] 功能表的 PrintHtmlActivity 螢幕擷取畫面](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![顯示 [另存新檔] PDF 功能表的 PrintHtmlActivity 螢幕擷取畫面](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>硬體

KitKat 會新增數個 Api 以配合新的裝置功能。 其中最值得注意的是以主機為基礎的卡片模擬和`SensorManager`新的。

### <a name="host-based-card-emulation-in-nfc"></a>NFC 中以主機為基礎的卡片模擬

以主機為基礎的卡片模擬 (HCE) 可讓應用程式在不依賴電訊廠商專屬安全元素的情況下, 行為像是 NFC 卡或 NFC 記憶卡讀卡機。 在設定 HCE 之前, 請確定裝置上有 HCE 可供`PackageManager.HasSystemFeature`使用:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE 需要在應用程式的`Nfc` `AndroidManifest.xml`中註冊 HCE 功能和許可權:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![在應用程式選項中設定 NFC 許可權](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

若要運作, HCE 必須能夠在背景中執行, 而且必須在使用者進行 NFC 交易時啟動, 即使使用 HCE 的應用程式不在執行中也一樣。 我們可以撰寫 HCE 程式碼做為來完成這`Service`項工作。 HCE 服務`HostApduService`會執行介面, 它會實作為下列方法:

-  *ProcessCommandApdu* -應用程式通訊協定資料單位 (APDU) 是 NFC 讀取器與 HCE 服務之間傳送的內容。 這個方法會使用讀取器中的 ADPU, 並傳回資料單位以回應。

-  *OnDeactivated* - `HostAdpuService`當 HCE 服務不再與 NFC 讀取器通訊時, 會停用。


HCE 服務也必須向應用程式的資訊清單註冊, 並以適當的許可權、意圖篩選和中繼資料裝飾。 下列程式碼是`HostApduService` `Service`使用屬性向 Android 資訊清單註冊的範例 (如需有關屬性的詳細資訊, 請參閱 Xamarin[使用 Android 資訊清單](~/android/platform/android-manifest.md)指南):

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

上述服務提供一種方式讓 NFC 讀取器與應用程式互動, 但 NFC 讀取器仍無法得知此服務是否正在模擬需要掃描的 NFC 卡。 為了協助 NFC 讀者識別服務, 我們可以為服務指派一個唯一的*應用程式識別碼 (輔助)* 。 我們會在以`MetaData`屬性註冊的 xml 資源檔中, 指定協助, 以及與 HCE 服務相關的其他中繼資料 (請參閱上述程式碼範例)。 此資源檔會指定一個或多個輔助篩選器, 這是十六進位格式的唯一識別碼字串, 其對應于一或多個 NFC 讀取器裝置的輔助:

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

除了協助篩選之外, xml 資源檔也會提供 HCE 服務的使用者面向描述、指定輔助群組 (付款應用程式與「其他」), 而在付款應用程式中, 會向使用者顯示 260x96 dp 橫幅。

上面所述的設定為模擬 NFC 卡的應用程式提供基本的基礎構件。 NFC 本身需要執行數個步驟, 並進一步測試以進行設定。 如需以主機為基礎的卡片模擬的詳細資訊, 請參閱[Android 檔入口網站](https://developer.android.com/guide/topics/connectivity/nfc/hce.html)。
如需使用 NFC 搭配 Xamarin 的詳細資訊, 請參閱[XAMARIN NFC 範例](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample)。

### <a name="sensors"></a>感應器

KitKat 透過[`SensorManager`](xref:Android.Hardware.SensorManager)提供裝置感應器的存取權。
`SensorManager`可讓 OS 以批次方式排程將感應器資訊傳遞給應用程式, 以保留電池壽命。

KitKat 也隨附兩個新的感應器類型來追蹤使用者的步驟。 這些是以加速計為基礎, 其中包括:

-  *StepDetector* -當使用者採取步驟時, 應用程式會收到通知/喚醒, 而偵測器會提供步驟發生時的時間值。

-  *StepCounter* -記錄在*下一次裝置重新開機之前*, 使用者從感應器註冊以來所採取的步驟數目。

下列螢幕擷取畫面說明作用中的步驟計數器:

[![顯示步驟計數器的 SensorsActivity 應用程式螢幕擷取畫面](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

您可以藉`SensorManager`由呼叫`GetSystemService(SensorService)`並將結果`SensorManager`轉換為, 來建立。 若要使用 step 計數器, 請`GetDefaultSensor` `SensorManager`在上呼叫。 您可以註冊感應器, 並使用的協助來接聽步驟計數的變更[`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener)
介面, 如下列程式碼範例所示:

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

`OnSensorChanged`如果當應用程式在前景時更新步驟計數, 則會呼叫。 如果應用程式進入背景, 或裝置處於睡眠狀態, `OnSensorChanged`則不會呼叫; 不過, 在呼叫之前`UnregisterListener` , 這些步驟仍會繼續計算。

請記住,*步驟計數值在註冊感應器的所有應用程式中都是累計*的。 這表示即使您卸載並重新安裝應用程式, 以及在應用程式`count`啟動時將變數初始化為 0, 感應器所報告的值仍會保留在註冊感應器時所採取的步驟總數, 而不論您的應用程式或其他。 您可以藉由`UnregisterListener` `SensorManager`在上呼叫來防止應用程式新增至步驟計數器, 如下列程式碼所示:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

重新開機裝置會將步驟計數重設為0。 您的應用程式將需要額外的程式碼, 以確保它會針對應用程式報告精確的計數, 而不論使用感應器的其他應用程式或裝置的狀態為何。


> [!NOTE]
> 雖然步驟偵測和計算的 API 隨附于 KitKat, 但並非所有的電話都會與感應器科系。 您可以藉由`PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`執行來檢查感應器是否可用, 或檢查以確定傳回的`GetDefaultSensor`值不`null`是。


<a name="developer_tools" />

## <a name="developer-tools"></a>開發人員工具

### <a name="screen-recording"></a>螢幕錄製

KitKat 包含新的螢幕錄製功能, 讓開發人員可以記錄應用程式的實際運作。 螢幕錄製可透過[Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html)用戶端取得, 這可在 Android SDK 中下載。

若要錄製您的畫面, 請連接您的裝置;然後, 找出您的 Android SDK 安裝, 流覽至 [**平臺工具**] 目錄, 然後執行**adb**用戶端:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

上述命令會將預設的3分鐘影片記錄為預設的4Mbps 解析度。 若要編輯長度, 請新增 *--時間限制*旗標。
若要變更解析度, 請新增 *--位速率*旗標。 下列命令會在8Mbps 記錄一段分鐘的影片:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

您可以在裝置上找到您的影片-錄製完成時, 它會出現在您的資源庫中。


## <a name="other-kitkat-additions"></a>其他 KitKat 新增專案

除了上面所述的變更之外, KitKat 還可讓您:

-  *使用全螢幕*KitKat 導入了新的[沉浸式模式](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int))來流覽內容、播放遊戲, 以及執行其他可從全螢幕體驗獲益的應用程式。

-  *自訂通知*-使用來取得系統通知的其他詳細資料[`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService)
   。 這可讓您以不同的方式呈現應用程式內的資訊。

-  *鏡像的可以繪製資源*-繪製資源有新的[`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
   屬性, 告訴系統針對需要從左至右配置翻轉的影像建立鏡像版本。

-  *暫停動畫*-暫停和繼續使用建立的動畫[`Animator`](xref:Android.Animation.Animator)
   類別的新執行個體。

-  *閱讀動態變更文字*-代表 UI 的部分, 以新的文字動態更新為「即時區域」, 新的[`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
   屬性, 以便在協助工具模式中自動讀取新的文字。

-  *增強音訊體驗*-讓追蹤更大[`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer)
   , 使用, 尋找音訊串流的尖峰和 RMS[`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms)
   類別, 並從[音訊時間戳記](xref:Android.Media.AudioTimestamp)取得資訊, 以協助進行音訊影片同步處理。

-  *在自訂間隔同步處理 ContentResolver* -KitKat 會在執行同步處理要求的時間內新增一些變動性。 藉由呼叫`ContentResolver.RequestSync`並傳入, 在`SyncRequest`自訂時間或間隔同步。 `ContentResolver`

-  *區分控制器*-在 KitKat 中, 控制器會被指派可透過裝置的`ControllerNumber`屬性存取的唯一整數識別碼。 這可讓您更輕鬆地分辨遊戲中的玩家。

-  *遠端控制*-只有硬體和軟體端的一些變更, KitKat 可讓您使用紅外線發送器將裝置科系轉換成遠端控制`ConsumerIrService`, 並透過新的來與週邊裝置互動[`RemoteController`](xref:Android.Media.RemoteController)
   Api.

如需上述 API 變更的詳細資訊, 請參閱 Google [Android 4.4 api](https://developer.android.com/about/versions/android-4.4.html)總覽。


## <a name="summary"></a>總結

本文介紹 Android 4.4 (API 層級 19) 中提供的一些新 Api, 並涵蓋將應用程式轉換為 KitKat 時的最佳作法。 其中概述影響使用者體驗的 Api 變更, 包括*轉換架構*和*主題*的新選項。 接下來, 它引進了*儲存體存取架構*和`DocumentsProvider`類別, 以及新的*列印 api*。 它探索*NFC 主機型卡片模擬*, 以及如何使用*低電源感應器*, 包括兩個新的感應器來追蹤使用者的步驟。 最後, 它會示範如何使用*螢幕錄製*來捕獲應用程式的即時示範, 並提供 KitKat API 變更和新增專案的詳細清單。


## <a name="related-links"></a>相關連結

- [KitKat 範例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/kitkat)
- [Android 4.4 Api](https://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](https://developer.android.com/about/versions/kitkat.html)
