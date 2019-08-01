---
title: 設定選擇器的 ItemsSource 屬性
description: 選擇器 檢視是從資料的清單中選取的文字項目控制項。 這篇文章說明如何藉由設定 ItemsSource 屬性，填入資料的選擇器，以及如何回應使用者的項目選取。
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: 803cd1babc32e3a42d957c4bac0cc93c4552fb8c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656127"
---
# <a name="setting-a-pickers-itemssource-property"></a>設定選擇器的 ItemsSource 屬性

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)

_選擇器 檢視是從資料的清單中選取的文字項目控制項。這篇文章說明如何藉由設定 ItemsSource 屬性，填入資料的選擇器，以及如何回應使用者的項目選取。_

已增強 Xamarin.Forms 2.3.4 [ `Picker` ](xref:Xamarin.Forms.Picker)加上能夠藉由設定以資料填入的檢視其[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)屬性，以及從擷取選取的項目[`SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)屬性。 此外，變更選取的項目文字的色彩，藉由設定[ `TextColor` ](xref:Xamarin.Forms.Picker.TextColor)屬性設[ `Color` ](xref:Xamarin.Forms.Color)。

## <a name="populating-a-picker-with-data"></a>以資料填入選擇器

A [ `Picker` ](xref:Xamarin.Forms.Picker)可以填入資料，藉由設定其[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)屬性設`IList`集合。 集合中的每個項目必須是的或衍生自類型`object`。 可以在 XAML 中加入項目，初始化`ItemsSource`從項目的陣列的屬性：

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
> 請注意，`x:Array`元素需要`Type`屬性，指出陣列中項目的類型。

對等的 C# 程式碼如下所示：

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

A [ `Picker` ](xref:Xamarin.Forms.Picker)支援一次選取一個項目。 當使用者選取項目， [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件引發時， [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)屬性更新為整數，表示選取的項目在清單中，索引和[`SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)屬性更新為`object`代表選取的項目。 [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)屬性是以零為起始的數字，指出使用者已選取的項目。 如果未不選取任何項目，則這是當[ `Picker` ](xref:Xamarin.Forms.Picker)先建立並初始化，`SelectedIndex`會是-1。

> [!NOTE]
> 項目中的選取行為[ `Picker` ](xref:Xamarin.Forms.Picker)可以自訂與特定平台在 iOS 上。 如需詳細資訊，請參閱 <<c0> [ 控制選擇器項目選取](~/xamarin-forms/platform/ios/picker-selection.md)。

下列程式碼範例示範如何擷取[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)屬性值從[ `Picker` ](xref:Xamarin.Forms.Picker)在 XAML 中：

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

對等的 C# 程式碼如下所示：

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

此外，事件處理常式可執行的時機[ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)引發事件：

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

這個方法會取得[ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)屬性值，並使用值來擷取選取的項目，從[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)集合。 這是功能上等於擷取選取的項目，從[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)屬性。 請注意，在每個項目`ItemsSource`集合屬於類型`object`，所以必須轉換成`string`顯示。

> [!NOTE]
> A [ `Picker` ](xref:Xamarin.Forms.Picker)可以藉由設定顯示特定的項目初始化[ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)或是[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)屬性。 不過，這些屬性必須設定在初始化之後[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)集合。

## <a name="populating-a-picker-with-data-using-data-binding"></a>使用資料系結將資料填入選擇器

A [ `Picker` ](xref:Xamarin.Forms.Picker)可以也使用資料來填入繫結中使用資料繫結其[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)屬性設`IList`集合。 在 XAML 中，這達成與[ `Binding` ](xref:Xamarin.Forms.Xaml.BindingExtension)標記延伸模組：

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}" />
```

對等的 C# 程式碼如下所示：

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)屬性的資料繫結至`Monkeys`連線的檢視模型，它會傳回屬性`IList<Monkey>`集合。 下列程式碼範例示範`Monkey`類別，其中包含四個屬性：

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

繫結至物件的清單時[ `Picker` ](xref:Xamarin.Forms.Picker)必須被告知要顯示的每個物件的屬性。 這藉由設定來達成[ `ItemDisplayBinding` ](xref:Xamarin.Forms.Picker.ItemDisplayBinding)必要的屬性來從每個物件的屬性。 在上述的程式碼範例`Picker`設定為顯示每個`Monkey.Name`屬性值。

### <a name="responding-to-item-selection"></a>回應專案選取

資料繫結可以用來把物件設為[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)屬性值變更時：

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

對等的 C# 程式碼如下所示：

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

[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)屬性的資料繫結至`SelectedMonkey`連線的檢視模型，也就是型別的屬性`Monkey`。 因此，當使用者選取中的項目時，才[ `Picker` ](xref:Xamarin.Forms.Picker)，則`SelectedMonkey`屬性會設定於所選`Monkey`物件。 `SelectedMonkey`物件的資料會顯示在使用者介面[ `Label` ](xref:Xamarin.Forms.Label)並[ `Image` ](xref:Xamarin.Forms.Image)檢視：

![](populating-itemssource-images/monkeys.png "選擇器的項目選取")

> [!NOTE]
> 請注意， [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)並[ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)預設屬性都支援雙向繫結。

## <a name="related-links"></a>相關連結

- [選擇器示範 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Monkey 應用程式 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)
- [可繫結選擇器 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
- [選擇器 API](xref:Xamarin.Forms.Picker)
