---
title： " Xamarin.Forms 選擇器" 描述： "選擇 Xamarin.Forms 器會顯示專案的簡短清單，供使用者從中選取專案。 本文說明如何使用 [選擇器] 類別，從資料清單中選取文字專案。
assetid： D4815A4B-104B-4294-951B-BD8F2EC33C86 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：02/26/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-picker"></a>Xamarin.Forms容器

_[選擇器] 視圖是從資料清單中選取文字專案的控制項。_

Xamarin.Forms [`Picker`](xref:Xamarin.Forms.Picker) 會顯示專案的簡短清單，使用者可以從中選取專案。 `Picker` 會定義下列屬性：

- [`Title`](xref:Xamarin.Forms.Picker.Title)類型的 `string` ，預設為 `null` 。
- `TitleColor`類型的 [`Color`](xref:Xamarin.Forms.Color) ，用來顯示文字的色彩 `Title` 。
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource)類型的 `IList` ，要顯示的專案來源清單，預設為 `null` 。
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)屬於類型的 `int` 索引，這是選取的專案，預設為-1。
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem)屬於的類型 `object` ，這是選取的專案，預設為 `null` 。
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor)類型的 [`Color`](xref:Xamarin.Forms.Color) ，用來顯示文字的色彩，預設為 [`Color.Default`](xref:Xamarin.Forms.Color.Default) 。
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes)類型的 [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) ，預設為 [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) 。
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily)類型的 `string` ，預設為 `null` 。
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize)類型的 `double` ，預設為-1.0。
- `CharacterSpacing`，屬於類型 `double` ，是所顯示之專案的字元之間的間距 `Picker` 。

所有屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以樣式化，而屬性可以是資料系結的目標。 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)和 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 屬性具有的預設系結模式 [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) ，這表示它們可以是使用[ViewModel （MVVM）](~/xamarin-forms/enterprise-application-patterns/mvvm.md)架構之應用程式中的資料系結目標。 如需設定字型屬性的相關資訊，請參閱[Fonts](~/xamarin-forms/user-interface/text/fonts.md)。

[`Picker`](xref:Xamarin.Forms.Picker)當第一次顯示時，不會顯示任何資料。 相反地，其屬性的值 [`Title`](xref:Xamarin.Forms.Picker.Title) 會顯示為 iOS 和 Android 平臺上的預留位置：

[![](images/picker-initial.png "Initial Picker Display")](images/picker-initial-large.png#lightbox "Initial Picker Display")

[`Picker`](xref:Xamarin.Forms.Picker)取得焦點時，會顯示其資料，且使用者可以選取一個專案：

[![](images/picker-selection.png "Picker Selecting an Item")](images/picker-selection-large.png#lightbox "Picker Selecting an Item")

[`Picker`](xref:Xamarin.Forms.Picker) [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) 當使用者選取專案時，會引發事件。 選取專案之後，會顯示選取的專案 `Picker` ：

![](images/picker-after-selection.png "Picker after Selection")

有兩種方法可將 [`Picker`](xref:Xamarin.Forms.Picker) 資料填入：

- 將屬性設定為 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 要顯示的資料。 這是建議使用的技巧。 如需詳細資訊，請參閱[設定選擇器的 ItemsSource 屬性](populating-itemssource.md)。
- 加入要顯示在集合中的資料 [`Items`](xref:Xamarin.Forms.Picker.Items) 。 這項技術是 [`Picker`](xref:Xamarin.Forms.Picker) 使用資料填入的原始程式。 如需詳細資訊，請參閱[將資料加入至選擇器的專案集合](populating-items.md)。

## <a name="related-links"></a>相關連結

- [Picker](xref:Xamarin.Forms.Picker)
