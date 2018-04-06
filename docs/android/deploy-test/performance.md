---
title: Xamarin.Android 效能
description: 有許多技巧可增加利用 Xamarin.Android 建置之應用程式的效能。 這些技巧可共同大幅減少 CPU 所執行的工作量和應用程式所耗用的記憶體數量。 本文將描述並討論這些技巧。
ms.prod: xamarin
ms.assetid: dc2e27f2-7f71-4d57-9cf9-165528276613
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: a22190adc97cb80f5900dda4a073bdcdf80ef99b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinandroid-performance"></a>Xamarin.Android 效能

_有許多技巧可增加利用 Xamarin.Android 來建置應用程式的效能。這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。本文將描述並討論這些技巧。_

## <a name="performance-overview"></a>效能概觀

不佳的應用程式效能會以許多方式表現。 它可能會讓應用程式看起來沒有回應、造成捲動緩慢，以及減少電池壽命。 不過，最佳化效能不僅僅只牽涉到實作有效率的程式碼而已。 同時也必須考量使用者對於應用程式效能的體驗。 例如，確保作業能在不封鎖使用者執行其他活動的情況下執行，將可以協助改善使用者體驗。

有一些技巧可以用來增加利用 Xamarin.Android 建置之應用程式的效能與認知效能。 包括：

