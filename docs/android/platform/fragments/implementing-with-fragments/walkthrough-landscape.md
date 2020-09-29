---
title: 片段逐步解說 - 第 2 部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: c0d4b0d1a31be43d16fb69eba18c07815631f496
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453931"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>片段逐步解說 &ndash; 橫向

[片段逐步解說 &ndash; 第1部分](./walkthrough.md)示範如何在 Android 應用程式中建立及使用以手機上較小螢幕為目標的片段。 本逐步解說的下一個步驟是修改應用程式，以利用 tablet 上的額外水準空間， &ndash;  其中會有一個活動一律是 () 的播放清單 `TitlesFragment` ，並會  `PlayQuoteFragment` 動態新增至活動以回應使用者所做的選擇：

[![在平板電腦上執行的應用程式](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

以橫向模式執行的電話也會受益于這項增強功能：

[![在 Android 手機上以橫向模式執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>更新應用程式以處理橫向方向

下列修改會依據在片段逐步解說中所做的工作來建立 [-Phone](./walkthrough.md)

1. 建立替代版面配置，以同時顯示 `TitlesFragment` 和 `PlayQuoteFragment` 。
1. 更新 `TitlesFragment` 以偵測裝置是否同時顯示這兩個片段，並據以變更行為。
1. `PlayQuoteActivity`當裝置處於橫向模式時，更新以關閉。

## <a name="1-create-an-alternate-layout"></a>1. 建立替代版面配置

當您在 Android 裝置上建立主要活動時，Android 會根據裝置的方向來決定要載入的版面配置。 根據預設，Android 會提供 **Resources/layout/activity_main .axml** 版面配置檔案。 針對在橫向模式中載入的裝置，Android 將提供 **資源/配置-land/activity_main .axml** 版面配置檔案。 [Android 資源](../../../app-fundamentals/resources-in-android/index.md)指南包含 android 如何決定要為應用程式載入哪些資源檔的詳細資料。

遵循[替代版面](../../../user-interface/android-designer/alternative-layout-views.md)配置指南中所述的步驟，建立以**橫向**方向為目標的替代配置。 這應該會將新的版面配置資源檔新增至專案、 **資源/配置/activity_main .axml**：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![方案總管中的替代版面配置](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![Solution Pad 中的替代版面配置](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

建立替代版面配置之後，請編輯檔案 **資源/配置-land/activity_main .axml** 的來源，使其符合此 XML：

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

活動的根視圖會獲得資源識別碼， `two_fragments_layout` 並具有兩個子視圖： `fragment` 和 `FrameLayout` 。 `fragment`靜態載入時， `FrameLayout` 會做為將在執行時間取代的「預留位置」 `PlayQuoteFragment` 。 每次在中選取新的播放時 `TitlesFragment` ，都會以的 `playquote_container` 新實例更新 `PlayQuoteFragment` 。

每個子視圖都會佔用其父系的完整高度。 每個子視圖的寬度都是由 `android:layout_weight` 和 `android:layout_width` 屬性所控制。 在此範例中，每個子視圖將會佔用父系所提供的50% 寬度。 如需_版面配置權數_的詳細資訊，請參閱[LinearLayout 上的 Google 檔](https://developer.android.com/guide/topics/ui/layout/linear.html)。

## <a name="2-changes-to-titlesfragment"></a>2. TitlesFragment 的變更

一旦建立了替代版面配置，就必須更新 `TitlesFragment` 。 當應用程式在一個活動上顯示兩個片段時， `TitlesFragment` 應 `PlayQuoteFragment` 在父活動中載入。 否則， `TitlesFragment` 應該啟動 `PlayQuoteActivity` 裝載的 `PlayQuoteFragment` 。 布林值旗標將有助於 `TitlesFragment` 判斷應該使用的行為。 此旗標會在方法中初始化 `OnActivityCreated` 。

首先，將執行個體變數新增至類別的最上方 `TitlesFragment` ：

```csharp
bool showingTwoFragments;
```

然後，將下列程式碼片段新增至以  `OnActivityCreated` 初始化變數： 

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

如果裝置是在橫向模式中執行，則會 `FrameLayout` `playquote_container` 在畫面上顯示具有資源識別碼的，因此將會 `showingTwoFragments` 初始化為 `true` 。 如果裝置是在直向模式中執行，則 `playquote_container` 不會在畫面上，因此會 `showingTwoFragments` 是 `false` 。

`ShowPlayQuote`方法將需要變更其 &ndash; 在片段中顯示報價或啟動新活動的方式。  `ShowPlayQuote`在顯示兩個片段時，更新方法以載入片段，否則應該啟動活動：

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

如果使用者選取的播放與目前顯示的不同，則會建立新的，並在的 `PlayQuoteFragment` `PlayQuoteFragment` 內容中取代的內容 `playquote_container` `FragmentTransaction` 。

### <a name="complete-code-for-titlesfragment"></a>TitlesFragment 的完整程式碼

完成所有先前的變更之後 `TitlesFragment` ，完整的類別應該會符合下列程式碼：

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

## <a name="3-changes-to-playquoteactivity"></a>3. 變更 PlayQuoteActivity

有一個要注意的最終詳細資料： `PlayQuoteActivity` 當裝置處於橫向模式時，並不需要。 如果裝置處於橫向模式，則 `PlayQuoteActivity` 看不到。 更新的 `OnCreate` 方法 `PlayQuoteActivity` ，以便將其關閉。 這段程式碼是的最終版本 `PlayQuoteActivity.OnCreate` ：

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

這種修改會新增裝置方向的檢查。 如果是在橫向模式中，則 `PlayQuoteActivity` 會關閉本身。

## <a name="4-run-the-application"></a>4. 執行應用程式

完成這些變更後，請執行應用程式，視需要將裝置旋轉為橫向模式 () ，然後選取 [播放]。 報價應顯示在與播放清單相同的畫面上：

[![在 Android 手機上以橫向模式執行的應用程式](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![在 Android 平板電腦上執行的應用程式](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)