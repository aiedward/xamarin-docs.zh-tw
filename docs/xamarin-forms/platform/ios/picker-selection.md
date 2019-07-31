---
title: IOS 上的選擇器專案選擇
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 iOS 平臺特定的來控制選擇器中發生專案選取時的情況。
ms.prod: xamarin
ms.assetid: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 57420921100c99db1e2c3a5259ece30cfda719f2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651849"
---
# <a name="picker-item-selection-on-ios"></a>IOS 上的選擇器專案選擇

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

當專案選取發生在[`Picker`](xref:Xamarin.Forms.Picker)時, 此 iOS 平臺特定的控制項可讓使用者指定在流覽控制項中的專案時, 或只在按下 [**完成**] 按鈕時, 才會發生選取專案。 它由在 XAML 中設定`Picker.UpdateMode`附加屬性的值`UpdateMode`列舉型別：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

`Picker.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 `Picker.SetUpdateMode`方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，用來控制何時發生的項目選取，請使用`UpdateMode`提供兩個可能值的列舉型別：

- `Immediately` – 使用者瀏覽中的項目，就會發生的項目選取[ `Picker` ](xref:Xamarin.Forms.Picker)。 這是在 Xamarin.Forms 中的預設行為。
- `WhenFinished` – 使用者已按下之後，才會發生的項目選取**完成**按鈕[ `Picker` ](xref:Xamarin.Forms.Picker)。

颾魤 ㄛ`SetUpdateMode`方法可用來切換列舉值，藉由呼叫`UpdateMode`方法，以傳回目前`UpdateMode`:

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

結果是，指定`UpdateMode`套用至[ `Picker` ](xref:Xamarin.Forms.Picker)，這會控制當項目選取項目，就會發生：

[![](picker-selection-images/picker-updatemode.png "選擇器 UpdateMode 平台專屬")](picker-selection-images/picker-updatemode-large.png#lightbox "選擇器 UpdateMode 平台專屬")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
