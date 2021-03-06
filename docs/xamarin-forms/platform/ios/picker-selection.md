---
title: IOS 上的選擇器專案選取專案
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，以控制在選擇器中發生專案選擇時。
ms.prod: xamarin
ms.assetid: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5b7315fce6a51c570c4486c54fdfc81932a4b8f8
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372678"
---
# <a name="picker-item-selection-on-ios"></a>IOS 上的選擇器專案選取專案

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

當選取專案時，這個 iOS 平臺特定的控制項 [`Picker`](xref:Xamarin.Forms.Picker) ，可讓使用者在流覽控制項中的專案時，或只在按下 [ **完成** ] 按鈕時，指定發生專案選取。 它是在 XAML 中使用，方法是將 `Picker.UpdateMode` 附加屬性設定為 `UpdateMode` 列舉值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`Picker.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 在 `Picker.SetUpdateMode` 命名空間中，方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制專案選取的發生時機，而 `UpdateMode` 列舉會提供兩個可能的值：

- `Immediately` –當使用者流覽中的專案時，就會發生專案選取 [`Picker`](xref:Xamarin.Forms.Picker) 。 這是中的預設行為 Xamarin.Forms 。
- `WhenFinished` –只有在使用者按下的 [ **完成** ] 按鈕時，才會選取專案 [`Picker`](xref:Xamarin.Forms.Picker) 。

此外， `SetUpdateMode` 方法可以藉由呼叫 `UpdateMode` 方法（傳回目前的）來切換列舉值 `UpdateMode` ：

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

結果是，會將指定的套用 `UpdateMode` 至 [`Picker`](xref:Xamarin.Forms.Picker) ，以控制何時會選取專案：

[![選擇器 UpdateMode 平臺特定](picker-selection-images/picker-updatemode.png)](picker-selection-images/picker-updatemode-large.png#lightbox "選擇器 UpdateMode Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)