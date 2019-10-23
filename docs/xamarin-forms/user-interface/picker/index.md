---
title: Xamarin. 表單選取器
description: '[Xamarin] 表單選取器會顯示專案的簡短清單，使用者可以從中選取專案。 本文說明如何使用選擇器類別，從資料清單中選取文字專案。'
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: c8246f8316a2a579bc890935dc4f9beecccb8dcc
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695995"
---
# <a name="xamarinforms-picker"></a>Xamarin. 表單選取器

_[選擇器] 視圖是從資料清單中選取文字專案的控制項。_

[Xamarin] [`Picker`](xref:Xamarin.Forms.Picker)會顯示專案的簡短清單，使用者可以從中選取專案。 `Picker` 會定義下列屬性：

- `string` 類型的[`Title`](xref:Xamarin.Forms.Picker.Title) ，預設為 `null`。
- [`Color`](xref:Xamarin.Forms.Color)類型的 `TitleColor`，用來顯示 `Title` 文字的色彩。
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource)類型 `IList`，要顯示的專案來源清單，預設為 `null`。
- `int` 類型的[`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) ，這是所選取專案的索引，預設為-1。
- `object` 類型的[`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) ，這是選取的專案，預設為 `null`。
- [`Color`](xref:Xamarin.Forms.Color)類型的[`TextColor`](xref:Xamarin.Forms.Picker.TextColor) ，用來顯示文字的色彩，預設為[`Color.Default`](xref:Xamarin.Forms.Color.Default)。
- [`FontAttributes`](xref:Xamarin.Forms.FontAttributes)類型的[`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes) ，預設為[`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None)。
- `string` 類型的[`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily) ，預設為 `null`。
- `double` 類型的[`FontSize`](xref:Xamarin.Forms.Picker.FontSize) ，預設為-1.0。
- `CharacterSpacing`，屬於 `double` 類型，這是 `Picker` 所顯示之專案的字元之間的間距。

所有屬性都是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以樣式化，而屬性可以是資料系結的目標。 [@No__t_1](xref:Xamarin.Forms.Picker.SelectedIndex)和[`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem)屬性具有[`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)的預設系結模式，這表示它們可以是使用[ViewModel （MVVM）](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構之應用程式中的資料系結目標。 如需設定字型屬性的相關資訊，請參閱[Fonts](~/xamarin-forms/user-interface/text/fonts.md)。

[@No__t_1](xref:Xamarin.Forms.Picker)在第一次顯示時不會顯示任何資料。 相反地，其[`Title`](xref:Xamarin.Forms.Picker.Title)屬性的值會顯示為 IOS 和 Android 平臺上的預留位置：

[![](images/picker-initial.png "Initial Picker Display")](images/picker-initial-large.png#lightbox "Initial Picker Display")

當[`Picker`](xref:Xamarin.Forms.Picker)取得焦點時，就會顯示其資料，且使用者可以選取一個專案：

[![](images/picker-selection.png "Picker Selecting an Item")](images/picker-selection-large.png#lightbox "Picker Selecting an Item")

當使用者選取專案時， [`Picker`](xref:Xamarin.Forms.Picker)會引發[`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件。 選取之後，`Picker` 會顯示選取的專案：

![](images/picker-after-selection.png "Picker after Selection")

有兩種方法可將資料填入[`Picker`](xref:Xamarin.Forms.Picker) ：

- 將 [ [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) ] 屬性設定為要顯示的資料。 這是建議使用的技巧。 如需詳細資訊，請參閱[設定選擇器的 ItemsSource 屬性](populating-itemssource.md)。
- 加入要顯示在[`Items`](xref:Xamarin.Forms.Picker.Items)集合中的資料。 這項技術是以資料填入[`Picker`](xref:Xamarin.Forms.Picker)的原始程式。 如需詳細資訊，請參閱[將資料加入至選擇器的專案集合](populating-items.md)。

## <a name="related-links"></a>相關連結

- [選擇器](xref:Xamarin.Forms.Picker)
