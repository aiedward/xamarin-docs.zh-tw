---
title: IOS 上的 TimePicker 專案選擇
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來控制 TimePicker 中的專案選擇何時發生。
ms.prod: xamarin
ms.assetid: 554AC877-8698-4B8C-A676-80DD64305A06
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: 818f368da8ebb375fbacd97d3d48185ba60470d4
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292558"
---
# <a name="timepicker-item-selection-on-ios"></a>IOS 上的 TimePicker 專案選擇

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定的控制項會在[`TimePicker`](xref:Xamarin.Forms.TimePicker)中發生專案選取時，讓使用者指定在流覽控制項中的專案時，或只在按下 [**完成**] 按鈕時，才會發生選取專案。 它會在 XAML 中使用，方法是將 `TimePicker.UpdateMode` 附加屬性設定為 `UpdateMode` 列舉的值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <TimePicker Time="14:00:00"
                   ios:TimePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

或者，也可以C#使用 Fluent API 來取用：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`TimePicker.On<iOS>` 方法指定此平臺特定的只會在 iOS 上執行。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間中的 `TimePicker.SetUpdateMode` 方法是用來控制專案選取的時機，而 `UpdateMode` 列舉提供兩個可能的值：

- `Immediately` –當使用者流覽[`TimePicker`](xref:Xamarin.Forms.TimePicker)中的專案時，就會發生專案選擇。 這是 Xamarin 中的預設行為。
- `WhenFinished` –只有在使用者按下[`TimePicker`](xref:Xamarin.Forms.TimePicker)中的 [**完成**] 按鈕之後，才會選取專案。

此外，`SetUpdateMode` 方法可以藉由呼叫 `UpdateMode` 方法（傳回目前的 `UpdateMode`）來切換列舉值：

```csharp
switch (timePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

結果是指定的 `UpdateMode` 會套用至[`TimePicker`](xref:Xamarin.Forms.TimePicker)，以控制專案選取的時機：

[![TimePicker 更新模式的螢幕擷取畫面](timepicker-selection-images/timepicker-updatemode.png "TimePicker UpdateMode 平臺特定")](timepicker-selection-images/timepicker-updatemode-large.png#lightbox "TimePicker UpdateMode 平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
