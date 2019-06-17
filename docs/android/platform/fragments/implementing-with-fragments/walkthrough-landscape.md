---
title: 片段逐步解說 - 第 2 部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 7ec8ad6ce428107d2255dd07c7e69c9e77780c09
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026235"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>片段逐步作業&ndash;橫印

[Fragment 逐步解說&ndash;第 1 部分](./walkthrough.md)示範了如何建立及使用以目標較小的螢幕，在手機上的 Android 應用程式中的片段。 本逐步解說的下一個步驟是修改應用程式以充分利用在平板電腦上的額外水平間距&ndash;會有一定的播放清單的一個活動 ( `TitlesFragment`) 和`PlayQuoteFragment`將以動態方式新增回應使用者所做的選取項目中的活動：

[![在平板電腦上執行的應用程式](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

手機在橫向模式中執行也能受益這項增強功能：

[![在 Android 手機中以橫向模式上執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>更新應用程序以處理橫向

下列的修改將於中完成的工作為基礎建置[Fragment 逐步解說-電話](./walkthrough.md)

1. 建立替代的配置，以顯示兩者`TitlesFragment`和`PlayQuoteFragment`。
1. 更新`TitlesFragment`偵測如果裝置顯示這兩個片段同時並據此變更行為。
1. 更新`PlayQuoteActivity`關閉裝置在橫向模式中時。

## <a name="1-create-an-alternate-layout"></a>1.建立替代的版面配置

在 Android 裝置上建立主要活動時，Android 會決定哪個配置，將根據裝置的方向。 根據預設，Android 會提供**Resources/layout/activity_main.axml**版面配置檔。 在橫向模式中載入的裝置會提供 Android **Resources/layout-land/activity_main.axml**版面配置檔。 在本指南[Android 資源](/xamarin/android/app-fundamentals/resources-in-android)包含更多有關 Android 如何決定哪項資源檔案載入應用程式。

建立替代的版面配置目標**橫向**中所述的步驟方向[替代配置](/xamarin/android/user-interface/android-designer/alternative-layout-views)指南。 這應該將新的版面配置資源檔加入專案中， **Resources/layout/activity_main.axml**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![在 [方案總管] 中的替代版面配置](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![在 Solution Pad 中的替代版面配置](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

在之後建立替代的版面配置，來編輯檔案的來源**Resources/layout-land/activity_main.axml** ，使其符合這個 XML:

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

活動的 [根] 檢視提供的資源識別碼`two_fragments_layout`有兩個的子檢視，並`fragment`和`FrameLayout`。 雖然`fragment`以靜態方式載入時，`FrameLayout`做為 「 預留位置 」，會在執行階段藉由取代`PlayQuoteFragment`。 每當中選取新婧矔菛`TitlesFragment`，則`playquote_container`的新執行個體將會更新`PlayQuoteFragment`。

子檢視的每個將佔滿其父系的完整高度。 每一個子檢視的寬度會受到`android:layout_weight`和`android:layout_width`屬性。 在此範例中，每一個子檢視會佔用 50%寬度的父元素所提供。 請參閱[Google 的文件上 LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html)如需詳細資訊_版面配置權重_。

## <a name="2-changes-to-titlesfragment"></a>2.TitlesFragment 的變更

一旦建立替代的版面配置，就必須更新`TitlesFragment`。 當應用程式顯示的兩個片段的一個活動，然後`TitlesFragment`應該載入`PlayQuoteFragment`父活動中。 否則，請`TitlesFragment`應該啟動`PlayQuoteActivity`哪一部主機`PlayQuoteFragment`。 布林值旗標可協助`TitlesFragment`判斷應該使用哪一種行為。 這個旗標將會初始化在`OnActivityCreated`方法。

首先，新增在頂端的 執行個體變數`TitlesFragment`類別：

```csharp
bool showingTwoFragments;
```

然後，新增下列程式碼片段`OnActivityCreated`來初始化變數： 

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

如果裝置正在執行中以橫向模式，則`FrameLayout`的資源識別碼`playquote_container`會顯示在畫面上，因此`showingTwoFragments`將會初始化為`true`。 如果裝置執行在直向模式中，然後`playquote_container`不會在畫面上，因此`showingTwoFragments`會`false`。

`ShowPlayQuote`方法將會需要變更報價的顯示方式&ndash;片段或啟動新的活動。  更新`ShowPlayQuote`方法時所要載入片段顯示兩個片段，否則便應該啟動的活動：

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

如果使用者選取與目前顯示在一個不同的婧矔菛`PlayQuoteFragment`，然後新`PlayQuoteFragment`會建立，並會取代內容`playquote_container`的內容中`FragmentTransaction`。

### <a name="complete-code-for-titlesfragment"></a>TitlesFragment 的完整程式碼

完成所有先前的變更，若要之後`TitlesFragment`，完整的類別應該符合此程式碼：

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

## <a name="3-changes-to-playquoteactivity"></a>3.PlayQuoteActivity 的變更

一個負責的最終詳細資料：`PlayQuoteActivity`裝置為橫向模式時並非必要。 如果裝置是以橫向模式`PlayQuoteActivity`不應該為可見。 更新`OnCreate`方法的`PlayQuoteActivity`，讓它將會自動關閉。 此程式碼是最終版本`PlayQuoteActivity.OnCreate`:

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

這項修改會將裝置方向的檢查。 如果它是以橫向模式，則`PlayQuoteActivity`會自動關閉。

## <a name="4-run-the-application"></a>4.執行應用程式

這些變更完成後，執行應用程式，旋轉成橫向模式 （如有必要），裝置，然後選取 婧矔菛。 報價應該顯示在同一個畫面的播放清單：

[![在 Android 手機中以橫向模式上執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![在 Android 平板電腦上執行的應用程式](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
