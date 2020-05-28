---
title: ''
description: 雖然 ListView 是顯示資料的功能強大的視圖，但還是有一些限制。 本文說明如何使用應用程式中的 ListView 確保絕佳的效能 Xamarin.Forms 。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2b8e057d9687cd0a472451fc73cc578f9358277
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139876"
---
# <a name="listview-performance"></a>ListView 效能

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

在撰寫行動應用程式時，效能很重要。 使用者預期會有順暢的滾動和快速的載入時間。 若無法符合使用者的期望，將會對應用程式商店中的評等收費，或在企業營運應用程式的情況下，為您的組織時間和金錢收費。

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 是用來顯示資料的強大觀點，但有一些限制。 使用自訂資料格時，滾動效能可能會受到影響，特別是當它們包含深度嵌套的視圖階層，或使用需要複雜測量的特定版面配置時。 幸運的是，有一些技巧可以用來避免效能不佳。

## <a name="caching-strategy"></a>快取策略

Listview 通常用來顯示比螢幕上更多的資料。 例如，音樂應用程式可能會有一份具有數千個專案的歌曲庫。 為每個專案建立專案會浪費寶貴的記憶體，並執行得不佳。 經常建立和終結資料列需要應用程式不斷地具現化和清除物件，這也會執行得不佳。

為節省記憶體，每個平臺的原生對等專案 [`ListView`](xref:Xamarin.Forms.ListView) 都有內建功能可重複使用資料列。 只有畫面上可見的儲存格會載入記憶體中，並將**內容**載入至現有的資料格。 此模式可防止應用程式將上千個物件具現化，以節省時間和記憶體。

Xamarin.Forms允許 [`ListView`](xref:Xamarin.Forms.ListView) 透過列舉的資料格重複使用 [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) ，其具有下列值：

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> 通用 Windows 平臺（UWP）會忽略快取 [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) 策略，因為它一律會使用快取來改善效能。 因此，根據預設，它的行為會如同套用快取 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 策略一樣。

### <a name="retainelement"></a>RetainElement

快取 [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) 策略指定 [`ListView`](xref:Xamarin.Forms.ListView) 將會為清單中的每個專案產生一個儲存格，而是預設 `ListView` 行為。 應用於下列情況：

- 每個資料格都有大量的系結（20-30 +）。
- 資料格範本經常變更。
- 測試顯示快取 `RecycleElement` 策略會導致執行速度降低。

使用自訂資料格時，請務必辨識快取策略的結果 [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) 。 所有的資料格初始化程式碼都必須針對每個資料格建立執行，每秒可能會多次。 在這種情況下，頁面上的版面配置技巧（例如使用多個嵌套 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 實例）會在使用者進行滾動時即時設定和終結時變成效能瓶頸。

### <a name="recycleelement"></a>RecycleElement

快取 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 策略指定 [`ListView`](xref:Xamarin.Forms.ListView) 將會嘗試藉由回收清單儲存格，將其記憶體使用量降到最低和執行速度。 此模式不一定會提供效能改善，而且應該執行測試來判斷是否有任何改善。 不過，這是慣用的選擇，而且應該在下列情況下使用：

- 每個資料格都有一個小型到適中的系結數目。
- 每個儲存格都 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 定義了所有資料格資料。
- 每個資料格主要類似，資料格範本不變。

在虛擬化期間，資料格會更新其系結內容，因此，如果應用程式使用此模式，它必須確保系結內容更新會適當地處理。 儲存格的所有資料都必須來自系結內容，否則可能會發生一致性錯誤。 您可以使用資料系結來顯示儲存格資料，來避免這個問題。 或者，您應該在覆寫中設定儲存格資料 `OnBindingContextChanged` ，而不是在自訂儲存格的「函數」中，如下列程式碼範例所示：

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

如需詳細資訊，請參閱系結[內容變更](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes)。

在 iOS 和 Android 上，如果資料格使用自訂轉譯器，則必須確定已正確地執行屬性變更通知。 當重複使用資料格時，其屬性值將會在系結內容更新為可用儲存格的時變更，並 `PropertyChanged` 引發事件。 如需詳細資訊，請參閱[自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

#### <a name="recycleelement-with-a-datatemplateselector"></a>具有 DataTemplateSelector 的 RecycleElement

當 [`ListView`](xref:Xamarin.Forms.ListView) 使用來選取時，快取策略不會快取 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) `DataTemplate` s。 相反地， `DataTemplate` 會針對清單中的每個資料項目目選取。

