---
title: ListView 效能
description: 雖然 ListView 是強大的檢視，來顯示資料，它會有一些限制。 這篇文章說明如何確保絕佳的效能與應用程式中的 Xamarin.Forms ListView。
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: ed920db129d2af203046a648c0069580f99a295e
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998185"
---
# <a name="listview-performance"></a>ListView 效能

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

在撰寫行動應用程式時，效能很重要。 使用者都是順暢的捲動和快速的載入時間。 無法符合使用者的期望，將會花費您的應用程式存放區中的評等，或在特定業務應用程式的情況下的成本您組織時間和金錢。

[Xamarin [`ListView`](xref:Xamarin.Forms.ListView) ] 是一個可顯示資料的強大視圖，但有一些限制。 使用自訂資料格時，滾動效能可能會受到影響，特別是當它們包含深度嵌套的視圖階層，或使用需要複雜測量的特定版面配置時。 幸好，有一些的技巧可以用來避免發生效能不佳。

## <a name="caching-strategy"></a>快取策略

Listview 通常用來顯示比螢幕上更多的資料。 例如，音樂應用程式可能會有一份具有數千個專案的歌曲庫。 為每個專案建立專案會浪費寶貴的記憶體，並執行得不佳。 經常建立和終結資料列需要應用程式不斷地具現化和清除物件，這也會執行得不佳。

為節省記憶體，每個[`ListView`](xref:Xamarin.Forms.ListView)平臺的原生對等專案都有內建功能可重複使用資料列。 只有儲存格畫面上看到載入記憶體中，**內容**載入現有的儲存格。 此模式可防止應用程式將上千個物件具現化，以節省時間和記憶體。

Xamarin 會允許[`ListView`](xref:Xamarin.Forms.ListView) [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy)透過列舉來重複使用資料格，其具有下列值：

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> 通用 Windows 平台 (UWP) 會忽略[ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)快取策略，因為它一律會使用快取來改善效能。 因此，依預設它的作用就如同[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)快取策略會套用。

### <a name="retainelement"></a>RetainElement

[ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)快取策略指定[ `ListView` ](xref:Xamarin.Forms.ListView)將會產生一個儲存格為每個項目在清單中，而且是預設`ListView`行為。 應用於下列情況：

- 每個資料格都有大量的系結（20-30 +）。
- 資料格範本經常變更。
- 測試顯示快取`RecycleElement`策略會導致執行速度降低。

請務必辨識的後果[ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)使用自訂的資料格時，快取策略。 任何儲存格初始化程式碼必須執行的每個資料格建立時，可能會多次每秒。 在這種情況下，頁面上的版面配置技巧（例如使用多個[`StackLayout`](xref:Xamarin.Forms.StackLayout)嵌套實例）會在使用者進行滾動時即時設定和終結時變成效能瓶頸。

### <a name="recycleelement"></a>RecycleElement

[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)快取策略指定[ `ListView` ](xref:Xamarin.Forms.ListView)會嘗試回收清單資料格，以減少其記憶體使用量和執行速度。 此模式不一定會提供效能改善，而且應該執行測試來判斷是否有任何改善。 不過，這是慣用的選擇，而且應該在下列情況下使用：

- 每個資料格都有一個小型到適中的系結數目。
- 每個單元[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)格都定義了所有資料格資料。
- 每個資料格主要類似，資料格範本不變。

在虛擬化的資料格會有更新，其繫結內容，所以如果應用程式使用此模式中它必須確保繫結內容更新適當地處理。 有關儲存格的所有資料都必須都來自繫結內容，或可能會發生一致性錯誤。 您可以使用資料系結來顯示儲存格資料，來避免這個問題。 或者，應該將資料格資料`OnBindingContextChanged`覆寫，而不是在自訂儲存格的建構函式，如下列程式碼範例所示：

```csharp
public class CustomCell : ViewCell
{
    Image image = null;
    
    public CustomCell ()
    {
        image = new Image();
        View = image;
    }
    
    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();
        
        var item = BindingContext as ImageItem;
        if (item != null) {
            image.Source = item.ImageUrl;
        }
    }
}
```

如需詳細資訊，請參閱 <<c0> [ 繫結的內容變更](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes)。

