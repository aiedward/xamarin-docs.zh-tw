---
title: 將資料新增到 Picker 的項目集合
description: 選擇器視圖是從資料清單中選取文字專案的控制項。 本文說明如何藉由將資料新增至 Items 集合，以及如何回應使用者的選取專案，來填入選擇器。
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d65352022057ce32bd969950c2165ad530c05bbb
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559619"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>將資料新增到 Picker 的項目集合

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)

_選擇器視圖是從資料清單中選取文字專案的控制項。本文說明如何藉由將資料新增至 Items 集合，以及如何回應使用者的選取專案，來填入選擇器。_

## <a name="populating-a-picker-with-data"></a>以資料填入選擇器

Xamarin.Forms在2.3.4 之前， [`Picker`](xref:Xamarin.Forms.Picker) 使用資料填入的程式是要將顯示的資料加入至唯讀 [`Items`](xref:Xamarin.Forms.Picker.Items) 集合，其類型為 `IList<string>` 。 集合中的每個專案都必須屬於類型 `string` 。 您可以使用專案清單來初始化屬性，以在 XAML 中新增專案 `Items` `x:String` ：

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

除了使用方法來加入資料之外 `Items.Add` ，也可以使用方法，將資料插入至集合 `Items.Insert` 。

## <a name="responding-to-item-selection"></a>回應專案選取

[`Picker`](xref:Xamarin.Forms.Picker)支援一次選取一個專案。 當使用者選取專案時， [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) 就會引發事件，並將 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 屬性更新為整數，表示清單中所選取專案的索引。 `SelectedIndex`屬性是以零為基底的數位，表示使用者選取的專案。 如果未選取任何專案，則在 `Picker` 第一次建立和初始化時，將會 `SelectedIndex` 是-1。

> [!NOTE]
> 中的專案選取行為 [`Picker`](xref:Xamarin.Forms.Picker) 可以在具有平臺特定的 iOS 上自訂。 如需詳細資訊，請參閱 [控制選擇器專案](~/xamarin-forms/platform/ios/picker-selection.md)選取。

下列程式碼範例示範 `OnPickerSelectedIndexChanged` 事件處理常式方法，這個方法會在 [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) 事件引發時執行：

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

這個方法會 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 取得屬性值，並使用該值從集合中取出選取的專案 [`Items`](xref:Xamarin.Forms.Picker.Items) 。 因為集合中的每個專案 `Items` 都是 `string` ，所以可以透過來顯示， [`Label`](xref:Xamarin.Forms.Label) 而不需要轉換。

> [!NOTE]
> 您 [`Picker`](xref:Xamarin.Forms.Picker) 可以藉由設定屬性來初始化，以顯示特定的專案 [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) 。 不過，您 `SelectedIndex` 必須在初始化集合之後設定屬性 [`Items`](xref:Xamarin.Forms.Picker.Items) 。

## <a name="related-links"></a>相關連結

- [選擇器示範 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Picker](xref:Xamarin.Forms.Picker)