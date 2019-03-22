---
title: Xamarin.Forms CollectionView
description: CollectionView 是富彈性且高效能的檢視，來呈現資料使用不同的版面配置規格的清單。
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
---

# <a name="xamarinforms-collectionview"></a>Xamarin.Forms CollectionView

![預覽](~/media/shared/preview.png)

> [!IMPORTANT]
> `CollectionView`目前為預覽狀態，且缺少其中一些規劃功能。 此外，實作完成時，可能會變更的 API。

`CollectionView` 呈現資料的清單檢視使用不同的版面配置規格。 它旨在提供更多的彈性，以及高效能替代方式，來[ `ListView` ](xref:Xamarin.Forms.ListView)。 雖然`CollectionView`和`ListView`Api 類似，有一些值得注意的差異：

- `CollectionView` 具有彈性的配置模型，可讓清單或方格中顯示的水平或垂直的資料。
- `CollectionView` 有沒有儲存格的概念。 相反地，資料範本用來在清單中定義的每個資料項目的外觀。
- `CollectionView` 自動會利用基礎原生控制項所提供的虛擬化。
- `CollectionView` 減少的 API 介面[ `ListView` ](xref:Xamarin.Forms.ListView)。 許多屬性和事件`ListView`不會出現在`CollectionView`。
- `CollectionView` 不包含內建的分隔符號。

`CollectionView` 使用 Xamarin.Forms 4.0 發行前版本。 不過，目前為實驗性，而且僅適用於藉由將下列這一行的程式碼，以您`AppDelegate`類別在 iOS 上，或您`MainActivity`類別在 Android 上，然後再呼叫`Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!NOTE]
> `CollectionView` 只有在 iOS 和 Android 上使用。

## <a name="populate-collectionview-with-datapopulate-datamd"></a>[以資料填入 CollectionView](populate-data.md)

A`CollectionView`藉由設定資料填入其`ItemsSource`屬性來實作任何集合`IEnumerable`。 設定也可以定義在清單中每個項目的外觀`ItemTemplate`屬性，以[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。

## <a name="specify-collectionview-layoutlayoutmd"></a>[指定 CollectionView 版面配置](layout.md)

根據預設，`CollectionView`會以垂直清單顯示其項目。 不過，可以指定垂直和水平清單與格線。

## <a name="set-collectionview-selection-modeselectionmd"></a>[設定 CollectionView 選取模式](selection.md)

根據預設，`CollectionView`選取項目已停用。 不過，您可以啟用單一和多重選取項目。

## <a name="display-an-emptyview-when-data-is-unavailableemptyviewmd"></a>[資料無法使用時顯示 EmptyView](emptyview.md)

在  `CollectionView`，可以指定空的檢視，可將提供意見反應給使用者，當沒有資料可供顯示。 字串、 一個檢視或多個檢視，可以是空的檢視。

## <a name="scroll-an-item-into-viewscrollingmd"></a>[捲動至檢視的項目](scrolling.md)

當起始捲使用者 swipes 時，就可以控制捲軸的結束位置，完整顯示的項目。 颾魤 ㄛ`CollectionView`會定義兩個`ScrollTo`方法，以程式設計方式捲動至檢視的 項目。 另將指定的項目捲動至檢視時，其中一個多載捲動到檢視中，指定索引處的項目。
