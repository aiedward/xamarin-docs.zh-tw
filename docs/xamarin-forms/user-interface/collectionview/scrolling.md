---
title: Xamarin. 表單 CollectionView 滾動
description: 當使用者撥動起始捲軸時, 可以控制捲軸的結束位置, 以便完全顯示專案。 此外, CollectionView 會定義兩個 ScrollTo 方法, 以程式設計方式將專案滾動到 view。
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 89bbe402f056b875a7dadd96527364847ad470e8
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738930"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Xamarin. 表單 CollectionView 滾動

![](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義兩[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)個方法, 可將專案滾動到 view。 其中一個多載會將指定索引處的專案滾動到 view, 而另一個則會將指定的專案滾動到 view。 這兩個多載都有額外的引數, 可指定以指出專案在捲軸完成後的確切位置, 以及是否要以動畫顯示捲軸。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義叫[`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested)用其中一個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法時所引發的事件。 `IsAnimated` `Item` `Index` `ScrollToPosition`事件隨附的[物件有許多屬性,包括、、和。`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) `ScrollToRequested` 這些屬性是由`ScrollTo`方法呼叫中指定的引數所設定。

當使用者撥動起始捲軸時, 可以控制捲軸的結束位置, 以便完全顯示專案。 這項功能稱為「貼齊」, 因為當滾動停止時, 專案會貼齊位置。 如需詳細資訊, 請參閱[貼齊點](#snap-points)。

## <a name="scroll-an-item-at-an-index-into-view"></a>將索引中的專案滾動到 view

第一個[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法多載會將指定索引處的專案滾動到 view。 假設有[`CollectionView`](xref:Xamarin.Forms.CollectionView)一個名`collectionView`為的物件, 下列範例會示範如何將索引12處的專案滾動到 view:

```csharp
collectionView.ScrollTo(12);
```

## <a name="scroll-an-item-into-view"></a>將專案滾動到視野中

第二[`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)個方法多載會將指定的專案滾動到 view。 假設有[`CollectionView`](xref:Xamarin.Forms.CollectionView)一個名`collectionView`為的物件, 下列範例會示範如何將指定的專案滾動到 view:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

## <a name="control-scroll-position"></a>控制項捲軸位置

將專案滾動到 view 時, 可以使用`position` [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*)方法的引數來指定專案在捲軸之後的確切位置。 這個引數會[`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition)接受列舉成員。

### <a name="makevisible"></a>MakeVisible

[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應該滾動到它在視圖中可見為止:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

這個範例程式碼會產生將專案滾動到視野所需的最小滾動:

[ ![CollectionView 垂直清單的螢幕擷取畫面, 其中包含滾動到 view 的專案、IOS 和 Android 上](scrolling-images/scrolltoposition-makevisible.png "具有捲軸專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

> [!NOTE]
> 如果[`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition)在`position`呼叫`ScrollTo`方法時未指定引數, 則預設會使用此成員。

### <a name="start"></a>啟動

[`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應該滾動到視圖的開頭:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

這個範例程式碼會導致專案滾動到視圖的開頭:

[ ![CollectionView 垂直清單的螢幕擷取畫面, 其中包含滾動到 view 的專案、IOS 和 Android 上](scrolling-images/scrolltoposition-start.png "具有捲軸專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-start-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

### <a name="center"></a>置中

[`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應該滾動到視圖的中心:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

此範例程式碼會將專案滾動到視圖的中心:

[ ![CollectionView 垂直清單的螢幕擷取畫面, 其中包含滾動到 view 的專案、IOS 和 Android 上](scrolling-images/scrolltoposition-center.png "具有捲軸專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-center-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

### <a name="end"></a>End - 結束

[`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition)成員表示專案應該滾動到視圖的結尾:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

這個範例程式碼會導致專案滾動到視圖的結尾:

[ ![CollectionView 垂直清單的螢幕擷取畫面, 其中包含滾動到 view 的專案、IOS 和 Android 上](scrolling-images/scrolltoposition-end.png "具有捲軸專案的 CollectionView 垂直清單")](scrolling-images/scrolltoposition-end-large.png#lightbox "具有滾動專案的 CollectionView 垂直清單")

## <a name="disable-scroll-animation"></a>停用捲軸動畫

將專案滾動到 view 時, 會顯示滾動動畫。 不過, 您可以將`animate` `ScrollTo`方法的引數設定為, 以`false`停用這個動畫:

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="snap-points"></a>貼齊點

當使用者撥動起始捲軸時, 可以控制捲軸的結束位置, 以便完全顯示專案。 這項功能稱為「貼齊」, 因為當滾動停止時, 專案會貼齊至位置, 而且是由[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)類別的下列屬性所控制:

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), 屬於類型[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), 會在滾動時指定貼齊點的行為。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), 屬於類型[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), 指定對齊點與專案對齊的方式。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援, 這表示屬性可以是資料系結的目標。

> [!NOTE]
> 發生貼齊時, 會以產生最少動作的方向進行。

### <a name="snap-points-type"></a>貼齊點類型

[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)列舉會定義下列成員:

- `None`表示滾動不會貼齊專案。
- `Mandatory`指出內容一律會貼齊到最接近的貼齊點, 也就是慣性的方向。
- `MandatorySingle`表示與相同的行為`Mandatory`, 但一次只會滾動一個專案。

根據預設, [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)屬性會設定為`SnapPointsType.None`, 以確保滾動不會貼齊專案, 如下列螢幕擷取畫面所示:

[![不含貼齊點之 CollectionView 垂直清單的螢幕擷取畫面, 在 IOS 和 Android](scrolling-images/snappoints-none.png "CollectionView 垂直清單 (不含貼齊點"))](scrolling-images/snappoints-none-large.png#lightbox "不含貼齊點的 CollectionView 垂直清單")

### <a name="snap-points-alignment"></a>貼齊點對齊

列舉會定義`Start`、 `Center`和`End`成員。 [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)

> [!IMPORTANT]
> 只有[`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) `Mandatory`當屬性設定為或`MandatorySingle`時, 才會遵守屬性的值。 [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)

#### <a name="start"></a>啟動

`SnapPointsAlignment.Start`成員表示貼齊點與專案的前置邊緣對齊。

根據預設, [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)屬性會設定為`SnapPointsAlignment.Start`。 不過, 基於完整性, 下列 XAML 範例示範如何設定這個列舉成員:

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

當使用者撥動起始捲軸時, 最上方的專案會與視圖的頂端對齊:

[![具有開始貼齊點之 CollectionView 垂直清單的螢幕擷取畫面, 在 IOS 和 Android 上,](scrolling-images/snappoints-start.png "具有開始貼齊點的 CollectionView 垂直清單")](scrolling-images/snappoints-start-large.png#lightbox "具有開始貼齊點的 CollectionView 垂直清單")

#### <a name="center"></a>置中

`SnapPointsAlignment.Center`成員表示貼齊點對齊專案的中心。 下列 XAML 範例顯示如何設定此列舉成員:

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

當使用者撥動起始捲軸時, 最上方的專案會置中對齊視圖的頂端:

[ ![CollectionView 垂直清單的螢幕擷取畫面, 其中包含中心貼齊點, 在 IOS 和 Android 上,](scrolling-images/snappoints-center.png "具有中心貼齊點的 CollectionView 垂直清單")](scrolling-images/snappoints-center-large.png#lightbox "具有中心貼齊點的 CollectionView 垂直清單")

#### <a name="end"></a>End - 結束

`SnapPointsAlignment.End`成員表示貼齊點與專案的尾端邊緣對齊。 下列 XAML 範例顯示如何設定此列舉成員:

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

當使用者撥動起始捲軸時, 底部專案會對齊視圖的底部:

[![具有結束貼齊點之 CollectionView 垂直清單的螢幕擷取畫面, 在 IOS 和 Android 上](scrolling-images/snappoints-end.png "具有結束貼齊點的 CollectionView 垂直清單")](scrolling-images/snappoints-end-large.png#lightbox "具有結束貼齊點的 CollectionView 垂直清單")

## <a name="related-links"></a>相關連結

- [CollectionView (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
