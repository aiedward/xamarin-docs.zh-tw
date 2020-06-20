---
title: 設定 Picker 的 ItemsSource 屬性
description: '[選擇器] 視圖是從資料清單中選取文字專案的控制項。 本文說明如何藉由設定 ItemsSource 屬性來填入選擇器的資料，以及如何回應使用者選取的專案。'
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c4fc732082a77a2e471465af448a487862b513c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136288"
---
# <a name="setting-a-pickers-itemssource-property"></a>設定 Picker 的 ItemsSource 屬性

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)

_[選擇器] 視圖是從資料清單中選取文字專案的控制項。本文說明如何藉由設定 ItemsSource 屬性來填入選擇器的資料，以及如何回應使用者選取的專案。_

Xamarin.Forms2.3.4 已藉 [`Picker`](xref:Xamarin.Forms.Picker) 由設定屬性來填入資料 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) ，並從屬性中取出選取的專案，藉此增強了視圖功能 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 。 此外，您可以藉由將屬性設定為，來變更所選取專案的文字色彩 [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) [`Color`](xref:Xamarin.Forms.Color) 。

## <a name="populating-a-picker-with-data"></a>以資料填入選擇器

將 [`Picker`](xref:Xamarin.Forms.Picker) 屬性設定為集合，即可填入資料 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) `IList` 。 集合中的每個專案都必須是（或衍生自）型別 `object` 。 藉由初始化專案陣列中的屬性，可以在 XAML 中加入專案 `ItemsSource` ：

```xaml
<Picker x:Name="picker"
        Title="Select a monkey"
        TitleColor="Red">
  <Picker.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </Picker.ItemsSource>
</Picker>
```

> [!NOTE]
> 請注意，`x:Array` 項目需要 `Type` 屬性，以指出陣列中的項目類型。

對等的 c # 程式碼如下所示：

```csharp
var monkeyList = new List<string>();
monkeyList.Add("Baboon");
monkeyList.Add("Capuchin Monkey");
monkeyList.Add("Blue Monkey");
monkeyList.Add("Squirrel Monkey");
monkeyList.Add("Golden Lion Tamarin");
monkeyList.Add("Howler Monkey");
monkeyList.Add("Japanese Macaque");

var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>回應專案選取

[`Picker`](xref:Xamarin.Forms.Picker)支援一次選取一個專案。 當使用者選取專案時， [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) 就會引發事件、將 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 屬性更新為代表清單中所選取專案之索引的整數，並將 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 屬性更新為 `object` 表示選取之專案的。 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)屬性是以零為基底的數位，表示使用者選取的專案。 如果未選取任何專案，這是 [`Picker`](xref:Xamarin.Forms.Picker) 第一次建立並初始化時的情況， `SelectedIndex` 將會是-1。

> [!NOTE]
> 中的專案選取行為 [`Picker`](xref:Xamarin.Forms.Picker) 可以使用平臺特定，在 iOS 上進行自訂。 如需詳細資訊，請參閱[控制選擇器專案選擇](~/xamarin-forms/platform/ios/picker-selection.md)。

下列程式碼範例顯示如何 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 在 XAML 中從取得屬性值 [`Picker`](xref:Xamarin.Forms.Picker) ：

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

對等的 c # 程式碼如下所示：

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

此外，當事件引發時，可以執行事件處理常式 [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) ：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = (string)picker.ItemsSource[selectedIndex];
  }
}
```

這個方法會 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 取得屬性值，並使用值來抓取集合中選取的專案 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 。 這在功能上相當於從屬性中抓取選取的專案 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 。 請注意，集合中的每個專案 `ItemsSource` 都是類型 `object` ，因此必須轉換成以 `string` 供顯示。

> [!NOTE]
> [`Picker`](xref:Xamarin.Forms.Picker)可以初始化，藉由設定或屬性來顯示特定專案 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 。 不過，在初始化集合之後，必須設定這些屬性 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 。

## <a name="populating-a-picker-with-data-using-data-binding"></a>使用資料系結將資料填入選擇器

也可以使用資料系結將其屬性系結至集合，以 [`Picker`](xref:Xamarin.Forms.Picker) 填入資料 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) `IList` 。 在 XAML 中，這是透過 [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) 標記延伸來達成：

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}" />
```

對等的 c # 程式碼如下所示：

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

屬性資料會系結 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 至 `Monkeys` 已連線視圖模型的屬性，以傳回 `IList<Monkey>` 集合。 下列程式碼範例顯示 `Monkey` 類別，其中包含四個屬性：

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

當系結至物件清單時， [`Picker`](xref:Xamarin.Forms.Picker) 必須告知要從每個物件顯示哪一個屬性。 將 [`ItemDisplayBinding`](xref:Xamarin.Forms.Picker.ItemDisplayBinding) 屬性從每個物件設定為必要屬性，即可達成此目的。 在上述的程式碼範例中， `Picker` 會設定為顯示每個 `Monkey.Name` 屬性值。

### <a name="responding-to-item-selection"></a>回應專案選取

資料系結可在變更時，用來將物件設定為 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 屬性值：

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}"
        SelectedItem="{Binding SelectedMonkey}" />
<Label Text="{Binding SelectedMonkey.Name}" ... />
<Label Text="{Binding SelectedMonkey.Location}" ... />
<Image Source="{Binding SelectedMonkey.ImageUrl}" ... />
<Label Text="{Binding SelectedMonkey.Details}" ... />
```

對等的 c # 程式碼如下所示：

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.SetBinding(Picker.SelectedItemProperty, "SelectedMonkey");
picker.ItemDisplayBinding = new Binding("Name");

var nameLabel = new Label { ... };
nameLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Name");

var locationLabel = new Label { ... };
locationLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Location");

var image = new Image { ... };
image.SetBinding(Image.SourceProperty, "SelectedMonkey.ImageUrl");

var detailsLabel = new Label();
detailsLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Details");
```

[`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem)屬性資料會系結至已 `SelectedMonkey` 連接視圖模型的屬性，其類型為 `Monkey` 。 因此，當使用者在中選取專案時 [`Picker`](xref:Xamarin.Forms.Picker) ， `SelectedMonkey` 屬性將會設定為選取的 `Monkey` 物件。 `SelectedMonkey`物件資料會在使用者介面中顯示 [`Label`](xref:Xamarin.Forms.Label) ，以及 [`Image`](xref:Xamarin.Forms.Image) views：

![](populating-itemssource-images/monkeys.png "Picker Item Selection")

> [!NOTE]
> 請注意， [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 和 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 屬性預設都支援雙向系結。

## <a name="related-links"></a>相關連結

- [選擇器示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [猴子應用程式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)
- [可系結的選擇器（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
- [選擇器 API](xref:Xamarin.Forms.Picker)
