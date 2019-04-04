---
title: Xamarin.Forms Picker
description: Xamarin.Forms 選擇器會顯示一份項目，使用者可以從中選取項目。 這篇文章說明如何使用選擇器類別，從資料的清單中選取的文字項目。
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: dc39fd9c129fb63fa4a3a73b15aea4204a38cdbd
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557265"
---
# <a name="xamarinforms-picker"></a>Xamarin.Forms Picker

_選擇器 檢視是從資料的清單中選取的文字項目控制項。_

Xamarin.Forms [ `Picker` ](xref:Xamarin.Forms.Picker)會顯示一份項目，使用者可以從中選取項目。 `Picker` 定義下列屬性：

- [`Title`](xref:Xamarin.Forms.Picker.Title) 型別的`string`，預設為`null`。
- `TitleColor` 型別的[ `Color` ](xref:Xamarin.Forms.Color)，用來顯示色彩`Title`文字。
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 型別的`IList`，預設為顯示，項目的來源清單`null`。
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 型別的`int`，選取的項目，預設值為-1 的索引。
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 型別的`object`，選取的項目，預設為`null`。
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) 型別的[ `Color` ](xref:Xamarin.Forms.Color)，用來顯示文字，其預設為色彩[ `Color.Default` ](xref:Xamarin.Forms.Color.Default)。
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) 型別的[ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes)，預設為[ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None)。
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) 型別的`string`，預設為`null`。
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) 型別的`double`，預設為-1.0。

所有的內容受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示它們可自訂樣式，而且屬性可以是資料繫結的目標。 [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)並[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)屬性具有預設繫結模式[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)，這表示它們可以是資料繫結的目標使用應用程式中[Model View ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。 如需設定字型屬性的詳細資訊，請參閱[字型](~/xamarin-forms/user-interface/text/fonts.md)。

A [ `Picker` ](xref:Xamarin.Forms.Picker)第一次顯示時，未顯示任何資料。 相反地，值及其[ `Title` ](xref:Xamarin.Forms.Picker.Title)屬性會顯示為預留位置，以在 iOS 和 Android 平台上：

[![](images/picker-initial.png "選擇器顯示的初始")](images/picker-initial-large.png#lightbox "初始選擇器顯示")

當[ `Picker` ](xref:Xamarin.Forms.Picker)提升取得焦點時，其資料會顯示，而且使用者可以選取項目：

[![](images/picker-selection.png "選擇器選取項目")](images/picker-selection-large.png#lightbox "選擇器選取項目")

[ `Picker` ](xref:Xamarin.Forms.Picker)引發[ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件，當使用者選取項目。 下列選項，顯示選取的項目`Picker`:

![](images/picker-after-selection.png "選取範圍之後的選擇器")

有兩種技術，用來填入[ `Picker` ](xref:Xamarin.Forms.Picker)資料：

- 設定[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)屬性設為要顯示的資料。 這是建議使用的技巧。 如需詳細資訊，請參閱 <<c0> [ 設定選擇器的 ItemsSource 屬性](populating-itemssource.md)。
- 加入要顯示的資料[ `Items` ](xref:Xamarin.Forms.Picker.Items)集合。 這項技術是原始的程序，用來填入[ `Picker` ](xref:Xamarin.Forms.Picker)資料。 如需詳細資訊，請參閱 <<c0> [ 將資料加入選擇器的項目集合至](populating-items.md)。

## <a name="related-links"></a>相關連結

- [選擇器](xref:Xamarin.Forms.Picker)
