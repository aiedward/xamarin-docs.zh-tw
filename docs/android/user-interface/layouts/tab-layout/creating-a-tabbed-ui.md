---
title: 逐步解說-使用 TabHost 建立索引標籤式的 UI
description: 本文將逐步引導 Xamarin.Android 使用 TabHost API 中建立索引標籤式的 UI。
ms.prod: xamarin
ms.assetid: AD6E2173-974E-477C-940F-0CAB5E53326D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: ca9a3f3d31707205cdcd4e0d8e74fa303ccba047
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---creating-a-tabbed-ui-with-tabhost"></a>逐步解說-使用 TabHost 建立索引標籤式的 UI

_本文將逐步引導 Xamarin.Android 使用 TabHost API 中建立索引標籤式的 UI。_

> [!NOTE]
> `TabHost` 是由 Google 已被取代的舊 API。 開發人員都建置索引標籤式的應用程式使用[項](~/android/user-interface/controls/action-bar.md)。 `ActionBar`用於所有的 Android 版本。 它引進 Android 3.0 （API 層級 11） 和回已移植到 Android 2.2 (API level 8) 和中的 Android 2.3 （API 層級 10） [V7 AppCompat 程式庫](http://developer.android.com/tools/support-library/features.html#v7-appcompat)，這是可透過 Xamarin.Android [XamarinAndroid 支援程式庫-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)封裝。

本文將逐步引導 Xamarin.Android 中建立索引標籤式的 UI 使用`TabHost`應用程式開發介面。 這是適用於 Android 的所有版本的舊版 API。 這個範例會建立三個索引標籤，在活動中封裝每個索引標籤的邏輯與應用程式。
下列螢幕擷取畫面是我們將建立的應用程式的範例：

![具有多個索引標籤的應用程式的範例螢幕擷取畫面](creating-a-tabbed-ui-images/image02.png)


## <a name="creating-the-application"></a>建立應用程式

下載並解壓縮[TabHostWalkthrough](https://developer.xamarin.com/samples/monodroid/UserInterface/TabHostWalkthrough/)。
這個專案做為我們的應用程式的起點，並包含一些映像。 如果您檢查此專案，您會看到，我們已經建立的 drawable 資源 索引標籤圖示。

第一個更新配置檔案，讓我們**Resources/Layout/Main.axml**將要裝載索引標籤。 編輯**Resources/Layout/Main.axml**並插入下列 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
         android:id="@android:id/tabhost"
         android:layout_width="fill_parent"
         android:layout_height="fill_parent">
    <LinearLayout
            android:orientation="vertical"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:padding="5dp">
        <TabWidget
                android:id="@android:id/tabs"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content" />
        <FrameLayout
                android:id="@android:id/tabcontent"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:padding="5dp" />
    </LinearLayout>
</TabHost>
```

下列螢幕擷取畫面顯示 Xamarin 設計工具中的配置：

[![TabHost 配置 Xamarin 設計工具中的螢幕擷取畫面](creating-a-tabbed-ui-images/image04-sml.png)](creating-a-tabbed-ui-images/image04.png#lightbox)

TabHost 必須有兩個內文中的子檢視：`TabWidget`和`FrameLayout`。 位置`TabWidget`和`FrameLayout`垂直內部`TabHost`、`LinearLayout`用。 每個索引標籤內容的地方，這是空的 FrameLayout 是因為`TabHost`會自動內嵌在執行階段的每個活動。 有數個建立的索引標籤式的使用者介面版面配置時，必須遵守的規則：

-   `TabHost`必須具有 id `@android:id/tabhost`。

-   `TabWidget`必須具有 id `@android:id/tabs`。

-   `FrameLayout`必須具有 id `@android:id/tabcontent`。

-   `TabHost` 需要的任何活動都繼承自它管理`TabActivity`。 因此，它是子類別化重要`TabActivity`這裡&ndash;一般活動將無法運作。

編輯檔案**Weatherapp**使類別`MainActivity`子類別`TabActivity`如下列程式碼片段所示：

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/ic_launcher")]
public class MainActivity : TabActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
    }
}
```

在專案中建立四個不同的活動類別： `MyScheduleActivity`， `SessionsActivity`， `SpeakersActivity`，和`WhatsOnActivity`。 每個活動將構成索引標籤的 UI。現在這些活動將會是顯示的虛設常式`TextView`與簡單的訊息。 編輯每個活動，以包含下列中的程式碼`OnCreate`實作：

```csharp
[Activity]
public class MyScheduleActivity : Activity
{
    protected override void OnCreate (Bundle savedInstanceState)
    {
        base.OnCreate (savedInstanceState);
        TextView textview = new TextView (this);
        textview.Text = "This is the My Schedule tab";
        SetContentView (textview);
    }
}
```

請注意上述程式碼不會使用配置檔案。 它只會建立`TextView`與一些文字，並將其設定`TextView`做為內容的檢視。 重複這的每個剩餘的三個活動。

接下來我們會將圖示指派給每個索引標籤。每個索引標籤需要兩個圖示&ndash;其中一個選取的狀態，另一個未選取狀態。 下列兩個映像 （此應用程式所需的圖示已加入至範例專案） 中可以看到這些兩個不同的圖示的範例：

![選取的狀態和未選取的狀態圖示的螢幕擷取畫面](creating-a-tabbed-ui-images/tab-icons.png)


我們將會藉由定義 [圖示] 索引標籤來指派 drawable 資源*狀態清單 Drawable*。 狀態清單 drawables 是特殊的 drawable 資源會以 XML 定義，並允許您指定不同的影像所特有的項目狀態。 在此範例中沒有所選取索引標籤時，使用一個影像，而另一個未選取 [] 索引標籤時所使用。 若要儲存您的時間，所需的狀態清單 drawables 已加入至專案。 下列清單顯示的檔案和每個 XML 包含：

-   `ic_tab_my_schedule.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_whats_on_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_whats_on_unselected"/>
    </selector>
    ```

-   `ic_tab_sessions.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_sessions_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_sessions_unselected"/>
    </selector>
    ```

-   `ic_tab_speakers.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_speakers_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_speakers_unselected"/>
    </selector>
    ```

-   `ic_tab_whats_on.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_whats_on_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_whats_on_unselected"/>
    </selector>
    ```

索引標籤會加入至`TabHost`程式設計角度而言，這是非常重複性的工作。 為協助解決這，將下列方法加入類別`MainActivity`:

```csharp
private void CreateTab(Type activityType, string tag, string label, int drawableId )
{
    var intent = new Intent(this, activityType);
    intent.AddFlags(ActivityFlags.NewTask);

    var spec = TabHost.NewTabSpec(tag);
    var drawableIcon = Resources.GetDrawable(drawableId);
    spec.SetIndicator(label, drawableIcon);
    spec.SetContent(intent);

    TabHost.AddTab(spec);
}
```

每個索引標籤中的`TabHost`的執行個體所表示的`TabHost.TabSpec`類別。 這個執行個體包含中繼資料，特別是呈現索引標籤上，所需：

-   **文字和圖示**&ndash;中顯示`TabWidget`。

-   **內容索引標籤上**&ndash;這可能是`Activity`或`View`並選取 [] 索引標籤時顯示。

-   **唯一的標記**&ndash;每個索引標籤都必須具有唯一的標記指派給它。

我們必須加入`TabHost.TabSpec`我們的應用程式中的每個索引標籤的執行個體。 可讓，在下一個步驟中。

Update 方法`OnCreate`中`MainActivity`，讓它類似下列程式碼：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateTab(typeof(WhatsOnActivity), "whats_on", "What's On", Resource.Drawable.ic_tab_whats_on);
    CreateTab(typeof(SpeakersActivity), "speakers", "Speakers", Resource.Drawable.ic_tab_speakers);
    CreateTab(typeof(SessionsActivity), "sessions", "Sessions", Resource.Drawable.ic_tab_sessions);
    CreateTab(typeof(MyScheduleActivity), "my_schedule", "My Schedule", Resource.Drawable.ic_tab_my_schedule);
}
```

執行應用程式。 您的應用程式看起來應該像螢幕擷取畫面顯示在本逐步解說的開頭。

就這麼容易！ 我們已經建立索引標籤式的應用程式，讓的使用者可以輕鬆地瀏覽應用程式的不同部分。



## <a name="summary"></a>總結

本章節討論索引標籤式的配置，並引導您完成建立索引標籤式的應用程式的程序。 逐步解說示範如何使用`TabActivity`擴大配置檔案的裝載`TabHost`和`TabWidget`。 `TabHost`接著填入集合`TabHost.TabSpec`物件就會使用由`TabHost`在執行階段具現化的活動，會用在每個索引標籤。



## <a name="related-links"></a>相關連結

- [TabHostWalkthrough （範例）](https://developer.xamarin.com/samples/monodroid/UserInterface/TabHostWalkthrough/)
- [TabHost](https://developer.xamarin.com/api/type/Android.Widget.TabHost/)
- [TabWidget](https://developer.xamarin.com/api/type/Android.Widget.TabWidget/)
- [TabActivity](https://developer.xamarin.com/api/type/Android.App.TabActivity/)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 支援程式庫 v7 AppCompat NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 程式庫](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