- [最佳化配置階層](#optimizelayout)
- [最佳化清單檢視](#optimizelistviews)
- [移除活動中的事件處理常式](#removeeventhandlers)
- [限制服務的存留時間](#limitservices)
- [收到通知時釋放資源](#releaseresources)
- [隱藏使用者介面時釋放資源](#releaseresourcesuihidden)
- [最佳化影像資源](#optimizeimages)
- [未使用的影像資源處置](#disposeimages)
- [避免浮點算術](#avoidfloats)
- [關閉對話方塊](#dismissdialogs)


> [!NOTE]
> 在閱讀本文之前，您應該先閱讀[跨平台效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)，其中探討可改善記憶體使用情況的非平台專用技術，以及使用 Xamarin 平台建置之應用程式的效能。

<a name="optimizelayout" />

## <a name="optimize-layout-hierarchies"></a>最佳化配置階層

每個新增至應用程式的配置都需要初始化、配置和繪圖。 巢狀使用 `weight` 參數的 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 執行個體時，由於每個子系都會測量兩次，因此配置階段可能很耗費資源。 使用巢狀的 `LinearLayout` 執行個體可能會形成深層檢視階層，進而因配置擴大多次 (例如在 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) 中) 而導致效能不佳。 因此，請務必最佳化這類配置，如此一來才能加倍提升效能。

例如，假設以 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 來表示具有圖示、標題和描述的清單檢視資料列。 `LinearLayout` 會包含 [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) 以及含有兩個 [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 執行個體的垂直 `LinearLayout`：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="0dip"
        android:layout_weight="1"
        android:layout_height="fill_parent">
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:gravity="center_vertical"
            android:text="Mei tempor iuvaret ad." />
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="0dip"
            android:layout_weight="1"
            android:singleLine="true"
            android:ellipsize="marquee"
            android:text="Lorem ipsum dolor sit amet." />
    </LinearLayout>
</LinearLayout>
```

此配置有 3 層深，當針對每個 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) 資料列擴大時會很浪費資源。 不過，您可以將配置壓平合併來改善，如下列程式碼範例所示：

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:padding="5dip">
    <ImageView
        android:id="@+id/icon"
        android:layout_width="wrap_content"
        android:layout_height="fill_parent"
        android:layout_alignParentTop="true"
        android:layout_alignParentBottom="true"
        android:layout_marginRight="5dip"
        android:src="@drawable/icon" />
    <TextView
        android:id="@+id/secondLine"
        android:layout_width="fill_parent"
        android:layout_height="25dip"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"
        android:singleLine="true"
        android:ellipsize="marquee"
        android:text="Lorem ipsum dolor sit amet." />
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/icon"
        android:layout_alignParentRight="true"
        android:layout_alignParentTop="true"
        android:layout_above="@id/secondLine"
        android:layout_alignWithParentIfMissing="true"
        android:gravity="center_vertical"
        android:text="Mei tempor iuvaret ad." />
</RelativeLayout>
```

先前的 3 層階層已減少為 2 層階層，並由單一 [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) 取代兩個 [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 執行個體。 當針對每個 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) 資料列擴大此配置時，將會大幅提升效能。

<a name="optimizelistviews" />

## <a name="optimize-list-views"></a>最佳化清單檢視

針對 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) 執行個體，使用者期望有順暢的捲動和快速載入時間。 不過，當每個清單檢視資料列包含深層巢狀檢視階層，或清單檢視資料列包含複雜配置時，捲動效能可能會受到影響。 不過，有可用來避免 `ListView` 效能不佳的技巧：

- 重複使用資料列檢視。如需詳細資訊，請參閱[重複使用資料列檢視](#reuserowviews)。
- 盡可能將配置壓平合併。
- 快取擷取自 Web 服務的資料列內容。
- 避免影像縮放。

這些技巧可共同協助保持 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) 執行個體捲動順暢。

<a name="reuserowviews" />

### <a name="reuse-row-views"></a>重複使用資料列檢視

當顯示 [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) 中數百個資料列，畫面上一次僅能顯示少數資料列時，卻建立了數百個 [`View`](https://developer.xamarin.com/api/type/Android.Views.View/) 物件，是一種浪費記憶體的方式。 反之，僅有畫面上資料列中看見的 `View` 物件可以載入記憶體，且**內容**載入至這些重複使用的物件中。 這可防止具現化數百個額外的物件，進而節省時間與記憶體。

因此，當資料列從畫面消失時，其檢視可以放置在佇列中以供重複使用，如下列程式碼範例所示：

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

當使用者捲動時，[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) 會呼叫 `GetView` 覆寫來要求顯示新的檢視；如果有的話，則會在 `convertView` 參數中傳遞未使用的檢視。 如果此值為 `null`，則程式碼會建立新的 [`View`](https://developer.xamarin.com/api/type/Android.Views.View/) 執行個體；否則即可重設和重複使用 `convertView` 屬性。

如需詳細資訊，請參閱[將資料填入 ListView](~/android/user-interface/layouts/list-view/populating.md) 中的[資料列檢視重複使用](~/android/user-interface/layouts/list-view/populating.md#row-view-re-use)。

<a name="removeeventhandlers" />

## <a name="remove-event-handlers-in-activities"></a>移除活動中的事件處理常式

當 Android 執行階段中的某個活動終結時，它仍然會存留在 Mono 執行階段中。 因此，請移除 `Activity.OnPause` 中外部物件的事件處理常式，以防止執行階段保留已終結活動的參考。

在活動中，於類別層級宣告事件處理常式：

```csharp
EventHandler<UpdatingEventArgs> service1UpdateHandler;
```

然後實作活動中的處理常式，例如在 `OnResume` 中：

```csharp
service1UpdateHandler = (object s, UpdatingEventArgs args) => {
    this.RunOnUiThread (() => {
        this.updateStatusText1.Text = args.Message;
    });
};
App.Current.Service1.Updated += service1UpdateHandler;
```

當活動結束執行狀態時，會呼叫 `OnPause`。 在 `OnPause` 實作中，如下所示移除處理常式：

```csharp
App.Current.Service1.Updated -= service1UpdateHandler;
```

<a name="limitservices" />

## <a name="limit-the-lifespan-of-services"></a>限制服務的存留時間

當服務啟動時，Android 會持續執行服務處理序。 這會導致處理序佔用大量資源，因為無法對其記憶體進行分頁或用在其他地方。 讓不需要的服務持續執行會提高應用程式因記憶體限制而導致效能不佳的風險。 由於 Android 可以快取的處理序數目變少，它也可能會使應用程式切換速率降低。

您可以使用 `IntentService` 來限制服務的存留時間，讓服務在處理啟動意圖之後自行終止。

<a name="releaseresources" />

## <a name="release-resources-when-notified"></a>收到通知時釋放資源

在應用程式生命週期期間，[`OnTrimMemory`](https://developer.xamarin.com/api/member/Android.App.Activity.OnTrimMemory/p/Android.Content.TrimMemory/) 回呼會在裝置記憶體偏低時提供通知。 此回呼應該實作為接聽下列記憶體等級通知：

- [`TrimMemoryRunningModerate`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryRunningModerate/) - 應用程式「可能」想要釋放部分不需要的資源。
- [`TrimMemoryRunningLow`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryRunningLow/) - 應用程式「應該」釋放不需要的資源。
- [`TrimMemoryRunningCritical`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryRunningCritical/) - 應用程式「應該」盡可能釋放許多非關鍵的處理序。

此外，快取應用程式處理序時，[`OnTrimMemory`](https://developer.xamarin.com/api/member/Android.App.Activity.OnTrimMemory/p/Android.Content.TrimMemory/) 回呼可能會收到下列記憶體等級通知：

- [`TrimMemoryBackground`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryBackground/) - 釋放可在使用者返回應用程式時快速有效率地重建的資源。
- [`TrimMemoryModerate`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryModerate/) - 釋放可協助系統繼續快取其他處理序的資源，以提升整體效能。
- [`TrimMemoryComplete`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryComplete/) - 如果沒有盡快復原更多記憶體，會立即終止應用程式處理序。

您應該根據收到的等級釋放資源，來回應這些通知。

<a name="releaseresourcesuihidden" />

## <a name="release-resources-when-the-user-interface-is-hidden"></a>隱藏使用者介面時釋放資源

當使用者巡覽至其他應用程式時，請釋放應用程式使用者介面所使用的任何資源，這樣做可大幅增加 Android 用於快取處理序的容量，進而影響使用者體驗品質。

若要在使用者結束 UI 時收到通知，請在 `Activity` 類別實作 [`OnTrimMemory`](https://developer.xamarin.com/api/member/Android.App.Activity.OnTrimMemory/p/Android.Content.TrimMemory/) 回呼，並接聽 [`TrimMemoryUiHidden`](https://developer.xamarin.com/api/field/Android.Content.ComponentCallbacks2.TrimMemoryUiHidden/) 層級 (此層級表示已隱藏 UI)。 只有在使用者看不到應用程式的「所有」UI 元件時，才會收到此通知。 收到此通知即釋放 UI 資源可確保當使用者從應用程式的另一個活動往回巡覽時，這些 UI 資源仍然可供使用，以便快速地繼續活動。

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>最佳化影像資源

影像是應用程式所使用之資源中成本最高的一種資源，且經常以高解析度擷取。 因此，當顯示影像時，請以裝置螢幕所需的解析度來顯示。 如果影像解析度比螢幕解析度還要高，則應該將它相應減少。

如需詳細資訊，請參閱[跨平台效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)指南中的[最佳化影像資源](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)。

<a name="disposeimages" />

## <a name="dispose-of-unused-image-resources"></a>未使用的影像資源處置

為了節省記憶體使用量，最好對不再需要的大型影像資源進行處置。 不過，請務必確保正確地處置影像。 不要使用明確的 `.Dispose()` 引動過程，請改用 [using](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/using-statement) 陳述式以確保正確使用 `IDisposable` 物件。 

例如，[Bitmap](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/) 類別會實作 `IDisposable`。 將 `BitMap` 物件的具現化包裝在 `using` 區塊中，可確保從區塊結束時會正確處置物件：

```csharp
using (Bitmap smallPic = BitmapFactory.DecodeByteArray(smallImageByte, 0, smallImageByte.Length))
{
    // Use the smallPic bit map here
}
```

如需釋放可處置資源的詳細資訊，請參閱[釋放 IDisposable 資源](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable)。  


<a name="avoidfloats" />

## <a name="avoid-floating-point-arithmetic"></a>避免浮點算術

在 Android 裝置上，浮點算術比整數算術大約慢 2 倍。 因此，請盡可能以整數算術取代浮點算術。 不過，在最新硬體上，`float` 與 `double` 算術的執行時間並沒有差別。

> [!NOTE]
> 即使是整數算術，某些 CPU 仍缺少硬體除法功能。 因此，整數除法與模數運算通常會在軟體中執行。

<a name="dismissdialogs" />

## <a name="dismiss-dialogs"></a>關閉對話方塊

使用 [`ProgressDialog`](https://developer.xamarin.com/api/type/Android.App.ProgressDialog/) 類別 (或是任何對話方塊或警示) 時，不要在對話方塊目的完成時呼叫 [`Hide`](https://developer.xamarin.com/api/member/Android.App.Dialog.Hide()/) 方法，而是改為呼叫 [`Dismiss`](https://developer.xamarin.com/api/member/Android.App.Dialog.Dismiss()/) 方法。 否則，對話方塊仍會保持運作，而且會因持有活動的參考而導致活動洩漏。

## <a name="summary"></a>總結

本文已描述與討論用來增加以 Xamarin.Android 建置之應用程式效能的技巧。 這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。


## <a name="related-links"></a>相關連結

- [跨平台效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)
