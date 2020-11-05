---
title: ListView 效能
description: 雖然 ListView 是顯示資料的強大觀點，但是有一些限制。 本文說明如何使用應用程式中的 ListView 確保絕佳的效能 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 98f91ec614df08997e7cbf23530086f541500b5d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375469"
---
# <a name="listview-performance"></a>ListView 效能

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

撰寫行動應用程式時，效能很重要。 使用者預期會有順暢的滾動和快速載入時間。 若無法符合您使用者的期望，將會對應用程式商店中的評等產生費用，或在企業營運應用程式的案例中，成本您的組織時間和金錢。

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 是顯示資料的強大觀點，但是有一些限制。 使用自訂資料格時，滾動效能可能會受到影響，尤其是當其包含深度嵌套視圖階層，或使用需要複雜測量的特定配置時。 幸運的是，您有一些技術可以用來避免效能不佳。

## <a name="caching-strategy"></a>快取策略

Listview 通常用來顯示比畫面上更多的資料。 例如，音樂應用程式可能會有包含上千個專案的歌曲庫。 為每個專案建立一個專案，將會浪費寶貴的記憶體並執行效能不佳。 不斷地建立和終結資料列時，應用程式必須不斷地具現化和清除物件，這樣也會執行不佳的情況。

為了節省記憶體，每個平臺的原生對等專案 [`ListView`](xref:Xamarin.Forms.ListView) 都有內建功能可重複使用資料列。 只有螢幕上可見的資料格會載入至記憶體中，而且會將 **內容** 載入現有的儲存格。 此模式可防止應用程式將數以千計的物件具現化，以節省時間和記憶體。

Xamarin.Forms 允許 [`ListView`](xref:Xamarin.Forms.ListView) 透過 [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) 列舉來重複使用資料格，其具有下列值：

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> 通用 Windows 平臺 (UWP) 會忽略快取 [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) 策略，因為它一律會使用快取來改善效能。 因此，根據預設，它的行為會如同已套用快取 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 策略。

### <a name="retainelement"></a>RetainElement

快取 [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) 策略指定 [`ListView`](xref:Xamarin.Forms.ListView) 將會針對清單中的每個專案產生儲存格，而且是預設 `ListView` 行為。 它應該在下列情況下使用：

- 每個資料格都有大量的系結 (20-30 +) 。
- 資料格範本經常變更。
- 測試會顯示 `RecycleElement` 快取策略會導致執行速度降低。

使用自訂資料格時，請務必辨識快取策略的結果 [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) 。 任何資料格的初始化程式碼都必須針對每個資料格建立執行，這可能是每秒多次。 在這種情況下，在頁面上正常運作的版面配置技術（例如使用多個嵌套 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的實例）會在使用者滾動時，即時地進行設定和終結時變成效能瓶頸。

### <a name="recycleelement"></a>RecycleElement

快取 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 策略指定 [`ListView`](xref:Xamarin.Forms.ListView) 將會藉由回收清單儲存格，嘗試將記憶體使用量和執行速度降至最低。 此模式不一定會提供效能改進，而且應該執行測試來判斷是否有任何改進。 不過，這是慣用的選擇，應該在下列情況下使用：

- 每個資料格都有一個小到中等的系結數目。
- 每個 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 資料格都會定義所有資料格資料。
- 每個資料格主要類似于資料格範本不變。

在虛擬化期間，儲存格將會更新其系結內容，因此，如果應用程式使用此模式，則必須確定已適當處理系結內容更新。 資料格的所有資料都必須來自系結內容，否則可能會發生一致性錯誤。 您可以使用資料系結來顯示資料格資料，以避免這個問題。 或者，您應該在覆寫中設定資料格資料 `OnBindingContextChanged` ，而不是在自訂儲存格的函式中設定，如下列程式碼範例所示：

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

如需詳細資訊，請參閱系結 [內容變更](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes)。

