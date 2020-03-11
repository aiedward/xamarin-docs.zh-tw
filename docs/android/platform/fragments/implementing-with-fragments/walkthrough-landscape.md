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
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020272"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>片段逐步解說 &ndash; 橫向

[片段逐步解說 &ndash; 第1部分](./walkthrough.md)示範如何在 Android 應用程式中建立及使用以電話上較小螢幕為目標的片段。 本逐步解說的下一個步驟是修改應用程式，以利用 tablet 上的額外水準空間 &ndash; 將會有一個活動一律會是播放清單（`TitlesFragment`），而 `PlayQuoteFragment` 會動態地新增至活動，以回應使用者所做的選擇：

[在平板電腦上執行 ![應用程式](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

以橫向模式執行的電話也將受益于這項增強功能：

[以橫向模式在 Android 手機上執行的 ![應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>更新應用程式以處理橫向方向

下列修改將依據[片段逐步解說-Phone](./walkthrough.md)中完成的工作來建立

1. 建立替代版面配置，以顯示 `TitlesFragment` 和 `PlayQuoteFragment`。
1. 更新 `TitlesFragment` 以偵測裝置是否同時顯示這兩個片段，並據此變更行為。
1. 當裝置處於橫向模式時，更新 `PlayQuoteActivity` 以關閉。

## <a name="1-create-an-alternate-layout"></a>1. 建立替代版面配置

在 Android 裝置上建立主要活動時，Android 會根據裝置的方向決定要載入的版面配置。 根據預設，Android 會提供**Resources/layout/activity_main 的 axml**設定檔案。 針對以橫向模式載入的裝置，Android 會提供**資源/配置-land/activity_main axml**配置檔案。 [Android 資源](/xamarin/android/app-fundamentals/resources-in-android)指南包含 android 如何決定要為應用程式載入哪些資源檔的詳細資料。

遵循[替代版面](/xamarin/android/user-interface/android-designer/alternative-layout-views)配置指南中所述的步驟，建立以**橫向**方向為目標的替代配置。 這應該會將新的版面配置資源檔加入至專案**Resources/layout/activity_main。 axml**：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[方案總管中 ![替代版面配置](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[Solution Pad 中 ![替代版面配置](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

建立替代配置之後，請編輯檔案**資源/配置-land/activity_main axml**的來源，使其符合此 XML：

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

活動的根視圖會提供資源識別碼 `two_fragments_layout`，而且有兩個子視圖、一個 `fragment` 和一個 `FrameLayout`。 當 `fragment` 以靜態方式載入時，`FrameLayout` 會做為「預留位置」，並會在執行時間由 `PlayQuoteFragment`取代。 每次在 `TitlesFragment`中選取新的播放時，`playquote_container` 都會以 `PlayQuoteFragment`的新實例進行更新。

每個子視圖都會佔用其父系的完整高度。 每個子視圖的寬度都是由 `android:layout_weight` 和 `android:layout_width` 屬性所控制。 在此範例中，每個子視圖都會佔用父系所提供的50% 寬度。 如需_版面配置權數_的詳細資訊，請參閱[LinearLayout 上的 Google 檔](https://developer.android.com/guide/topics/ui/layout/linear.html)。

## <a name="2-changes-to-titlesfragment"></a>2. 對 TitlesFragment 的變更

建立替代版面配置之後，就必須更新 `TitlesFragment`。 當應用程式在一個活動上顯示兩個片段時，`TitlesFragment` 應該在父活動中載入 `PlayQuoteFragment`。 否則，`TitlesFragment` 應該啟動裝載 `PlayQuoteFragment`的 `PlayQuoteActivity`。 布林值旗標將有助於 `TitlesFragment` 判斷應該使用的行為。 此旗標將會在 `OnActivityCreated` 方法中初始化。

首先，在 `TitlesFragment` 類別的頂端加入執行個體變數：

```csharp
bool showingTwoFragments;
```

然後，將下列程式碼片段新增至 `OnActivityCreated` 以初始化變數： 

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

如果裝置是在橫向模式中執行，則會在畫面上看到具有資源識別碼 `playquote_container` 的 `FrameLayout`，因此 `showingTwoFragments` 會初始化為 `true`。 如果裝置是以直向模式執行，`playquote_container` 將不會顯示在畫面上，因此 `showingTwoFragments` 將會 `false`。

`ShowPlayQuote` 方法將需要變更它在片段中的顯示方式 &ndash;，或啟動新的活動。  在顯示兩個片段時，更新 `ShowPlayQuote` 方法以載入片段，否則應該啟動活動：

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

如果使用者選取的播放與目前在 `PlayQuoteFragment`中顯示的不同，則會建立新的 `PlayQuoteFragment`，並取代 `FragmentTransaction`內容中的 `playquote_container` 內容。

### <a name="complete-code-for-titlesfragment"></a>TitlesFragment 的完整程式碼

完成所有先前的變更 `TitlesFragment`之後，完整的類別應該符合此程式碼：

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

## <a name="3-changes-to-playquoteactivity"></a>3. 對 PlayQuoteActivity 的變更

有一項要注意的最後詳細資料：當裝置處於橫向模式時，`PlayQuoteActivity` 不是必要的。 如果裝置處於橫向模式，則不應該顯示 `PlayQuoteActivity`。 更新 `PlayQuoteActivity` 的 `OnCreate` 方法，使其本身會關閉。 這段程式碼是 `PlayQuoteActivity.OnCreate`的最終版本：

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

這種修改會新增裝置方向的檢查。 如果它是橫向模式，`PlayQuoteActivity` 會自行關閉。

## <a name="4-run-the-application"></a>4. 執行應用程式

這些變更完成後，請執行應用程式，將裝置旋轉為橫向模式（如有必要），然後選取 [播放]。 報價應該會顯示在與播放清單相同的畫面上：

[以橫向模式在 Android 手機上執行的 ![應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[在 Android 平板電腦上執行 ![應用程式](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
