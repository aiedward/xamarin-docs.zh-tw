---
title: 將資料加入至選擇器的項目集合
description: 選擇器 檢視是從資料清單中選取的文字項目控制項。 本文說明如何將它加入至項目集合中，填入資料的選擇器以及如何回應使用者所選擇的項目。
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: 63a72861895f79d2d0154297f88610ddb8bb8beb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="adding-data-to-a-pickers-items-collection"></a>將資料加入至選擇器的項目集合

_選擇器 檢視是從資料清單中選取的文字項目控制項。本文說明如何將它加入至項目集合中，填入資料的選擇器以及如何回應使用者所選擇的項目。_

## <a name="populating-a-picker-with-data"></a>填入資料的選擇器

Xamarin.Forms 2.3.4，填入的程序之前[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)資料已加入要顯示的唯讀資料[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合，其中的型別`IList<string>`. 集合中的每個項目必須是型別`string`。 可以在 XAML 中加入項目，初始化`Items`屬性的清單`x:String`項目：

```xaml
<Picker Title="Select a monkey">
  <Picker.Items>
    <x:String>Baboon</x:String>
    <x:String>Capuchin Monkey</x:String>
    <x:String>Blue Monkey</x:String>
    <x:String>Squirrel Monkey</x:String>
    <x:String>Golden Lion Tamarin</x:String>
    <x:String>Howler Monkey</x:String>
    <x:String>Japanese Macaque</x:String>
  </Picker.Items>
</Picker>
```

對等的 C# 程式碼如下所示：

```csharp
var picker = new Picker { Title = "Select a monkey" };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

除了新增資料使用`Items.Add`方法，可以也將資料插入集合使用`Items.Insert`方法。

## <a name="responding-to-item-selection"></a>回應的項目選取

A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)支援選取的一個項目一次。 當使用者選取一個項目， [ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)事件引發，而[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)會更新屬性以整數，代表在清單中選取之項目的索引。 `SelectedIndex`屬性是以零為起始的數字，指出使用者選取的項目。 如果不選取任何項目，也就是當`Picker`是先建立並初始化，`SelectedIndex`是-1。

> [!NOTE]
> 項目中的選取項目行為[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可以自訂與特定平台在 iOS 上。 如需詳細資訊，請參閱[控制選擇器項目選取](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode)。

下列程式碼範例示範`OnPickerSelectedIndexChanged`事件處理常式方法，也就是執行時[ `SelectedIndexChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Picker.SelectedIndexChanged/)事件引發：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = picker.Items[selectedIndex];
  }
}
```

這個方法會取得[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)屬性值，並使用值來擷取選取的項目從[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合。 因為每個項目中`Items`集合是`string`，它們可以顯示[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)而不需要轉型。

> [!NOTE]
> A [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)可以藉由設定顯示特定的項目初始化[ `SelectedIndex` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.SelectedIndex/)屬性。 不過，`SelectedIndex`屬性必須設定為晚初始化[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合。

## <a name="summary"></a>總結

[ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)檢視是從資料清單中選取的文字項目控制項。 這篇文章解釋如何填入`Picker`將它加入至資料[ `Items` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Picker.Items/)集合，以及如何回應使用者所選擇的項目。 這是使用的程序`Picker`Xamarin.Forms 2.3.4 之前。


## <a name="related-links"></a>相關連結

- [選擇器示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PickerDemo/)
- [選擇器](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/)
