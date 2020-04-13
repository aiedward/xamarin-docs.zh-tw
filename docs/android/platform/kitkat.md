---
title: KitKat 功能
description: Android 4.4 (KitKat) 為使用者和開發人員配備了一系列功能。 本指南重點介紹了其中幾個功能,並提供了代碼示例和實現詳細資訊,以説明您充分利用 KitKat。
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 43061272f3d3486926f38af792ee3b9df0c53670
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027234"
---
# <a name="kitkat-features"></a>KitKat 功能

_Android 4.4 (KitKat) 為使用者和開發人員配備了一系列功能。本指南重點介紹了其中幾個功能,並提供了代碼示例和實現詳細資訊,以説明您充分利用 KitKat。_

## <a name="overview"></a>概觀

Android 4.4(API 級別 19),也稱為"KitKat",於 2013 年末發佈。 KitKat 提供各種新功能和改進,包括:

- [用戶體驗](#user_experience)&ndash;輕鬆動畫,具有過渡框架、半透明狀態和導航欄以及全屏沉浸式模式,有助於為使用者提供更好的體驗。

- [使用者內容](#user_content)&ndash;使用者檔案管理簡化與存儲存取框架;通過改進的列印 API,列印圖片、網站和其他內容變得更加容易。

- [硬體](#hardware)&ndash;將任何應用轉換為 NFC 卡,具有基於 NFC 主機的卡模擬功能;使用執行低功耗的`SensorManager`感測器 。

- [開發人員工具](#developer_tools)&ndash;篩選應用程式在操作與Android調試橋客戶端,作為Android SDK的一部分提供。

本指南提供了將現有 Xamarin.Android 應用程式遷移到 KitKat 的指導,以及針對 Xamarin.Android 開發人員的 KitKat 的高級概述。

## <a name="requirements"></a>需求

要使用 KitKat 開發 Xamarin.Android 應用程式,您需要*Xamarin.Android 4.11.0*或更高版本,以及透過 Android SDK 管理器安裝的 Android 4.4(API 19),如下圖所示:

[![在 Android SDK 管理器中選擇 Android 4.4](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>將應用移至 KitKat

本節提供一些第一回應專案,以説明將現有應用程序轉換為 Android 4.4。

### <a name="check-system-version"></a>檢查系統版本

如果應用程式需要與舊版本的 Android 相容,請確保在系統版本檢查中包裝任何特定於 KitKat 的代碼,如下代碼範例所示:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>報警處理

Android 使用報警服務在指定時間喚醒後台的應用。 KitKat 通過批處理警報來保留電源,從而更進一步。 這意味著,KitKat 更願意將註冊為在同一時間間隔內喚醒的多個應用程式分組,並同時喚醒它們,而不是準確喚醒每個應用。
要告訴 Android 在指定的時間間隔內喚醒應用`SetWindow`,請[`AlarmManager`](xref:Android.App.AlarmManager)調用 以最小和最大時間(以毫秒為單位)調用可在喚醒應用之前經過的時間,並在喚醒時執行操作。
以下代碼提供了一個應用程式的範例,該應用程式需要在設定視窗時半小時後喚醒:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

要在確切的時間繼續喚醒應用,請使用`SetExact`, 傳入應喚醒應用的確切時間,以及執行的操作:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat 不再允許您設置精確的重複報警。 使用的應用程式[`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*)
並且需要精確的報警才能工作,現在需要手動觸發每個警報。

### <a name="external-storage"></a>外部儲存體

外部儲存現在分為兩種類型 - 應用程式獨有的存儲和由多個應用程式共享的數據。 讀取和寫入應用在外部存儲上的特定位置不需要特殊許可權。 現在,與共用存儲上的數據交互需要`READ_EXTERNAL_STORAGE``WRITE_EXTERNAL_STORAGE`或許可權。 可以這樣分類這兩種類型:

- 如果您通過調用方法獲取檔或目錄路徑`Context`- 例如,[`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*)
  或[`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs)
  - 你的應用不需要額外的許可權。

- 如果透過存取屬性或在`Environment`上 呼叫方法(如[`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory)
  或[`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*)
  ,你的應用需要`READ_EXTERNAL_STORAGE``WRITE_EXTERNAL_STORAGE`或許可權。

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE`表示`READ_EXTERNAL_STORAGE`許可權,因此您只需要設置一個許可權。

### <a name="sms-consolidation"></a>短信整合

KitKat 透過聚合使用者選擇的一個預設應用程式中的所有 SMS 內容來簡化使用者的消息傳遞。 開發人員負責使應用可選擇為預設訊息傳遞應用程式,並在未選擇應用程式時在代碼和生命週期中行為得當。 有關將 SMS 應用轉換為 KitKat 的詳細資訊,請參閱 Google 的「[為 KitKat 做好準備」簡訊應用](https://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html)指南。

### <a name="webview-apps"></a>網頁檢視應用

[WebView](xref:Android.Webkit.WebView)在 KitKat 中做了一個改造。 最大的更改是增加了將內容載入到的`WebView`安全措施。 雖然大多數面向較舊 API 版本的應用程式應按預期工作,但強烈建議`WebView`使用該 類的測試應用程式。 有關受影響的 WebView API 的詳細資訊,請參閱 Android 4.4 文檔中的 Android[遷移到 WebView。](https://developer.android.com/guide/webapps/migrating.html)

<a name="user_experience" />

## <a name="user-experience"></a>使用者經驗

KitKat 附帶了幾個新的 API 來增強使用者體驗,包括用於處理屬性動畫的新過渡框架和用於美選的半透明 UI 選項。 這些更改如下所述。

### <a name="transition-framework"></a>過渡框架

過渡框架使動畫更易於實現。 KitKat 允許您僅使用一行代碼執行簡單的屬性動畫,或使用*場景*自定義過渡。

#### <a name="simple-property-animation"></a>簡單屬性動畫

新的 Android 轉換庫簡化了屬性動畫背後的代碼。 該框架允許您使用最少的代碼執行簡單的動畫。 例如,以下代碼範例使用[`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*)
顯示與隱藏`TextView`設定動畫:

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

上面的範例使用過渡框架在不斷變化的屬性值之間創建自動預設轉換。 由於動畫由一行代碼處理,因此您可以通過在系統版本檢查中`BeginDelayedTransition`包裝調用,輕鬆使此版本與舊版本的 Android 相容。 有關詳細資訊[,請參閱將應用遷移到 KitKat](#Migrating_Your_App_to_KitKat)部分。

下面的螢幕截圖顯示了動畫之前的應用程式:

[![動畫啟動前的應用程式螢幕擷取](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

下面的螢幕截圖顯示了動畫後的應用程式:

[![動畫完成後的應用程式螢幕擷取](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

您可以使用"場景"對過渡進行更多控制,下一節將介紹這些場景。

#### <a name="android-scenes"></a>安卓場景

[場景](xref:Android.Transitions.Scene)作為過渡框架的一部分引入,以便開發人員對動畫進行更多控制。 場景在 UI 中創建一個動態區域:為容器內的 XML 內容指定一個容器和多個版本或"場景",而 Android 則執行其餘工作,為場景之間的過渡設置動畫。 Android 場景可讓您在開發方面以最少的工作構建複雜的動畫。

包含動態內容的靜態 UI 元素稱為*容器*或*場景基*。 下面的範例使用 Android 設計器`RelativeLayout`建立`container`一 個稱為 :

[![使用 Android 設計器建立相對佈局容器](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

範例佈局來定義按鈕,`sceneButton``container`這個按鈕在下方稱為 。 此按鈕將觸發轉換。

容器內的動態內容需要兩個新的 Android 佈局。 這些佈局僅指定容器*內*的代碼。
下面的範例代碼定義了名為*Scene1*的佈局,該佈局包含兩個分別讀取"Kit"和"Kat"的文本欄位,以及一個稱為*Scene2*的第二個佈局,該佈局包含反轉的相同文本字段。 XML 如下所示:

 **場景1.axml**:

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

 **場景2.axml**:

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

上面的範例用於`merge`縮短檢視代碼並簡化視圖層次結構。 您可以在此處閱讀有關`merge`佈局的更多[內容](https://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html)。

場景是通過調用[`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*)、傳入容器物件、場景佈局檔的資源 ID`Context`和當前 ()創建的,如下代碼示例所示:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

按下按鈕在兩個場景之間翻轉,Android 使用預設過渡值進行動畫處理:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

下面的螢幕截圖說明瞭動畫之前的場景:

[![動畫啟動前套用螢幕截圖](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

下面的屏幕截圖說明瞭動畫后的場景:

[![動畫完成後套用螢幕截圖](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)

> [!NOTE]
> Android 過渡庫中存在一個[已知 Bug,](https://code.google.com/p/android/issues/detail?id=62450)`GetSceneForLayout`該 Bug 會導致 當使用者第二次瀏覽活動時,使用的場景中斷。 [此處](http://www.doubleencore.com/2013/11/new-transitions-framework/)描述了 java 解決方法。

##### <a name="custom-transitions-in-scenes"></a>場景中的自訂過渡

可以在 下面`transition``Resources`的 目錄中的 xml 資源檔中定義自訂轉換,如下圖所示:

[![資源/轉換目錄下的轉換.xml 檔案的位置](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

以下代碼範例定義一個過渡,該過渡為 5 秒的動畫設定,並使用[過沖插值器](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

轉換使用[轉換在](xref:Android.Transitions.TransitionInflater)作用中建立,如下代碼所示:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

然後,`Go`新轉換將新增到開始動畫的呼叫中:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>半透明 UI

KitKat 為您提供了使用可選半透明狀態和導航列對應用進行設置的更多控制。 您可以更改用於定義 Android 主題的同一 XML 檔案中的系統 UI 元素的半透明性。 KitKat 引入以下屬性:

- `windowTranslucentStatus`- 設定為 true 時,使頂部狀態列半透明。

- `windowTranslucentNavigation`- 設定為 true 時,使底部導航欄半透明。

- `fitsSystemWindows`- 預設情況下,將頂部或底部列設置為在透明 UI 元素下轉移內容。 將此屬性設置為`true`防止內容與半透明系統 UI 元素重疊的一種簡單方法。

以下代碼定義具有半透明狀態和導航列的主題:

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

下面的螢幕截圖顯示了上面帶有半透明狀態和導航列的主題:

[![具有半透明狀態和導覽列的應用程式範例螢幕擷取](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>使用者內容

### <a name="storage-access-framework"></a>儲存-存取框架

儲存存取框架 (SAF) 是使用者與儲存的內容(如影像、視訊和文件)進行互動的新方式。 KitKat 不向使用者提供對話方塊來選擇處理內容的應用程式,而是打開一個新的 UI,允許使用者在一個聚合位置訪問其數據。 選擇內容後,使用者將返回到請求內容的應用程式,應用體驗將照常進行。

此更改需要在開發人員方面採取兩個操作:首先,需要從供應商處獲得內容的應用需要更新為請求內容的新方式。 其次,`ContentProvider`需要修改將資料寫入應用程式才能使用新框架。 這兩種情況都取決於新的[`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
API。

#### <a name="documentsprovider"></a>文件提供者

在 KitKat`ContentProviders`中, 與`DocumentsProvider`類的交互 被抽象化。 這意味著 SAF 不關心數據的物理位置,只要`DocumentsProvider`數據可以通過 API 訪問。 本地提供者、雲端服務和外部儲存裝置都使用相同的介面,並且以相同的方式對待,為使用者提供和開發人員一個與使用者內容互動的位置。

本節介紹如何使用存儲存取框架載入和保存內容。

#### <a name="request-content-from-a-provider"></a>提供者要求內容

我們可以告訴 KitKat,我們希望使用帶`ActionOpenDocument`有 意圖的 SAF UI 來選取內容,這意味著我們希望連接到設備可用的所有內容供應商。 您可以通過`CategoryOpenable`指定 向此意圖添加一些篩選,這意味著僅返回可以打開的內容(即可訪問、可用的內容)。 KitKat 還允許使用`MimeType`篩選內容 。 例如,下面的代碼通過指定圖像`MimeType`來篩選影像結果:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

呼叫`StartActivityForResult`將啟動 SAF UI,然後使用者可以瀏覽該 UI 來選擇影像:

[![使用儲存存取框架瀏覽影像的應用程式範例](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

使用者選擇影像後,`OnActivityResult`傳回選取的檔案`Android.Net.Uri`的 。 下面的代碼範例顯示使用者的影像選擇:

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

除了從 SAF UI 載入內容外,KitKat 還允許您`ContentProvider`將內容`DocumentProvider`保存到 任何實現 API 的內容。 儲存內容使用`Intent` `ActionCreateDocument` :

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

上述代碼範例載入 SAF UI,允許使用者更改檔案名稱並選擇目錄來容納新檔案:

[![使用者在下載目錄中將檔案名稱變更為 NewDoc 的螢幕截圖](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

當使用者按 **「儲存」**`OnActivityResult`時,`Android.Net.Uri`將傳遞新的建立的檔案,該檔案`data.Data`可以使用存取 。 uri 可用於將資料串流式傳輸到新檔案中:

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
返回`System.IO.Stream`a ,因此整個流過程可以在 .NET 中寫入。

有關使用儲存存取框架載入、建立及編輯內容的詳細資訊,請參閱[儲存存取框架的 Android 文件](https://developer.android.com/guide/topics/providers/document-provider.html)。

### <a name="printing"></a>列印

在 KitKat 中引入了[列印服務和](xref:Android.PrintServices)`PrintManager`,列印內容得到了簡化。 KitKat 也是第一個使用[Google 雲列印應用程式](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint)充分利用[Google 雲列印服務 API 的](https://developers.google.com/cloud-print/)API 版本。
大多數隨套件提供的裝置在首次連接到 WiFi 時會自動下載 Google 雲端列印應用和[HP 列印服務外掛程式](https://play.google.com/store/apps/details?id=com.hp.android.printservice)。 使用者可以通過導航到 **"設置>系統>列印**來檢查其設備的列印設置:

[![列印設定螢幕的範例螢幕擷取](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)

> [!NOTE]
> 儘管默認情況下將列印 API 設定為與 Google 雲端列印配合使用,但 Android 仍允許開發人員使用新的 API 準備列印內容,並將其發送到其他應用程式來處理列印。

#### <a name="printing-html-content"></a>列印 HTML 內容

KitKat 會自動[`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)為`WebView.CreatePrintDocumentAdapter`具有的 Web 檢視建立一個檢視。 列印 Web 內容是等待[`WebViewClient`](xref:Android.Webkit.WebViewClient)載入 HTML 內容並讓活動知道以使列印選項在選項選單中可用的和「活動」(等待使用者選擇」列印選項`Print`並在`PrintManager`上調用 ) 之間的協調工作。 本節介紹列印螢幕上 HTML 內容所需的基本設置。

請注意,載入與列印 Web 內容需要 Internet 權限:

[![在應用程式選項中設定 Internet 權限](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>列印選單項目

列印選項通常將顯示在活動[的選項功能表](https://developer.android.com/guide/topics/ui/menus.html#options-menu)中。
選項功能表允許使用者對活動執行操作。 它位於螢幕的右上角,如下所示:

[![螢幕右上角顯示的列印選單項目的範例螢幕擷圖](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)

其他功能表項可以在*參考資料*下的*菜單*目錄中定義。 下面的代碼定義了名為[「列印](xref:Android.Print.PrintManager)」的範例選單項:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

通過`OnCreateOptionsMenu`和`OnOptionsItemSelected`方法與 活動中的選項功能表進行互動。
`OnCreateOptionsMenu`是從*菜單*資源目錄中添加新功能表項(如"列印"選項)的地方。
`OnOptionsItemSelected`偵聽使用者從選單中選擇「列印」選項,並開始列印:

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

上面的代碼還定義了一個變數,用於`dataLoaded`跟蹤 HTML 內容的狀態。 將`WebViewClient`此變數設置為 true,當所有內容都已載入時,因此"活動"知道將"列印"選單項添加到選項功能表中。

##### <a name="webviewclient"></a>WebView 用戶端

的作業`WebViewClient`是確保`WebView`在 列印選項顯示在功能表中之前完全載入 中的數據`OnPageFinished`,而使用方法執行該選項。 `OnPageFinished`偵聽 Web 內容以完成載入,並告訴作用使用 重新建立其`InvalidateOptionsMenu`選項選單 :

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

`OnPageFinished`還將`dataLoaded`該值設定`true`到`OnCreateOptionsMenu`, 因此可以使用「列印」選項重新創建選單。

##### <a name="printmanager"></a>PrintManager

以下代碼範例列印`WebView`: 的內容:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print`用作參數:列印工作的名稱(本範例為"MyWebPage"),一個[`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)
從內容產生列印文件,以及[`PrintAttributes`](xref:Android.Print.PrintAttributes)
(`null`在上面的示例中)。 您可以指定`PrintAttributes`以説明在列印頁上佈局內容,儘管默認屬性應處理大多數方案。

呼叫`Print`載印表 UI,其中列出了列印作業的選項。 UI 為使用者提供列印 HTML 內容或將 HTML 內容儲存到 PDF 的選項,如下圖所示:

[![顯示列印選單的列印Html活動螢幕擷取](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![顯示「儲存為 PDF」選單的 PrintHtmlActivity 截圖](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>硬體

KitKat 添加了多個 API 以適應新的設備功能。 其中最引人注目的是基於主機的卡模擬和新的`SensorManager`。

### <a name="host-based-card-emulation-in-nfc"></a>NFC 的主機的卡模擬

基於主機的卡仿真 (HCE) 允許應用像 NFC 卡或 NFC 讀卡機一樣,而無需依賴營運商專有的安全元件。 在設定 HCE 之前,請確保 HCE`PackageManager.HasSystemFeature`在裝置上 可用:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE 要求將 HCE`Nfc`功能與權限`AndroidManifest.xml`註冊到應用程式的 :

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![在應用程式選項中設定 NFC 權限](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

為了工作,HCE 必須能夠在後台運行,並且必須在使用者進行 NFC 事務時啟動,即使使用 HCE 的應用程式未運行也是如此。 我們可以通過將 HCE 代碼編寫為來`Service`實現此 目的。 HCE 服務實現`HostApduService`介面 ,實現以下方法:

- *ProcessCommandApdu* - 應用程式協定數據單元 (APDU) 是在 NFC 讀取器和 HCE 服務之間發送的內容。 此方法使用讀取器中的 ADPU,並返回回應中的數據單元。

- *已停用*`HostAdpuService`- 當 HCE 服務不再與 NFC 讀取器通信時,將停用。

HCE 服務還需要在應用程式的清單中註冊,並使用適當的許可權、意圖篩選器和元數據進行修飾。 以下代碼是使用`HostApduService``Service`屬性在 Android 清單中註冊的範例(有關屬性的詳細資訊,請參閱使用 Android[清單的](~/android/platform/android-manifest.md)Xamarin 指南):

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

上述服務為 NFC 讀取器提供了與應用程式交互的方法,但 NFC 讀卡器仍無法知道此服務是否正在類比需要掃描的 NFC 卡。 為了説明 NFC 讀取器識別服務,我們可以為服務分配唯一*的應用程式 ID (AID)。* 我們在與`MetaData`屬性一起註冊的 xml 資源檔中指定 AID 以及有關 HCE 服務的其他元數據(請參閱上面的代碼示例)。 此資源檔案指定一個或多個 AID 篩選器 - 十六進位格式的唯一識別元字串,對應於一個或多個 NFC 讀取器裝置的 AID:

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

除了 AID 篩選器外,xml 資源檔還提供面向使用者的 HCE 服務描述,指定 AID 組(付款應用程式與"其他"),在付款應用程式的情況下,還會向使用者顯示要顯示的 260x96 dp 橫幅。

上面概述的設置為類比 NFC 卡的應用程式提供了基本構建基塊。 NFC 本身還需要幾個步驟和進一步測試來配置。 有關基於主機的卡仿真的詳細資訊,請參閱[Android 文件門戶](https://developer.android.com/guide/topics/connectivity/nfc/hce.html)。
有關將 NFC 與 Xamarin 一起使用的詳細資訊,請檢視[Xamarin NFC 樣品](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample)。

### <a name="sensors"></a>感應器

KitKat 透過提供裝置偵測器[`SensorManager`](xref:Android.Hardware.SensorManager)的存取 。
允許`SensorManager`操作系統分批將感測器資訊傳送到應用程式,從而保留電池壽命。

KitKat 還附帶了兩種新的感測器類型,用於跟蹤使用者的步驟。 這些基於加速度計,包括:

- *步進檢測器*- 當使用者執行步驟時,應用程式會收到通知/喚醒,並且探測器為步驟發生的時間值提供時間值。

- *Step計數器*- 追蹤使用者自註冊感測器以來所採取的步驟數 *,直到下一個裝置重新啟動*。

下面的螢幕截圖描述了操作中的步進計數器:

[![顯示步進計數器的感應器活動應用的螢幕擷圖](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

可以透過呼`SensorManager``GetSystemService(SensorService)`叫 並將結果強制轉換`SensorManager`為, 可以建立 。 要使用步驟計數器,請呼叫`GetDefaultSensor` `SensorManager` 。 您可以註冊感測器,並在[`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener)
介面,如下代碼示例所示:

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

`OnSensorChanged`如果步驟計數在應用程式在前臺時更新,則調用。 如果應用程式進入後台,或設備處於睡眠狀態,將不會調用`OnSensorChanged`;否則,將不會調用但是,這些步驟將繼續計數,直到`UnregisterListener`調用。

請記住 *,步進計數值是註冊感測器的所有應用程式的累積*值。 這意味著,即使您卸載並重新安裝應用程式,並在應用程式啟動時在 0`count`初始化變數,感測器報告的值仍將是感測器註冊時所執行的步驟總數,無論是應用程式還是其他。 可以通過呼`UnregisterListener`叫 來防止應用程式添加到步驟計數`SensorManager`器, 如下代碼所示:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

重新啟動設備會將步長數重置為 0。 無論使用感測器或設備的狀態的其他應用程式如何,你的應用都將需要額外的代碼來確保它報告應用程式的準確計數。

> [!NOTE]
> 雖然用於步進檢測和計數的 API 附帶 KitKat,但並非所有手機都配備感測器。 您可以透過`PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`執行檢查器是否可以檢查, 也可以檢查`GetDefaultSensor`以確保傳`null`回的值不是 。

<a name="developer_tools" />

## <a name="developer-tools"></a>開發人員工具

### <a name="screen-recording"></a>螢幕錄製

KitKat 包括新的螢幕錄製功能,以便開發人員可以記錄應用程式。 屏幕錄製可透過 Android[除錯橋 (ADB)](https://developer.android.com/tools/help/adb.html)客戶端進行,該用戶端可作為 Android SDK 的一部分下載。

要錄製螢幕,請連接您的設備;然後,找到您的 Android SDK 安裝,導航到**平臺工具**目錄並運行**adb**用戶端:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

上述命令將以預設解析度為 4Mbps 的預設解析度錄製預設 3 分鐘影片。 要編輯長度,請添加 *--時間限制*標誌。
要更改解析度,添加 *-bit 速率*標誌。 以下命令將以 8Mbps 的速度錄製一分鐘長的影片:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

您可以在設備上找到視頻 - 錄製完成後,視頻將顯示在庫中。

## <a name="other-kitkat-additions"></a>其他 KitKat 新增功能

除了上述變更外,KitKat 還允許您:

- *使用全屏*- KitKat 引入了新的[沉浸式模式](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)),用於瀏覽內容、玩遊戲和運行其他應用程式,這些應用程式可能受益於全屏體驗。

- *自訂通知*- 使用[`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService)
  . 這允許您在應用內以不同的方式顯示資訊。

- *可繪製資源*- 可繪製資源具有新的[`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
  告訴系統為需要向左向右翻轉佈局的圖像創建鏡像版本的屬性。

- *暫停動畫*─ 暫停與暫停使用[`Animator`](xref:Android.Animation.Animator)
  類別的新執行個體。

- *閱讀動態變更的文字*- 表示使用新文字動態更新的 UI 部份,作為「即時區域」使用新文字[`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
  屬性,以便新文本將在輔助功能模式下自動讀取。

- *增強音效體驗*─ 使曲目更響亮[`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer)
  ,找到音頻流的峰值和 RMS,[`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms)
  類,並從[音訊時間戳](xref:Android.Media.AudioTimestamp)獲取資訊,以幫助進行音訊-視頻同步。

- *自訂間隔處同步內容解析器*- KitKat 為執行同步請求的時間添加了一些可變性。 `ContentResolver`以在自`ContentResolver.RequestSync`訂時間或時間間隔同步 。 `SyncRequest`

- *區分控制器*- 在 KitKat 中,控制器被分配唯一的整數識別`ControllerNumber`符,可以通過設備的屬性訪問。 這使得在遊戲中區分玩家更容易。

- *遠端控制*- 在硬體和軟體方面進行了一些更改,KitKat 允許您使用 將配備紅外發射器的設備轉換為`ConsumerIrService`遙控器, 並與週邊設備進行交互。[`RemoteController`](xref:Android.Media.RemoteController)
  API 的 OAuth 2.0 存取。

有關上述 API 更改的詳細資訊,請參閱 Google Android [4.4 API](https://developer.android.com/about/versions/android-4.4.html)概述。

## <a name="summary"></a>總結

本文介紹了 Android 4.4(API 級別 19)中提供的一些新 API,並介紹了將應用程式轉換為 KitKat 時的最佳做法。 它概述了對影響使用者體驗的 API 的更改,包括*過渡框架*和*用於該選項*的新選項。 接下來,它引入了*儲存存取框架*和`DocumentsProvider`類,以及新的*列印API。* 它探討了*基於NFC主機的卡模擬*以及如何使用*低功耗感測器*,包括兩個新的感測器來跟蹤使用者的步驟。 最後,它演示了通過*螢幕錄製*捕獲應用程式的即時演示,並提供了 KitKat API 更改和添加的詳細清單。

## <a name="related-links"></a>相關連結

- [KitKat 樣品](https://docs.microsoft.com/samples/xamarin/monodroid-samples/kitkat)
- [安卓 4.4 API](https://developer.android.com/about/versions/android-4.4.html)
- [安卓凱特](https://developer.android.com/about/versions/kitkat.html)
