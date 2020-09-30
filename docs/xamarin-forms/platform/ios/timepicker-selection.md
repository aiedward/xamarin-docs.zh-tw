---
title: IOS 上的 TimePicker 專案選取專案
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，以控制在 TimePicker 中發生專案選擇時。
ms.prod: xamarin
ms.assetid: 554AC877-8698-4B8C-A676-80DD64305A06
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 372c268c13c50719953ac63bcc43cc8d9bff4bdd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563714"
---
# <a name="timepicker-item-selection-on-ios"></a>IOS 上的 TimePicker 專案選取專案

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

當選取專案時，這個 iOS 平臺特定的控制項 [`TimePicker`](xref:Xamarin.Forms.TimePicker) ，可讓使用者在流覽控制項中的專案時，或只在按下 [ **完成** ] 按鈕時，指定發生專案選取。 它是在 XAML 中使用，方法是將 `TimePicker.UpdateMode` 附加屬性設定為 `UpdateMode` 列舉值：

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

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`TimePicker.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 在 `TimePicker.SetUpdateMode` 命名空間中，方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制專案選取的發生時機，而 `UpdateMode` 列舉會提供兩個可能的值：

- `Immediately` –當使用者流覽中的專案時，就會發生專案選取 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 。 這是中的預設行為 Xamarin.Forms 。
- `WhenFinished` –只有在使用者按下的 [ **完成** ] 按鈕時，才會選取專案 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 。

此外， `SetUpdateMode` 方法可以藉由呼叫 `UpdateMode` 方法（傳回目前的）來切換列舉值 `UpdateMode` ：

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

結果是，會將指定的套用 `UpdateMode` 至 [`TimePicker`](xref:Xamarin.Forms.TimePicker) ，以控制何時會選取專案：

[![TimePicker 更新模式的螢幕擷取畫面](timepicker-selection-images/timepicker-updatemode.png "TimePicker UpdateMode 平臺特定")](timepicker-selection-images/timepicker-updatemode-large.png#lightbox "TimePicker UpdateMode 平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)