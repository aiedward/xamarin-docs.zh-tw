---
title: Xamarin.Forms CollectionView 捲動
description: 當起始捲使用者 swipes 時，就可以控制捲軸的結束位置，完整顯示的項目。 颾魤 ㄛ CollectionView 定義兩個 ScrollTo 方法，以程式設計方式捲動至檢視的 項目。
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: b2f32f6695fffa27068fce9d8c12f4ecd9157bc2
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970532"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Xamarin.Forms CollectionView 捲動

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義兩個[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法，捲動至檢視的項目。 另將指定的項目捲動至檢視時，其中一個多載捲動到檢視中，指定索引處的項目。 這兩個多載包含有額外的引數可指定捲軸已在完成之後，表示項目的確切的位置，以及是否要以動畫顯示捲軸。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義[ `ScrollToRequested` ](xref:Xamarin.Forms.ItemsView.ScrollToRequested)時引發的事件之一[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法叫用。 [ `ScrollToRequestedEventArgs` ](xref:Xamarin.Forms.ScrollToRequestedEventArgs)隨附的物件`ScrollToRequested`事件具有許多屬性，包括`IsAnimated`， `Index`， `Item`，以及`ScrollToPosition`。 這些屬性設定中指定的引數從`ScrollTo`方法呼叫。

當起始捲使用者 swipes 時，就可以控制捲軸的結束位置，完整顯示的項目。 這項功能稱為貼齊，因為項目貼齊定位捲動停駐點時。 如需詳細資訊，請參閱 <<c0> [ 貼齊點](#snap-points)。

## <a name="scroll-an-item-at-an-index-into-view"></a>捲動至檢視的索引處的項目

第一個[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法多載會將指定的索引處的項目捲動至檢視。 給定[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)名為物件`collectionView`，下列範例示範如何以捲動到檢視的索引 12 處的項目：

```csharp
collectionView.ScrollTo(12);
```

## <a name="scroll-an-item-into-view"></a>捲動至檢視的項目

第二個[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法多載會將指定的項目捲動至檢視。 給定[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)名為物件`collectionView`，下列範例示範如何指定的項目捲動至檢視：

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

## <a name="control-scroll-position"></a>控制捲動位置

捲動到檢視的項目時, 可以使用指定的項目之後的捲軸已完成的確切位置`position`引數[ `ScrollTo` ](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法。 此引數會接受[ `ScrollToPosition` ](xref:Xamarin.Forms.ScrollToPosition)列舉成員。

### <a name="makevisible"></a>MakeVisible

[ `ScrollToPosition.MakeVisible` ](xref:Xamarin.Forms.ScrollToPosition)成員表示顯示在檢視之前，應該捲動項目：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

此程式碼範例會產生捲動至檢視的項目所需的最小捲動：

[![與某個項目 CollectionView 垂直清單的螢幕擷取畫面捲動到檢視，在 iOS 和 Android 上](scrolling-images/scrolltoposition-makevisible.png "CollectionView 垂直捲動項目清單")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "CollectionView 垂直清單捲動項目")

> [!NOTE]
> [ `ScrollToPosition.MakeVisible` ](xref:Xamarin.Forms.ScrollToPosition)成員會依預設，如果`position`呼叫時未指定引數`ScrollTo`方法。

### <a name="start"></a>啟動

[ `ScrollToPosition.Start` ](xref:Xamarin.Forms.ScrollToPosition)成員表示的項目應該捲動到檢視的開始：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

此程式碼範例會產生要捲動到檢視開頭的項目：

[![與某個項目 CollectionView 垂直清單的螢幕擷取畫面捲動到檢視，在 iOS 和 Android 上](scrolling-images/scrolltoposition-start.png "CollectionView 垂直捲動項目清單")](scrolling-images/scrolltoposition-start-large.png#lightbox "CollectionView 垂直清單捲動項目")

### <a name="center"></a>置中

[ `ScrollToPosition.Center` ](xref:Xamarin.Forms.ScrollToPosition)成員表示的項目應該捲動到檢視的中心：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

此程式碼範例會產生要捲動到檢視的中央的項目：

[![與某個項目 CollectionView 垂直清單的螢幕擷取畫面捲動到檢視，在 iOS 和 Android 上](scrolling-images/scrolltoposition-center.png "CollectionView 垂直捲動項目清單")](scrolling-images/scrolltoposition-center-large.png#lightbox "CollectionView 垂直清單捲動項目")

### <a name="end"></a>End - 結束

[ `ScrollToPosition.End` ](xref:Xamarin.Forms.ScrollToPosition)成員表示的項目應該捲動到檢視的結尾：

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

此程式碼範例會產生要捲動到檢視結尾的項目：

[![與某個項目 CollectionView 垂直清單的螢幕擷取畫面捲動到檢視，在 iOS 和 Android 上](scrolling-images/scrolltoposition-end.png "CollectionView 垂直捲動項目清單")](scrolling-images/scrolltoposition-end-large.png#lightbox "CollectionView 垂直清單捲動項目")

## <a name="disable-scroll-animation"></a>停用捲動的動畫

捲動到檢視的項目時，會顯示捲動的動畫。 不過，這個動畫可以停用藉由設定`animate`引數`ScrollTo`方法來`false`:

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="snap-points"></a>貼齊點

當起始捲使用者 swipes 時，就可以控制捲軸的結束位置，完整顯示的項目。 這項功能稱為貼齊，因為項目時捲動會停止，而由下列屬性所控制的位置貼齊[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout)類別：

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)型別的[ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType)，指定捲動時貼齊點的行為。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)型別的[ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment)，指定與項目貼齊點的對齊方式。

這些屬性都會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示，屬性可以是資料繫結的目標。

> [!NOTE]
> 貼齊發生時，它會發生的方向，會產生最少的動作。

### <a name="snap-points-type"></a>貼齊點類型

[ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType)列舉會定義下列成員：

- `None` 指出，捲動不貼齊項目。
- `Mandatory` 指出該內容會貼齊至最接近的嵌入式管理單元一律會指向其中捲動會自然地停止，沿著慣性的方向。
- `MandatorySingle` 表示相同的行為`Mandatory`，但只有一次捲動一個項目。

根據預設， [ `SnapPointsType` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)屬性設定為`SnapPointsType.None`，以確保該捲動不會貼齊項目，如下列螢幕擷取畫面所示：

[![IOS 和 Android 上的貼齊點沒有 CollectionView 垂直清單的螢幕擷取畫面](scrolling-images/snappoints-none.png "CollectionView 垂直清單，而不需要貼齊點")](scrolling-images/snappoints-none-large.png#lightbox "CollectionView 垂直清單，而不需要 嵌入式管理單元點")

### <a name="snap-points-alignment"></a>貼齊點的對齊方式

[ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment)列舉會定義`Start`， `Center`，和`End`成員。

> [!IMPORTANT]
> 值[ `SnapPointsAlignment` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)屬性只會遵守時[ `SnapPointsType` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)屬性設定為`Mandatory`，或`MandatorySingle`。

#### <a name="start"></a>啟動

`SnapPointsAlignment.Start`成員表示貼齊點會配合先進的項目。

根據預設， [ `SnapPointsAlignment` ](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)屬性設定為`SnapPointsAlignment.Start`。 不過，基於完整性，下列 XAML 範例示範如何設定這個列舉成員：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Start">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

當起始捲使用者 swipes 時，第一個項目會配合檢視的頂端：

[![IOS 和 Android 上開始貼齊點 CollectionView 垂直清單的螢幕擷取畫面](scrolling-images/snappoints-start.png "CollectionView 垂直清單開始的貼齊點")](scrolling-images/snappoints-start-large.png#lightbox "CollectionView 垂直清單開始貼齊點")

#### <a name="center"></a>置中

`SnapPointsAlignment.Center`成員表示貼齊點會與項目的中心對齊。 下列 XAML 範例示範如何設定這個列舉成員：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Center">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

當起始捲使用者 swipes 時，第一個項目會置中對齊檢視的頂端：

[![具有 center 貼齊點，在 iOS 和 Android 上的 CollectionView 垂直清單的螢幕擷取畫面](scrolling-images/snappoints-center.png "CollectionView 垂直中心的貼齊點清單")](scrolling-images/snappoints-center-large.png#lightbox "CollectionView 垂直清單center 貼齊點")

#### <a name="end"></a>End - 結束

`SnapPointsAlignment.End`成員表示尾端邊緣的項目，符合的貼齊點。 下列 XAML 範例示範如何設定這個列舉成員：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="End">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

當起始捲使用者 swipes 時，底部項目會配合檢視的下方：

[![結束貼齊點，在 iOS 和 Android 上的 CollectionView 垂直清單的螢幕擷取畫面](scrolling-images/snappoints-end.png "CollectionView 垂直清單結尾的貼齊點")](scrolling-images/snappoints-end-large.png#lightbox "CollectionView 垂直清單結尾貼齊點")

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
