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
ms.openlocfilehash: c65cac4c777150185524b291adc6e9d1e79958d3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138550"
---
# <a name="datepicker-item-selection-on-ios"></a>IOS 上的 DatePicker 專案選擇

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

當專案選取發生在時，此 iOS 平臺特定的控制項 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 可讓使用者指定在流覽控制項中的專案時，或只在按下 [**完成**] 按鈕時，才會發生選取專案。 它會在 XAML 中使用，方法是將 `DatePicker.UpdateMode` 附加屬性設為列舉的值 `UpdateMode` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <DatePicker MinimumDate="01/01/2020"
                   MaximumDate="12/31/2020"
                   ios:DatePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`DatePicker.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `DatePicker.SetUpdateMode`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 可用來控制何時會選取專案，而 `UpdateMode` 列舉會提供兩個可能的值：

- `Immediately`–當使用者流覽中的專案時，就會發生專案選取 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 。 這是中的預設行為 Xamarin.Forms 。
- `WhenFinished`–只有在使用者按下中的 [**完成**] 按鈕之後，才會進行專案選取 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 。

此外， `SetUpdateMode` 方法可以藉由呼叫方法來切換列舉值，這會傳回 `UpdateMode` 目前的 `UpdateMode` ：

```csharp
switch (datePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

結果是會將指定的套用 `UpdateMode` 至，以 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 控制專案選取的時機：

[![DatePicker 更新模式的螢幕擷取畫面](datepicker-selection-images/datepicker-updatemode.png "DatePicker UpdateMode 平臺特定")](datepicker-selection-images/datepicker-updatemode-large.png#lightbox "DatePicker UpdateMode 平臺特定")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
