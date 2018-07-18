---
title: 片段逐步解說-第 2 部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 58291388d375a4fd9273c8e0cd46db3799966766
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798910"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>片段逐步解說&ndash;橫印

[Fragment 逐步解說&ndash;第 1 部分](./walkthrough.md)示範了如何建立及使用目標較小的螢幕，在手機上的 Android 應用程式中的片段。 本逐步解說的下一個步驟是修改應用程式以充分利用平板電腦上的額外水平空間&ndash;會有一個一律會播放清單的活動 ( `TitlesFragment`) 和`PlayQuoteFragment`將以動態方式加入若要回應使用者所做的選取項目中的活動：

[![平板電腦上執行的應用程式](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

手機在橫向模式中執行也會從這項增強功能獲益：

[![在 Android 手機中以橫向模式執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>更新應用程序以處理橫向

中完成的工作將會建置下列修改[Fragment 逐步解說-電話](./walkthrough.md)

1. 建立替代的配置，以同時顯示`TitlesFragment`和`PlayQuoteFragment`。
1. 更新`TitlesFragment`偵測如果裝置會顯示兩個片段同時並據此變更行為。
1. 更新`PlayQuoteActivity`關閉裝置以橫向模式時。

## <a name="1-create-an-alternate-layout"></a>1.建立替代的版面配置

Android 裝置上建立主要活動時，Android 會決定要載入之版面配置會根據裝置的方向。 根據預設，會提供 Android **Resources/layout/activity_main.axml**配置檔案。 載入以橫向模式的裝置將會提供 Android **Resources/layout-land/activity_main.axml**配置檔案。 輔助線[Android 資源](/xamarin/android/app-fundamentals/resources-in-android)包含在 Android 如何決定哪些資源檔載入的應用程式的更多詳細資料。

建立替代的版面配置目標**橫向**方向中所述的步驟[替代配置](/xamarin/android/user-interface/android-designer/alternative-layout-views)指南。 這應該將新的版面配置資源檔加入專案中， **Resources/layout/activity_main.axml**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![替代在 [方案總管] 的版面配置](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![替代方案板的版面配置](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

在之後建立替代的配置，來編輯檔案的來源**Resources/layout-land/activity_main.axml**使其符合此 XML:

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

活動的 [根] 檢視提供的資源識別碼`two_fragments_layout`有兩個子檢視，和`fragment`和`FrameLayout`。 雖然`fragment`是以靜態方式載入，`FrameLayout`做為 「 預留位置 」，將會取代在執行階段由`PlayQuoteFragment`。 中已選取新的播放每次`TitlesFragment`、`playquote_container`將更新的新執行個體`PlayQuoteFragment`。

每個子檢視的會佔用其父代的全部高度。 每一個子檢視的寬度由`android:layout_weight`和`android:layout_width`屬性。 在此範例中，每一個子檢視可能會佔用 50%的寬度父系所提供。 請參閱[Google 的文件，在線性](https://developer.android.com/guide/topics/ui/layout/linear.html)如需詳細資訊_配置加權_。

## <a name="2-changes-to-titlesfragment"></a>2.TitlesFragment 的變更

一旦建立替代的版面配置，則需要更新`TitlesFragment`。 當應用程式會顯示兩個片段上一個活動，然後`TitlesFragment`應該載入`PlayQuoteFragment`父活動中。 否則，`TitlesFragment`應該啟動`PlayQuoteActivity`哪一部主機`PlayQuoteFragment`。 布林值旗標可協助`TitlesFragment`判斷應該使用哪一種行為。 這個旗標將會初始化在`OnActivityCreated`方法。

首先，在頂端新增執行個體變數`TitlesFragment`類別：

```csharp
bool showingTwoFragments;
```

然後，加入下列程式碼片段至`OnActivityCreated`來初始化變數： 

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

如果裝置正在執行以橫向模式，則`FrameLayout`的資源識別碼`playquote_container`會顯示在畫面上，因此`showingTwoFragments`將初始化為`true`。 如果裝置執行的在直向模式中，然後`playquote_container`將不會在畫面上，因此`showingTwoFragments`將`false`。

`ShowPlayQuote`方法必須將引號的顯示方式&ndash;片段或啟動新的活動。  更新`ShowPlayQuote`方法時所要載入片段顯示兩個片段，否則它應該啟動活動：

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

如果使用者選取不同的目前顯示在播放`PlayQuoteFragment`，然後新`PlayQuoteFragment`建立，就會取代內容`playquote_container`內`FragmentTransaction`。

### <a name="complete-code-for-titlesfragment"></a>TitlesFragment 的完整程式碼

完成所有先前變更之後`TitlesFragment`，完整的類別應該符合這個程式碼：

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

沒有一個來負責的最後一個詳細資料：`PlayQuoteActivity`不需要裝置以橫向模式時。 如果裝置是以橫向模式`PlayQuoteActivity`不應該為可見。 更新`OnCreate`方法`PlayQuoteActivity`，讓它將會自動關閉。 此程式碼是最終版本`PlayQuoteActivity.OnCreate`:

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

這項修改會將檢查裝置方向。 如果它是以橫向模式，則`PlayQuoteActivity`自動關閉。

## <a name="4-run-the-application"></a>4.執行應用程式

這些變更完成後，執行應用程式，旋轉橫向模式 （如有必要），該裝置，然後選取 播放。 引號也應該顯示在同一個畫面的播放清單：

[![在 Android 手機中以橫向模式執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![Android 平板電腦上執行應用程式](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