> [!NOTE]
> 快取 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 策略具有2.4 中引進的必要條件， Xamarin.Forms 當 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 系統要求您選取 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 每個 `DataTemplate` 必須傳回相同 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 類型的時，就會發生此情況。 例如，假設有一個 [`ListView`](xref:Xamarin.Forms.ListView) 可以傳回的 `DataTemplateSelector` `MyDataTemplateA` （其中會 `MyDataTemplateA` `ViewCell` 傳回類型的 `MyViewCellA` ），或 `MyDataTemplateB` （其中會 `MyDataTemplateB` `ViewCell` 傳回類型的），則 `MyViewCellB` 當傳回時， `MyDataTemplateA` 它必須傳回，否則會擲回 `MyViewCellA` 例外狀況。

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

快取策略是以快取 [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) 策略為基礎， [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 此外還能確保當 [`ListView`](xref:Xamarin.Forms.ListView) 使用 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 來選取時 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ， `DataTemplate` 清單中的專案類型會快取 s。 因此， `DataTemplate` 會針對每個專案類型選取一次，而不是每個專案實例一次。

> [!NOTE]
> 快取策略的必要條件是，所 [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) 傳回的 `DataTemplate` [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 必須使用 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) 接受的函式 `Type` 。

### <a name="set-the-caching-strategy"></a>設定快取策略

[`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy)列舉值是以函式多載指定 [`ListView`](xref:Xamarin.Forms.ListView) ，如下列程式碼範例所示：

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

在 XAML 中，設定 `CachingStrategy` 屬性，如下列 xaml 所示：

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

這個方法與在 c # 中的函式中設定快取策略引數的效果相同。

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>在子類別化的 ListView 中設定快取策略

在子類別化的 `CachingStrategy` XAML 上設定屬性 [`ListView`](xref:Xamarin.Forms.ListView) 不會產生所需的行為，因為 `CachingStrategy` 上沒有屬性 `ListView` 。 此外，如果啟用[XAMLC](~/xamarin-forms/xaml/xamlc.md) ，將會產生下列錯誤訊息：**找不到 ' CachingStrategy ' 的屬性、可系結屬性或事件**

這個問題的解決方法是在子類別上指定可 [`ListView`](xref:Xamarin.Forms.ListView) 接受 [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) 參數並將它傳遞至基類的函式：

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

然後，您 [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) 可以使用語法，從 XAML 指定列舉值 `x:Arguments` ：

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>ListView 效能建議

有許多技巧可改善的效能 `ListView` 。 下列建議可改善 ListView 的效能

- 將屬性系結 `ItemsSource` 至 `IList<T>` 集合，而不是 `IEnumerable<T>` 集合，因為 `IEnumerable<T>` 集合不支援隨機存取。
- 使用內建資料格（例如 `TextCell`  /  `SwitchCell` ），而不是 `ViewCell` 每次都可以。
- 使用較少的元素。 例如，請考慮使用單一 `FormattedString` 標籤，而不是多個標籤。
- `ListView` `TableView` 顯示非同質資料時（也就是不同類型的資料），將取代為。
- 限制使用 [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) 方法。 如果過度用，將會降低效能。
- 在 Android 上，請避免在具 `ListView` 現化後設定的資料列分隔符號可見度或色彩，因為這會導致效能大幅下降。
- 避免變更以為基礎的資料格版面配置 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 變更版面配置會產生大量的測量和初始化成本。
- 避免深度嵌套的版面配置階層。 使用 `AbsoluteLayout` 或 `Grid` 可協助減少嵌套。
- 避免 `LayoutOptions` 以外的特定 `Fill` （ `Fill` 這是計算的最便宜）。
- 基於 `ListView` 下列原因，請避免將放在中 `ScrollView` ：
  - 會 `ListView` 實行自己的滾動。
  - `ListView`將不會收到任何手勢，因為它們會由父系處理 `ScrollView` 。
  - `ListView`可以呈現具有清單元素的自訂頁首和頁尾，這可能會提供所使用的功能 `ScrollView` 。 如需詳細資訊，請參閱頁首[和](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers)頁尾。
- 如果您需要在儲存格中呈現特定的複雜設計，請考慮使用自訂轉譯器。

`AbsoluteLayout`有可能在沒有單一量值呼叫的情況下執行版面配置，使其具有高效能。 如果 `AbsoluteLayout` 無法使用，請考慮 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 。 如果使用 `RelativeLayout` ，直接傳遞條件約束會比使用運算式 API 快得多。 此方法的速度較快，因為運算式 API 使用 JIT，而在 iOS 上必須解讀樹狀結構，這會較慢。 Expression API 適用于只有初始版面配置和旋轉才需要的頁面配置，但是在中，它會在 `ListView` 滾動期間持續執行，因此會影響效能。

建立或其資料格的自訂轉譯器 [`ListView`](xref:Xamarin.Forms.ListView) ，是降低版面配置計算在滾動效能上的影響的一種方法。 如需詳細資訊，請參閱[自訂 ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)和[自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

## <a name="related-links"></a>相關連結

- [自訂轉譯器視圖（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [自訂轉譯器 ViewCell （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
