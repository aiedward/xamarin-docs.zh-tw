---
title: 逐步解說
ms.prod: xamarin
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: cc5c05fce9b9c3dd974afe027cc7cbb60085c621
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough"></a>逐步解說

在下列步驟中，與片段建立基本的應用程式。 第一個步驟是建立新的 Xamarin.Android 針對 Android 專案。

## <a name="1-create-a-project"></a>1.建立專案

建立新的 Xamarin.Android 專案，稱為**FragmentSample**。 **最小值 Android**版本應該被設定至 Android 3.1 或更新版本，如圖所示：

[![設定 Android 最小版本](walkthrough-images/00.png)](walkthrough-images/00.png#lightbox)


## <a name="2-create-the-mainactivity"></a>2.建立 MainActivity

接下來，我們必須建立`MainActivity`類別。 這是為應用程式的啟動活動。 這項活動將會裝載一或兩個片段，螢幕大小而定。 `MainActivity` 會這樣做是適用於裝置的版面配置檔案載入。

```csharp
[Activity(Label = "Fragments Walkthrough", MainLauncher = true, Icon = "@drawable/launcher")]
public class MainActivity : Activity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       SetContentView(Resource.Layout.activity_main);
   }
}
```

> [!NOTE]
> `Note:` 沒有引數建構函式時，片段子類別必須有公用的預設值。

## <a name="3-create-the-layout-files"></a>3.建立版面配置檔案

兩個不同的螢幕大小需要兩個不同的版面配置檔案。 現在讓我們來建立新的資料夾，**資源/配置大型**，並建立新的版面配置，稱為**activity_main.axml**。 我們也將重新命名預設配置檔案，做為**Resources/Layout/activity_main.axml**。 這些變更之後，請配置資料夾應該類似下列的螢幕擷取畫面：

[![在 IDE 中的配置資料夾的螢幕擷取畫面](walkthrough-images/01.png)](walkthrough-images/01.png#lightbox)


所有的裝置將會載入並使用中的配置檔案**資源/配置**。
它是非常簡單的版面配置，只會顯示`TitlesFragment`:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="horizontal"
       android:layout_width="fill_parent"
       android:layout_height="fill_parent">
 <fragment class="com.xamarin.sample.fragments.supportlib.TitlesFragment"
           android:id="@+id/titles_fragment"
           android:layout_width="fill_parent"
           android:layout_height="fill_parent" />
</LinearLayout>
```

針對有大螢幕的裝置，Android 將會載入設定檔中的**資源/配置大型**。 平板電腦的版面配置的內容如下所示：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="horizontal"
       android:layout_width="fill_parent"
       android:layout_height="fill_parent">
 <fragment class="com.xamarin.sample.fragments.supportlib.TitlesFragment"
           android:id="@+id/titles_fragment"
           android:layout_weight="1"
           android:layout_width="0px"
           android:layout_height="match_parent" />
 <FrameLayout android:id="@+id/details"
              android:layout_weight="1"
              android:layout_width="0px"
              android:layout_height="match_parent" />
</LinearLayout>
```

對於較大的螢幕配置檔有些許不同。 不只是`TitlesFragment`顯示在這個配置的檔案，但`FrameLayout`加入片段旁邊。 在較大的畫面，`DetailsFragment`以程式設計方式加入`MainActivity`當使用者選取的播放。 在稍後我們將說明更詳細地方式。

Android 3.2 導入了新的方法，來指定畫面配置。 這些新的辨識符號指定需要您的配置，而不是螢幕大小的空間數量。 如果此應用程式都要執行只會在 Android 3.2 或更新版本，我們可以建立**sw600dp-配置資源/**資料夾 (而不是資料夾**資源/配置大型**) 配置檔案**activity_main.axml**。 資源載入此檔案會由具有 600 密度無關的像素的最小的螢幕寬度的所有裝置。 不過，因為這個應用程式設為目標 Android 3.1 或更新版本中，它會使用較舊的資源限定詞。

## <a name="4-create-the-titlesfragment"></a>4.建立 TitlesFragment

`TitlesFragment` 會顯示各種所扮演的標題，請讓我們將加入新專案的片段稱為`TitlesFragment`:

[![TitlesFragment 專案中加入新的片段](walkthrough-images/02.png)](walkthrough-images/02.png#lightbox)

之後`TitlesFragment`已經加入，我們必須變更類別，使它繼承自`Android.App.ListFragment`。 `ListFragment` 是特製化的片段類型，包括清單功能。
`TitlesFragment` 也會覆寫`OnActivityCreated`（另一個片段存留週期方法），並提供`Adapter`，`ListFragment`用以填入清單：

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
   base.OnActivityCreated(savedInstanceState);
   var adapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemChecked, Shakespeare.Titles);
   ListAdapter = adapter;
   if (savedInstanceState != null)
   {
       _currentPlayId = savedInstanceState.GetInt("current_play_id", 0);
   }
   var detailsFrame = Activity.FindViewById<View>(Resource.Id.details);
   _isDualPane = detailsFrame != null && detailsFrame.Visibility == ViewStates.Visible;
   if (_isDualPane)
   {
       ListView.ChoiceMode = (int) ChoiceMode.Single;
       ShowDetails(_currentPlayId);
   }
}
```

如先前所述，我們的應用程式具有兩個配置`MainActivity`。 中的程式碼`OnActivityCreated`偵測到的存在`FrameLayout`並判斷哪些配置檔已經載入。 如果`FrameLayout`存在於版面配置，然後在`_isDualPane`旗標設為`true`。 `_isDualPane`旗標在其他地方使用活動，特別是藉由`ShowDetails`方法。 `ShowDetails`方法將以下更詳細討論。

`TitlesFragment` 是清單，並必須回應使用者選取項目在清單中。 若要這樣做，`TitlesFragment`會覆寫方法`OnListItemClick`。 內部`OnListItemClick`，新`DetailsFragment`會建立並顯示在`FrameLayout`、 以程式設計的方式。 相關的程式碼內`TitlesFragment`是：

```csharp
public override void OnListItemClick(ListView l, View v, int position, long id)
{
   ShowDetails(position);
}
private void ShowDetails(int playId)
{
   _currentPlayId = playId;
   if (_isDualPane)
   {
       // We can display everything in place with fragments.
       // Have the list highlight this item and show the data.
       ListView.SetItemChecked(playId, true);
       // Check what fragment is shown, replace if needed.
       var details = FragmentManager.FindFragmentById(Resource.Id.details) as DetailsFragment;
       if (details == null || details.ShownPlayId != playId)
       {
           // Make new fragment to show this selection.
           details = DetailsFragment.NewInstance(playId);
           // Execute a transaction, replacing any existing
           // fragment with this one inside the frame.
           var ft = FragmentManager.BeginTransaction();
           ft.Replace(Resource.Id.details, details);
           ft.SetTransition(FragmentTransaction.TransitFragmentFade);
           ft.Commit();
       }
   }
   else
   {
       // Otherwise we need to launch a new Activity to display
       // the dialog fragment with selected text.
       var intent = new Intent();
       intent.SetClass(Activity, typeof (DetailsActivity));
       intent.PutExtra("current_play_id", playId);
       StartActivity(intent);
   }
}
```

程式碼會判斷從裝置如何格式化並顯示選取的播放引號。 在平板電腦的情況下`_isDualPane`旗標會設為`true`，使其旁邊會顯示引號， `TitlesFragment`。 如果選取的播放`id`尚未顯示，然後新`DetailsFragment`時建立，並接著載入至`FrameLayout`活動上。 針對沒有大型顯示其他裝置&ndash;手機，例如&ndash;`isDualPane`會設定為`false`讓新`DetailsActivity`會啟動。


## <a name="5-create-the-detailsactivity"></a>5.建立 DetailsActivity

`DetailsActivity`顯示`DetailsFragment`較小的裝置。 若要查看此問題，首先我們會加入新活動專案名為`DetailsActivity`。 `DetailsActivity` 是非常簡單的活動。 將建立並裝載 新`DetailsFragment`播放`id`，已傳送：

```csharp
[Activity(Label = "Details Activity")]
public class DetailsActivity : Activity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       var index = Intent.Extras.GetInt("current_play_id", 0);

       var details = DetailsFragment.NewInstance(index); // DetailsFragment.NewInstance is a factory method to create a Details Fragment
       var fragmentTransaction = FragmentManager.BeginTransaction();
       fragmentTransaction.Add(Android.Resource.Id.Content, details);
       fragmentTransaction.Commit();
   }
}
```

請注意，沒有配置檔已載入來`DetailsActivity`。 相反地，`DetailsFragment`會載入至活動的 [根] 檢視。 此根檢視具有特殊的識別碼`Android.Resource.Id.Content`。 新`DetailFragment`已建立並再加入到這個根目錄內的檢視`FragmentTransaction`，它由活動的`FragmentManager`。


## <a name="6-create-the-detailsfragment"></a>6.建立 DetailsFragment

現在，讓我們指定的應用程式中加入另一個片段`DetailsFragment`。 此片段會顯示所選的紙牌的引號。 下列程式碼顯示完整`DetailsFragment`:

```csharp
internal class DetailsFragment : Fragment
{
   public static DetailsFragment NewInstance(int playId)
   {
       var detailsFrag = new DetailsFragment {Arguments = new Bundle()};
       detailsFrag.Arguments.PutInt("current_play_id", playId);
       return detailsFrag;
   }
   public int ShownPlayId
   {
       get { return Arguments.GetInt("current_play_id", 0); }
   }
   public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
   {
       if (container == null)
       {
           // Currently in a layout without a container, so no reason to create our view.
           return null;
       }
       var scroller = new ScrollView(Activity);
       var text = new TextView(Activity);
       var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
       text.SetPadding(padding, padding, padding, padding);
       text.TextSize = 24;
       text.Text = Shakespeare.Dialogue[ShownPlayId];
       scroller.AddView(text);
       return scroller;
   }
}
```

為了讓`DetailsFragment`才能正常運作中，它必須具有播放 中選取的索引`TitlesFragment`。 有許多方式提供此值設為`DetailsFragment`; 在此範例中，播放`Id`放入組合和組合所儲存的執行個體的引數屬性`DetailsFragment`。 屬性`ShownPlayId`為了方便起見&ndash;的執行個體將使用`DetailsFragment`從配套擷取該值。

`OnCreateView` 此片段需要繪製其使用者介面，而且應該會傳回時，會呼叫`Android.Views.View`物件。 在大部分情況下，這是`View`膨脹從現有的版面配置檔案。 在上述範例中，片段會以程式設計方式建置將會用來顯示的檢視。

恭喜您！ 現在您已建立的應用程式使用簡化的開發工作之間的表單係數的片段。

在[下一節](supporting-pre-honeycomb.md)，我們將延伸這個應用程式，讓它將會在預先 Android 3.0 裝置上使用。

