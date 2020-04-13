---
title: 片段逐步解說 - 第 2 部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: 4d9ef88f39914f8fa5e578577ee9f6977c2bc88e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020272"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>片段演練&ndash;景觀

[片段演練第&ndash;1 部分](./walkthrough.md)演示了如何在面向手機上較小螢幕的 Android 應用中創建和使用片段。 這個演練的下一步是修改應用程式,以利用平板電腦&ndash;上的額外水準空間,將有一個活動將始終為播放清單`TitlesFragment`(the),`PlayQuoteFragment`並將 動態添加到活動以回應使用者所做的選擇:

[![在平板電腦上執行的應用程式](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

在橫向模式下運行的手機也將受益於此增強功能:

[![在橫向模式下在 Android 手機上執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>更新應用以處理橫向

以下修改將基於在[片段演練](./walkthrough.md)中完成的工作 - 電話

1. 建立備用佈局以同時顯示`TitlesFragment`與`PlayQuoteFragment`。
1. 更新`TitlesFragment`以檢測設備是否同時顯示兩個片段,並相應地更改行為。
1. 當`PlayQuoteActivity`設備處於橫向模式時更新以關閉。

## <a name="1-create-an-alternate-layout"></a>1. 建立備用佈局

在 Android 設備上創建主活動時,Android 將根據設備的方向決定載入哪個佈局。 默認情況下,Android 將提供**資源/佈局/activity_main.axml**佈局檔。 對於在橫向模式下載入的設備,Android 將提供**資源/佈局-土地/activity_main.axml**佈局檔。 [Android 資源](/xamarin/android/app-fundamentals/resources-in-android)指南包含有關 Android 如何決定為應用程式載入哪些資源檔的更多詳細資訊。

按照[「備用佈局](/xamarin/android/user-interface/android-designer/alternative-layout-views)」指南中描述的步驟創建以**橫向**方向為目標的備用佈局。 這應該新增新的佈局資源檔案,**資源/ 佈局 / activity_main. axml**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![解決方案資源管理員中的備用佈局](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![解決方案板中的備用佈局](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

建立備用佈局後,編輯檔案**資源/佈局-land/activity_main.axml 的**來源,以便它與此 XML 符合:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/two_fragments_layout"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <fragment android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_weight="1"
        android:layout_width="0px"
        android:layout_height="match_parent" />

    <FrameLayout android:id="@+id/playquote_container"
            android:layout_weight="1"
            android:layout_width="0px"
            android:layout_height="match_parent"
             />
</LinearLayout>
```

活動的根檢視為資源代碼`two_fragments_layout`提供,並且具有兩個子檢視,a`fragment`和`FrameLayout`。 `fragment`靜態載入 時,充`FrameLayout`當 「占位符」,將在執行時`PlayQuoteFragment`由 取代 。 每次選擇新播放時`TitlesFragment`,`playquote_container`您可以使用新的`PlayQuoteFragment`實體更新 。

每個子視圖將佔據其父視圖的全部高度。 每個子檢視的寬度由`android:layout_weight`和`android:layout_width`屬性控制。 在此示例中,每個子視圖將佔用父視圖提供的寬度的 50%。 有關_佈局權重_的詳細資訊,請參閱[Google 有關線性佈局的文件](https://developer.android.com/guide/topics/ui/layout/linear.html)。

## <a name="2-changes-to-titlesfragment"></a>2. 改變標題區段

建立備用佈局後,必須更新`TitlesFragment`。 當應用在一個活動上顯示兩個片段時,則應`TitlesFragment`在父活動`PlayQuoteFragment`中 載入 。 否則,`TitlesFragment`應`PlayQuoteActivity`啟動 哪個`PlayQuoteFragment`主機 。 布爾標誌將幫助`TitlesFragment`確定應該使用哪些行為。 這個旗標會在方法中初始`OnActivityCreated`化 。

首先,在`TitlesFragment`類的頂部添加實例變數:

```csharp
bool showingTwoFragments;
```

然後,新增以下代碼段以`OnActivityCreated`初始化變數: 

```csharp
var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
showingTwoFragments = quoteContainer != null &&
                      quoteContainer.Visibility == ViewStates.Visible;
if (showingTwoFragments)
{
    ListView.ChoiceMode = ChoiceMode.Single;
    ShowPlayQuote(selectedPlayId);
}
```

如果裝置以橫向模式執行,`FrameLayout`則具有資源`playquote_container`ID 的視窗上可見`showingTwoFragments`,因此將初始`true`化為 。 如果裝置以縱向模式執行,則`playquote_container`不會在螢幕上,因此`showingTwoFragments`將`false`。

該方法`ShowPlayQuote`需要更改它在片段中顯示報價&ndash;單 或啟動新活動的方式。  在`ShowPlayQuote`顯示兩個片段時更新方法以載入片段,否則應啟動活動:

```csharp
void ShowPlayQuote(int playId)
{
    selectedPlayId = playId;
    if (showingTwoFragments)
    {
        ListView.SetItemChecked(selectedPlayId, true);

        var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

        if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
        {
            var container = Activity.FindViewById(Resource.Id.playquote_container);
            var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

            FragmentTransaction ft = FragmentManager.BeginTransaction();
            ft.Replace(Resource.Id.playquote_container, quoteFrag);
            ft.Commit();
        }
    }
    else
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

如果用戶選擇了`PlayQuoteFragment`與 當前中顯示的播放不同的播放,則會創建一`PlayQuoteFragment`個新戲,`playquote_container`並將替換的上下文中`FragmentTransaction`的內容。

### <a name="complete-code-for-titlesfragment"></a>標題片段的完整代碼

完成對 的所有以前`TitlesFragment`變更 後,完整類別應符合此代碼:

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;
    bool showingTwoFragments;

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }

        var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
        showingTwoFragments = quoteContainer != null &&
                                quoteContainer.Visibility == ViewStates.Visible;
        if (showingTwoFragments)
        {
            ListView.ChoiceMode = ChoiceMode.Single;
            ShowPlayQuote(selectedPlayId);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        selectedPlayId = playId;
        if (showingTwoFragments)
        {
            ListView.SetItemChecked(selectedPlayId, true);

            var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

            if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
            {
                var container = Activity.FindViewById(Resource.Id.playquote_container);
                var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

                FragmentTransaction ft = FragmentManager.BeginTransaction();
                ft.Replace(Resource.Id.playquote_container, quoteFrag);
                ft.AddToBackStack(null);
                ft.SetTransition(FragmentTransit.FragmentFade);
                ft.Commit();
            }
        }
        else
        {
            var intent = new Intent(Activity, typeof(PlayQuoteActivity));
            intent.PutExtra("current_play_id", playId);
            StartActivity(intent);
        }
    }
}
```

## <a name="3-changes-to-playquoteactivity"></a>3. 變更播放報價活動

有最後一個細節需要處理:`PlayQuoteActivity`當設備處於橫向模式時,不需要。 如果設備處於橫向模式,`PlayQuoteActivity`則不應可見。 更新`OnCreate``PlayQuoteActivity`方法 ,以便它自行關閉。 此代碼是`PlayQuoteActivity.OnCreate`: 的最終版本:

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    base.OnCreate(savedInstanceState);

    if (Resources.Configuration.Orientation == Android.Content.Res.Orientation.Landscape)
    {
        Finish();
    }

    var playId = Intent.Extras.GetInt("current_play_id", 0);
    var playQuoteFrag = PlayQuoteFragment.NewInstance(playId);
    FragmentManager.BeginTransaction()
                    .Add(Android.Resource.Id.Content, playQuoteFrag)
                    .Commit();
}
```

此修改添加了對設備方向的檢查。 如果處於橫向模式,則`PlayQuoteActivity`將自行關閉。

## <a name="4-run-the-application"></a>4. 執行應用程式

完成這些更改後,運行應用,將設備旋轉到橫向模式(如有必要),然後選擇播放。 報價應與播放清單在同一螢幕上顯示:

[![在橫向模式下在 Android 手機上執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![在 Android 平板電腦上執行的應用程式](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
