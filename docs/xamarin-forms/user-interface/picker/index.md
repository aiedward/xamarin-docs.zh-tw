---
title: 選擇器
description: 選擇器 檢視是從資料清單中選取的文字項目控制項。
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 7f0050351ca28d7f8afeb82a85e82e51d399824b
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847494"
---
# <a name="picker"></a>選擇器

_選擇器 檢視是從資料清單中選取的文字項目控制項。_

Xamarin.Forms [ `Picker` ](xref:Xamarin.Forms.Picker)顯示的項目，使用者可以從中選取項目簡短清單。 `Picker` 定義八個屬性：

- [`Title`](xref:Xamarin.Forms.Picker.Title) 型別的`string`，預設為`null`。
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 型別的`IList`，預設為顯示，項目的來源清單`null`。
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 型別的`int`，預設值為-1 的選取項目索引。
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 型別的`object`，選取的項目，其預設位置為`null`。
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) 型別的[ `Color` ](xref:Xamarin.Forms.Color)，用來顯示文字，其預設位置為色彩[ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/)。
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) 型別的[ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes)，預設為[ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None)。
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) 型別的`string`，預設為`null`。
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize) 型別的`double`，預設為-1.0。

所有的八個屬性都由[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示它們可以套用樣式，而且屬性可以做為目標的資料繫結。 [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)和[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)屬性都使用預設繫結模式的[ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)，這表示它們可以是資料繫結的目標使用的應用程式中[模型-檢視-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構。 設定字型屬性的相關資訊，請參閱[字型](~/xamarin-forms/user-interface/text/fonts.md)。

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)第一次出現時，不會顯示任何資料。 相反地，值及其[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Title/)屬性會顯示為 iOS 和 Android 平台上的預留位置：

[![](images/picker-initial.png "初始選擇器顯示")](images/picker-initial-large.png#lightbox "初始選擇器顯示")

當[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)獲得焦點時，其資料會顯示，而且使用者可以選取項目：

[![](images/picker-selection.png "選擇器中選取項目")](images/picker-selection-large.png#lightbox "選擇器中選取項目")

[ `Picker` ](xref:Xamarin.Forms.Picker)引發[ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件，當使用者選取項目。 選取的項目會顯示所選取項目，下列`Picker`:

![](images/picker-after-selection.png "選取範圍之後的選擇器")

有兩種方法來填入[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)資料：

- 設定[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)要顯示之資料的屬性。 這是建議使用的技巧，在 Xamarin.Forms 2.3.4 所導入。 如需詳細資訊，請參閱[選擇器 ItemsSource 屬性設定](populating-itemssource.md)。
- 將資料加入至要顯示[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合。 這項技術是原始的程序，用來填入[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)與資料。 如需詳細資訊，請參閱[將資料加入至選擇器的項目集合](populating-items.md)。

## <a name="related-links"></a>相關連結

- [選擇器](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
