---
title: ListView 效能
description: 請與您在清單檢視為基礎的應用程式的高效能。
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: dcd4881e2ad7f1bb4af5455805da1dd2cade3605
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="listview-performance"></a>ListView 效能

在撰寫行動裝置應用程式時，效能將很重要。 使用者預期會有的平滑捲動和快速載入時間。 無法符合使用者的期望，將會成本您的應用程式存放區中的評等，或在特定業務應用程式的成本您組織的時間和金錢。

雖然[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)是功能強大的檢視來顯示資料，有一些限制。 捲動效能可能會使用自訂的資料格，尤其是在包含深的巢狀檢視階層，或使用某些需要大量的度量的版面配置時。 幸運的是，有的技術可用來避免效能不佳。

本文章中討論下列主題：

- **[快取策略](#cachingstrategy)**
- **[改善 ListView 效能](#improving-performance)**

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>快取策略

Listview 通常用來顯示更多的資料，比可以調整螢幕上。 如需範例，請考慮音樂應用程式。 歌曲的媒體櫃可能有數千個項目。 最簡單的方式，就是建立一個資料列的所有歌曲，就必須效能不佳。 該方法會浪費寶貴的記憶體，並減慢向下捲動到搜耙。 另一個方法是建立和終結的資料列，因為資料捲動到檢視。 這需要常數具現化和清除可能會非常慢的檢視物件。

若要節省記憶體，原生[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)每個平台的對等項目已重複使用資料列的內建功能。 載入記憶體中的儲存格螢幕上看見和**內容**會載入至現有的資料格。 這可防止應用程式就不需要具現化數千個物件，節省時間和記憶體。

Xamarin.Forms 可讓[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)儲存格重複使用透過[ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)列舉，具有下列值：

```csharp
public enum ListViewCachingStrategy
{
  RetainElement,   // the default value
  RecycleElement,
  RecycleElementAndDataTemplate
}
```

> [!NOTE]
> 通用 Windows 平台 (UWP) 會忽略[ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/)快取策略，因為它一律會使用快取來改善效能。 因此依預設它的行為如同[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)快取策略會套用。

### <a name="retainelement"></a>RetainElement

[ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/)快取策略指定[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)會產生一個儲存格為每個項目在清單中，而且是預設值`ListView`行為。 通常應該在下列情況下使用：

- 當每個儲存格有大量的繫結 (20-30 +)。
- 當儲存格樣板可以經常變更了。
- 當測試顯示，`RecycleElement`快取策略導致降低的執行速度。

請務必辨識的後果[ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/)使用自訂的儲存格時，快取策略。 任何儲存格初始化程式碼必須執行的每個資料格建立時，可能會多次每秒。 在此情況下，已在頁面上，好的版面配置技巧想使用多個巢狀[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)執行個體，在安裝時成為效能瓶頸和捲動即時損毀。

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)快取策略指定[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)會嘗試回收清單資料格來減少其記憶體使用量，並執行速度。 此模式中永遠不提供改進效能，並在執行測試時需特別以判斷任何改進。 不過，通常是較適合的選擇，應在下列情況：

- 當每個資料格具有小型至適量的繫結。
- 當每個資料格[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)定義所有的資料格資料。
- 每個資料格時大致相似，與儲存格樣板不變。

在虛擬化的資料格會有更新，其繫結內容，所以如果應用程式使用此模式中它必須確保正確地處理繫結內容更新。 儲存格的相關的所有資料都必須都來自繫結內容，或可能會發生一致性錯誤。 這可以使用資料繫結來顯示資料格資料完成。 或者，設定資料格資料`OnBindingContextChanged`覆寫，而不是在自訂儲存格的建構函式，如下列程式碼範例所示：

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

如需詳細資訊，請參閱[繫結的內容變更](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes)。

在 iOS 和 Android，如果資料格都使用自訂轉譯器而言，它們必須確定已正確地實作屬性變更告知。 重複使用的資料格時繫結內容所使用的資料格，以更新時，會變更其屬性值`PropertyChanged`所引發的事件。 如需詳細資訊，請參閱[自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

#### <a name="recycleelement-with-a-datatemplateselector"></a>與 DataTemplateSelector RecycleElement

當[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)使用[ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)選取[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)、 [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)快取並不會快取策略`DataTemplate`s。 相反地，`DataTemplate`選取每個項目的清單中的資料。

> [!NOTE]
> [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)快取策略有必要，Xamarin.Forms 2.4 中導入，當[ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)提示選取[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)每個`DataTemplate`必須傳回相同[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)型別。 例如，假設[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)與`DataTemplateSelector`，可傳回`MyDataTemplateA`(其中`MyDataTemplateA`傳回`ViewCell`型別的`MyViewCellA`)，或`MyDataTemplateB`(其中`MyDataTemplateB`傳回`ViewCell`型別的`MyViewCellB`)，當`MyDataTemplateA`會傳回它必須傳回`MyViewCellA`，否則會擲回例外狀況。

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

[ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/)快取策略是根據[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)此外確保，當快取策略[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)使用[`DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)選取[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)， `DataTemplate`s 所快取的清單中的項目類型。 因此， `DataTemplate`s 每個項目類型，而不是每個項目執行個體一次選取一次。

> [!NOTE]
> [ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/)快取策略具有必要的`DataTemplate`所傳回的 s [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)必須使用[ `DataTemplate` ](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/)使用建構函式`Type`。

### <a name="setting-the-caching-strategy"></a>設定快取策略

[ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)與指定的列舉值[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)建構函式多載，如下列程式碼範例所示：

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

在 XAML 中，設定`CachingStrategy`屬性，如下列程式碼所示：

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

這會有相同的效果與在 C# 中，建構函式中設定快取策略引數請注意，沒有任何`CachingStrategy`屬性`ListView`。

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>在 子類別化清單檢視中設定快取策略

設定`CachingStrategy`屬性從 XAML 子類別化[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)將不會產生所要的行為，因為沒有任何`CachingStrategy`屬性`ListView`。 此外，如果[XAMLC](~/xamarin-forms/xaml/xamlc.md)已啟用，出現下列錯誤訊息將會產生：**沒有屬性，可繫結的屬性或找到 'CachingStrategy' 的事件**

此問題的解決方案為指定子類別化建構函式[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)接受[ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)參數並將其傳遞至基底類別：

```csharp
public class CustomListView : ListView
{
  public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
  {
  }
  ...
}
```

然後在[ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)列舉值，可以從指定 XAML 使用`x:Arguments`語法：

```xaml
<local:CustomListView>
  <x:Arguments>
    <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
  </x:Arguments>
</local:CustomListView>
```

<a name="improving-performance" />

## <a name="improving-listview-performance"></a>改善 ListView 效能

有許多技巧可改善效能的`ListView`:

-  繫結`ItemsSource`屬性`IList<T>`集合，而不是`IEnumerable<T>`集合，因為`IEnumerable<T>`集合不支援隨機存取。
-  使用內建的儲存格 (例如`TextCell`  /  `SwitchCell` ) 而不是`ViewCell`您可以在每當。
-  使用較少的項目。 如需範例，請考慮使用單一`FormattedString`而不是多個標籤的標籤。
-  取代`ListView`與`TableView`顯示非同質性資料 – 也就是不同類型的資料時。
-  限制使用[ `Cell.ForceUpdateSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.ForceUpdateSize()/)方法。 如果過度使用而使，它會降低效能。
-  在 Android 上，避免設定`ListView`的資料列分隔符號的可見性或色彩之後執行個體化，因為它會造成大量的效能負面影響。
-  請避免變更為基礎的資料格配置[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)。 這會產生大量的配置和初始化成本。
-  應避免階層深度巢狀版面配置。 使用`AbsoluteLayout`或`Grid`以減少巢狀結構。
-  避免特定`LayoutOptions`以外`Fill`（填滿是成本來計算）。
-  避免將`ListView`內`ScrollView`，原因如下：
  - `ListView`實作自己的捲動。
  - `ListView`不接收它們將父系所處理的任何放軌跡， `ScrollView`。
  - `ListView`可以與潛在供應項目功能的清單項目呈現的自訂的頁首和頁尾捲動`ScrollView`用於。 如需詳細資訊，請參閱[頁首和頁尾](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers)。
-  如果您需要非常特定的複雜的設計中儲存格顯示，請考慮自訂轉譯器。

`AbsoluteLayout` 就可能會執行配置而沒有單一量值呼叫。 這可讓您非常強大的效能。 如果`AbsoluteLayout`不能使用，請考慮[ `RelativeLayout` ](http://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)。 如果使用`RelativeLayout`，直接傳遞條件約束將會比使用應用程式開發介面的運算式更快速。 這是因為運算式 API 使用 JIT，並在 iOS 上樹狀結構具有解譯，這是較慢。 應用程式開發介面的運算式是適合的版面配置，它只需要上初始配置和輪替，而`ListView`，其中執行持續期間向下捲動，它都會損及效能。

建立自訂轉譯器[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)或其儲存格是一種方法來減少配置計算捲動效能的影響。 如需詳細資訊，請參閱[自訂 ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)和[自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。


## <a name="related-links"></a>相關連結

- [自訂轉譯器檢視 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [自訂轉譯器 ViewCell （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
- [ListViewCachingStrategy](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)
