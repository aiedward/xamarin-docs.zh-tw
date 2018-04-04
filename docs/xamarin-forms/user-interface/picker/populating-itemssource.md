---
title: 選擇器 ItemsSource 屬性設定
description: 選擇器 檢視是從資料清單中選取的文字項目控制項。 本文說明如何藉由設定 ItemsSource 屬性中，填入資料的選擇器以及如何回應使用者所選擇的項目。
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: bf3940bc1bc0318bad4d785388f9dc9292af80ca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="setting-a-pickers-itemssource-property"></a>選擇器 ItemsSource 屬性設定

_選擇器 檢視是從資料清單中選取的文字項目控制項。本文說明如何藉由設定 ItemsSource 屬性中，填入資料的選擇器以及如何回應使用者所選擇的項目。_

已經強化 Xamarin.Forms 2.3.4 [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)檢視加入的能力以資料填入藉由設定其[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)屬性，以及從擷取選取的項目[`SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)屬性。 此外，變更為選取的項目文字的色彩，藉由設定[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.TextColor/)屬性[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)。

## <a name="populating-a-picker-with-data"></a>填入資料的選擇器

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可以藉由設定以資料填入其[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)屬性`IList`集合。 集合中的每個項目必須是的或衍生自類型`object`。 可以在 XAML 中加入項目，初始化`ItemsSource`屬性從陣列的項目：

```xaml
<Picker x:Name="picker" Title="Select a monkey">
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

var picker = new Picker { Title = "Select a monkey" };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>回應的項目選取

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)支援選取的一個項目一次。 當使用者選取一個項目， [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)事件引發[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)會更新屬性以整數，代表在清單中，選取之項目的索引和[`SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)屬性更新為`object`表示選取的項目。 [ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)屬性是以零為起始的數字，指出使用者選取的項目。 如果不選取任何項目，也就是當[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)是先建立並初始化，`SelectedIndex`是-1。

> [!NOTE]
> 項目中的選取項目行為[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可以自訂與特定平台在 iOS 上。 如需詳細資訊，請參閱[控制選擇器項目選取](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode)。

下列程式碼範例示範如何擷取[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)屬性值從[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)在 XAML 中：

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

對等的 C# 程式碼如下所示：

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

此外，事件處理常式可以是執行時[ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)事件引發：

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

這個方法會取得[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)屬性值，並使用值來擷取選取的項目從[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)集合。 這在功能上相當於擷取選取的項目從[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)屬性。 請注意，每個項目`ItemsSource`集合屬於型別`object`，所以必須轉換成`string`顯示。

> [!NOTE]
> A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可以藉由設定顯示特定的項目初始化[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)或[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)屬性。 不過，這些屬性必須設定為晚初始化[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)集合。

## <a name="populating-a-picker-with-data-using-data-binding"></a>填入與使用資料繫結的資料選擇器

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可以也填入資料繫結中使用資料繫結其[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)屬性`IList`集合。 在 XAML 中，這達成與[ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/)標記延伸：

```xaml
<Picker Title="Select a monkey" ItemsSource="{Binding Monkeys}" ItemDisplayBinding="{Binding Name}" />
```

對等的 C# 程式碼如下所示：

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)屬性資料繫結至`Monkeys`連線的檢視模型，它會傳回屬性的`IList<Monkey>`集合。 下列程式碼範例示範`Monkey`類別，其中包含四個屬性：

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

當繫結至物件的清單， [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)必須告知要顯示的每個物件的內容。 這藉由設定[ `ItemDisplayBinding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemDisplayBinding/)必要的屬性來從每個物件的屬性。 上述的程式碼範例`Picker`設為顯示每個`Monkey.Name`屬性值。

### <a name="responding-to-item-selection"></a>回應的項目選取

資料繫結可用來將物件設定[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)屬性值變更時：

```xaml
<Picker Title="Select a monkey"
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
var picker = new Picker { Title = "Select a monkey" };
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

[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)屬性資料繫結至`SelectedMonkey`連線的檢視模型的型別屬性`Monkey`。 因此，當使用者選取中的項目[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)、`SelectedMonkey`屬性會設定至所選`Monkey`物件。 `SelectedMonkey`物件資料會顯示在使用者介面中[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)和[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)檢視：

![](populating-itemssource-images/monkeys.png "選擇器的項目選取")

> [!NOTE]
> 請注意， [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedItem/)和[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)預設屬性都支援雙向繫結。

## <a name="summary"></a>總結

[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)檢視是從資料清單中選取的文字項目控制項。 這篇文章解釋如何填入`Picker`藉由設定資料[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.ItemsSource/)屬性，以及如何回應使用者所選擇的項目。 這種方法在 Xamarin.Forms 2.3.4 引進，是建議的方式與互動`Picker`。


## <a name="related-links"></a>相關連結

- [選擇器示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [猴子應用程式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/MonkeyAppPicker/)
- [可繫結選擇器 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
- [選擇器](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
