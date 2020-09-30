---
title: 設定 Picker 的 ItemsSource 屬性
description: 選擇器視圖是從資料清單中選取文字專案的控制項。 本文說明如何藉由設定 ItemsSource 屬性來將資料填入選擇器，以及如何回應使用者的選取專案。
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 395536752fb0d581408d0b8f91ef623d926aeeb4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559541"
---
# <a name="setting-a-pickers-itemssource-property"></a>設定 Picker 的 ItemsSource 屬性

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)

_選擇器視圖是從資料清單中選取文字專案的控制項。本文說明如何藉由設定 ItemsSource 屬性來將資料填入選擇器，以及如何回應使用者的選取專案。_

Xamarin.Forms 2.3.4 已增強 [`Picker`](xref:Xamarin.Forms.Picker) 視圖的功能，方法是藉由設定 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 屬性，並從屬性中取出選取的專案，藉以新增將資料填入資料的功能 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 。 此外，您可以藉由將屬性設定為，來變更所選取專案的文字色彩 [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) [`Color`](xref:Xamarin.Forms.Color) 。

## <a name="populating-a-picker-with-data"></a>以資料填入選擇器

您 [`Picker`](xref:Xamarin.Forms.Picker) 可以藉由將其屬性設定為集合，來填入資料 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) `IList` 。 集合中的每個專案都必須是或衍生自類型 `object` 。 從專案陣列初始化屬性，即可在 XAML 中加入專案 `ItemsSource` ：

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

[`Picker`](xref:Xamarin.Forms.Picker)支援一次選取一個專案。 當使用者選取專案時， [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) 就會引發此事件， [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 屬性會更新為整數，表示清單中所選取專案的索引，而且此 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 屬性會更新為 `object` 代表選取專案的。 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)屬性是以零為基底的數位，表示使用者選取的專案。 如果未選取任何專案，則在 [`Picker`](xref:Xamarin.Forms.Picker) 第一次建立和初始化時，將會 `SelectedIndex` 是-1。

> [!NOTE]
> 中的專案選取行為 [`Picker`](xref:Xamarin.Forms.Picker) 可以在具有平臺特定的 iOS 上自訂。 如需詳細資訊，請參閱 [控制選擇器專案](~/xamarin-forms/platform/ios/picker-selection.md)選取。

下列程式碼範例示範如何 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 在 XAML 中取出的屬性值 [`Picker`](xref:Xamarin.Forms.Picker) ：

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

對等的 c # 程式碼如下所示：

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

此外，事件引發時也可執行事件處理常式 [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) ：

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

這個方法會 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 取得屬性值，並使用該值從集合中取出選取的專案 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 。 這在功能上相當於從屬性中取出選取的專案 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 。 請注意，集合中的每個專案 `ItemsSource` 都屬於類型 `object` ，因此必須轉換成以 `string` 顯示。

> [!NOTE]
> [`Picker`](xref:Xamarin.Forms.Picker)可以透過設定或屬性來初始化，以顯示特定的 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 專案 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 。 不過，您必須在初始化集合之後設定這些屬性 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 。

## <a name="populating-a-picker-with-data-using-data-binding"></a>使用資料系結將資料填入選擇器

您 [`Picker`](xref:Xamarin.Forms.Picker) 也可以使用資料系結，將其屬性系結至集合，以填入資料 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) `IList` 。 在 XAML 中，這會使用 [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) 標記延伸來達成：

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

屬性資料會系結 [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) 至 `Monkeys` 連接視圖模型的屬性，該屬性會傳回 `IList<Monkey>` 集合。 下列程式碼範例顯示 `Monkey` 類別，其中包含四個屬性：

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

系結至物件的清單時， [`Picker`](xref:Xamarin.Forms.Picker) 必須告知要從每個物件顯示的屬性。 這是藉由將 [`ItemDisplayBinding`](xref:Xamarin.Forms.Picker.ItemDisplayBinding) 屬性設定為每個物件的必要屬性來達成。 在上述程式碼範例中， `Picker` 設定為顯示每個 `Monkey.Name` 屬性值。

### <a name="responding-to-item-selection"></a>回應專案選取

當物件變更時，可以使用資料系結將物件設定為 [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 屬性值：

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

[`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem)屬性資料系結至 `SelectedMonkey` 連接視圖模型的屬性，其類型為 `Monkey` 。 因此，當使用者選取中的專案時 [`Picker`](xref:Xamarin.Forms.Picker) ， `SelectedMonkey` 屬性會設定為選取的 `Monkey` 物件。 `SelectedMonkey`物件資料會在使用者介面中顯示 [`Label`](xref:Xamarin.Forms.Label) ，而 views 會顯示 [`Image`](xref:Xamarin.Forms.Image) ：

![Picker 項目選取](populating-itemssource-images/monkeys.png)

> [!NOTE]
> 請注意， [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) 和 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 屬性預設都支援雙向系結。

## <a name="related-links"></a>相關連結

- [選擇器示範 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [猴子 App (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)
- [可系結選擇器 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
- [選擇器 API](xref:Xamarin.Forms.Picker)