在 iOS 和 Android 上，如果資料格使用自訂轉譯器，則必須確定已正確地執行屬性變更通知。 當資料格重複使用時，當系結內容更新為可用儲存格的系結內容時，將會變更其屬性值，並 `PropertyChanged` 引發事件。 如需詳細資訊，請參閱 [自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

#### <a name="recycleelement-with-a-datatemplateselector"></a>具有 DataTemplateSelector 的 RecycleElement

當 [`ListView`](xref:Xamarin.Forms.ListView) 使用 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 來選取時，快取 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 策略不會快取 `DataTemplate` s。 相反地， `DataTemplate` 會針對清單中的每個資料項目目選取。

> [!NOTE]
> 快取 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 策略具有在2.4 中引進的必要條件， Xamarin.Forms 當 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 系統要求您選取 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 每個都 `DataTemplate` 必須傳回相同 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 類型的時。 例如，假設有 [`ListView`](xref:Xamarin.Forms.ListView) 一個 `DataTemplateSelector` ，可以傳回 `MyDataTemplateA` (，其中會 `MyDataTemplateA` `ViewCell` 傳回型別) 的 `MyViewCellA` ，或是 `MyDataTemplateB` (其中 `MyDataTemplateB` `ViewCell` 傳回) 型別的，則 `MyViewCellB` 當傳回時， `MyDataTemplateA` 它必須傳回，否則會擲回 `MyViewCellA` 例外狀況。

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

快取策略是以快取 [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) 策略為基礎 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) ，此外還能確保當 [`ListView`](xref:Xamarin.Forms.ListView) 使用 [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 來選取時 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ， `DataTemplate` 會以清單中的專案類型來快取。 因此， `DataTemplate` 會針對每個專案類型選取一次，而不是針對每個專案實例選取一次。

> [!NOTE]
> 快取 [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) 策略具有所傳回的先決條件， `DataTemplate` [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 必須使用 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) 接受的函式 `Type` 。

### <a name="set-the-caching-strategy"></a>設定快取策略

[`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy)列舉值是使用函式多載指定的 [`ListView`](xref:Xamarin.Forms.ListView) ，如下列程式碼範例所示：

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

此方法與在 c # 的函式中設定快取策略引數的效果相同。

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>在子類別化的 ListView 中設定快取策略

`CachingStrategy`從子類別上的 XAML 設定屬性 [`ListView`](xref:Xamarin.Forms.ListView) 不會產生所需的行為，因為沒有的 `CachingStrategy` 屬性 `ListView` 。 此外，如果啟用 [XAMLC](~/xamarin-forms/xaml/xamlc.md) ，則會產生下列錯誤訊息： **找不到 ' CachingStrategy ' 的屬性、可系結屬性或事件**

此問題的解決方法是在子類別上指定可接受參數的函式 [`ListView`](xref:Xamarin.Forms.ListView) [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) ，並將它傳遞至基類：

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

然後，您 [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) 可以使用下列語法從 XAML 指定列舉值 `x:Arguments` ：

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>ListView 效能建議

有許多技術可用來改善的效能 `ListView` 。 下列建議可改善 ListView 的效能

- 將屬性系結 `ItemsSource` 至 `IList<T>` 集合，而不是 `IEnumerable<T>` 集合，因為 `IEnumerable<T>` 集合不支援隨機存取。
- 使用內建的資料格 (例如 `TextCell`  /  `SwitchCell` ) ，而不是 `ViewCell` 每次都使用。
- 使用較少的元素。 例如，請考慮使用單一 `FormattedString` 標籤，而不是多個標籤。
- `ListView` `TableView` 當顯示非同質資料（也就是不同類型的資料）時，請將取代為。
- 限制方法的使用 [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) 方式。 如果過度用，將會降低效能。
- 在 Android 上，請避免在具 `ListView` 現化之後設定的資料列分隔符號可見度或色彩，因為這樣會導致效能大幅下降。
- 避免根據來變更資料格版面配置 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 變更版面配置會產生大量的測量和初始成本。
- 避免深度嵌套的版面配置階層。 使用  `AbsoluteLayout` 或  `Grid` 有助於減少嵌套。
- 避免 `LayoutOptions`  `Fill` (`Fill` 的特定情況是計算) 的最便宜。
- 避免將放置在 `ListView` 中， `ScrollView` 原因如下：
  - 會執行 `ListView` 它自己的滾動。
  - `ListView`將不會收到任何筆勢，因為它們會由父系處理 `ScrollView` 。
  - `ListView`可以呈現以清單元素來滾動的自訂頁首和頁尾，可能會提供所使用的功能 `ScrollView` 。 如需詳細資訊，請參閱頁首 [和](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers)頁尾。
- 如果您需要在儲存格中呈現特定且複雜的設計，請考慮使用自訂轉譯器。

`AbsoluteLayout` 有可能在沒有單一量值呼叫的情況下執行版面配置，使其具有高效能。 如果 `AbsoluteLayout` 無法使用，請考慮 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 。 如果使用 `RelativeLayout` ，直接傳遞條件約束的速度會比使用運算式 API 更快。 這種方法較快，因為運算式 API 使用 JIT，而在 iOS 上，必須解釋樹狀結構，這會較慢。 運算式 API 適用于只在初始配置和旋轉時才需要的版面配置，但在中 `ListView` ，它會在滾動時不斷地執行，因此會影響效能。

建立或其儲存格的自訂轉譯器 [`ListView`](xref:Xamarin.Forms.ListView) ，是減少配置計算對滾動效能之影響的方法之一。 如需詳細資訊，請參閱 [自訂 ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) 和 [自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

## <a name="related-links"></a>相關連結

- [自訂轉譯器視圖 (範例) ](/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [自訂轉譯器 ViewCell (範例) ](/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)