在 iOS 和 Android 上，如果儲存格使用的自訂轉譯器，它們必須確定已正確地實作屬性變更通知。 重複使用的儲存格時其屬性值，將繫結內容更新時所使用的資料格，`PropertyChanged`所引發的事件。 如需詳細資訊，請參閱 <<c0> [ 自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

#### <a name="recycleelement-with-a-datatemplateselector"></a>使用 DataTemplateSelector RecycleElement

當[ `ListView` ](xref:Xamarin.Forms.ListView)會使用[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)選取[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，則[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)快取並不會快取策略`DataTemplate`s。 相反地，`DataTemplate`選取每個項目清單中的資料。

> [!NOTE]
> [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)快取策略有必要條件，Xamarin.Forms 2.4 中導入，當[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)會要求選取[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)每個`DataTemplate`必須傳回相同[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)型別。 例如，假設[ `ListView` ](xref:Xamarin.Forms.ListView)具有`DataTemplateSelector`，可傳回`MyDataTemplateA`(其中`MyDataTemplateA`傳回`ViewCell`型別的`MyViewCellA`)，或`MyDataTemplateB`(其中`MyDataTemplateB`會傳回`ViewCell`型別的`MyViewCellB`)，當`MyDataTemplateA`會傳回它必須傳回`MyViewCellA`，否則會擲回例外狀況。

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

[ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate)快取策略是根據[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)此外確保，當快取策略[ `ListView` ](xref:Xamarin.Forms.ListView)使用[`DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)來選取[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)， `DataTemplate`s 會快取在清單中的項目類型。 因此， `DataTemplate`s 每個項目類型，而不是每個項目執行個體一次選取一次。

> [!NOTE]
> [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate)快取策略有必要，`DataTemplate`所傳回的 s [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)必須使用[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type))使用建構函式`Type`。

### <a name="set-the-caching-strategy"></a>設定快取策略

[ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy)列舉值指定[ `ListView` ](xref:Xamarin.Forms.ListView)建構函式多載，如下列程式碼範例所示：

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

在 xaml 中，設定`CachingStrategy`屬性，如下列 xaml 所示：

```xaml
<ListView CachingStrategy="RecycleElement">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
              ...
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

這個方法與在的函式中C#設定快取策略引數的效果相同。

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>在子類別化的 ListView 中設定快取策略

在子類別化的 XAML 上設定`CachingStrategy` `ListView`屬性不 `CachingStrategy` [會產生所需`ListView`](xref:Xamarin.Forms.ListView)的行為，因為上沒有屬性。 此外，如果啟用[XAMLC](~/xamarin-forms/xaml/xamlc.md) ，將會產生下列錯誤訊息：**找不到 ' CachingStrategy ' 的屬性、可系結屬性或事件**

此問題的解決方案是指定子類別化之建構函式[ `ListView` ](xref:Xamarin.Forms.ListView)它會接受[ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy)參數並將它傳遞到基底類別：

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

然後[ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy)列舉值，可以從指定 XAML 使用`x:Arguments`語法：

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>ListView 效能建議

有許多技巧可改善的效能`ListView`。 下列建議可改善 ListView 的效能

- 繫結`ItemsSource`屬性，以`IList<T>`而不是集合`IEnumerable<T>`集合，因為`IEnumerable<T>`集合不支援隨機存取。
- 使用內建`TextCell`資料格（例如 /  `SwitchCell` ），而不`ViewCell`是每次都可以。
- 使用較少的項目。 例如，請考慮使用單一`FormattedString`標籤，而不是多個標籤。
- 取代`ListView`與`TableView`顯示非同質性的資料-也就是不同類型的資料。
- 使用限制[ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法。 如果過度使用而使，它會降低效能。
- 在 Android 上，請避免設定`ListView`的資料列分隔符號的可見性或色彩之後執行個體化，因為它會造成大量的效能負面影響。
- 避免變更為基礎的儲存格配置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)。 變更版面配置會產生大量的測量和初始化成本。
- 應避免階層深度巢狀版面配置。 使用`AbsoluteLayout`或`Grid`以減少巢狀結構。
- 避免以外`LayoutOptions` `Fill`的特定（`Fill`這是計算的最便宜）。
- 避免在放置`ListView`內`ScrollView`，原因如下：
  - `ListView`實作自己的捲動。
  - `ListView` ，將會由父代，則不會收到任何筆勢， `ScrollView`。
  - `ListView`可以呈現的自訂標頭和頁尾捲動清單中，可能會提供功能的項目`ScrollView`有人使用了。 如需詳細資訊，請參閱頁首[和](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers)頁尾。
- 如果您需要在儲存格中呈現特定的複雜設計，請考慮使用自訂轉譯器。

`AbsoluteLayout`有可能在沒有單一量值呼叫的情況下執行版面配置，使其具有高效能。 如果`AbsoluteLayout`無法使用，請考慮[ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout)。 如果使用`RelativeLayout`，直接傳遞條件約束相當大的速度會比使用 API 的運算式。 此方法的速度較快，因為運算式 API 使用 JIT，而在 iOS 上必須解讀樹狀結構，這會較慢。 API 運算式是適合的版面配置，它只需要在初始的版面配置與輪替，但在`ListView`，其中執行持續期間向下捲動，它都會損及效能。

建置自訂轉譯器[ `ListView` ](xref:Xamarin.Forms.ListView)或它的儲存格是一個方法，來減少版面配置計算上捲動效能的影響。 如需詳細資訊，請參閱 <<c0> [ 自訂 ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)並[自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

## <a name="related-links"></a>相關連結

- [自訂轉譯器檢視 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [自訂轉譯器 ViewCell （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
