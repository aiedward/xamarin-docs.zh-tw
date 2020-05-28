---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8872c6748ba778a2622d82803d580c781bd282cd
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139629"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>將資料新增到 Picker 的項目集合

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)

_[選擇器] 視圖是從資料清單中選取文字專案的控制項。本文說明如何在選擇器中填入資料，方法是將它加入至 Items 集合，以及如何回應使用者選取的專案。_

## <a name="populating-a-picker-with-data"></a>以資料填入選擇器

Xamarin.Forms在2.3.4 之前，用來填入資料的程式， [`Picker`](xref:Xamarin.Forms.Picker) 是將要顯示的資料加入至唯讀 [`Items`](xref:Xamarin.Forms.Picker.Items) 集合，其類型為 `IList<string>` 。 集合中的每個專案都必須是類型 `string` 。 藉由初始化 `Items` 具有專案清單的屬性，可以在 XAML 中加入專案 `x:String` ：

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red">
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

對等的 c # 程式碼如下所示：

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

除了使用方法加入資料之外 `Items.Add` ，也可以使用方法，將資料插入集合中 `Items.Insert` 。

## <a name="responding-to-item-selection"></a>回應專案選取

[`Picker`](xref:Xamarin.Forms.Picker)支援一次選取一個專案。 當使用者選取專案時， [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) 就會引發事件，並將 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 屬性更新為整數，代表清單中所選取專案的索引。 `SelectedIndex`屬性是以零為基底的數位，表示使用者選取的專案。 如果未選取任何專案，這是 `Picker` 第一次建立並初始化時的情況， `SelectedIndex` 將會是-1。

> [!NOTE]
> 中的專案選取行為 [`Picker`](xref:Xamarin.Forms.Picker) 可以使用平臺特定，在 iOS 上進行自訂。 如需詳細資訊，請參閱[控制選擇器專案選擇](~/xamarin-forms/platform/ios/picker-selection.md)。

下列程式碼範例示範 `OnPickerSelectedIndexChanged` 事件處理常式方法，它會在 [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) 事件引發時執行：

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

這個方法會 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 取得屬性值，並使用值來抓取集合中選取的專案 [`Items`](xref:Xamarin.Forms.Picker.Items) 。 因為集合中的每個專案 `Items` 都是 `string` ，所以可以顯示， [`Label`](xref:Xamarin.Forms.Label) 而不需要轉換。

> [!NOTE]
> [`Picker`](xref:Xamarin.Forms.Picker)可以初始化，藉由設定屬性來顯示特定專案 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 。 不過，在 `SelectedIndex` 初始化集合之後，必須設定屬性 [`Items`](xref:Xamarin.Forms.Picker.Items) 。

## <a name="related-links"></a>相關連結

- [選擇器示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Picker](xref:Xamarin.Forms.Picker)
