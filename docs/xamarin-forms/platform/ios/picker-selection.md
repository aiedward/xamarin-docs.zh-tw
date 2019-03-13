---
title: 在 iOS 上的選擇器項目選取
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台特定的控制項選擇器中的項目選取項目發生時。
ms.prod: xamarin
ms.assetid: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 21c4c289a3fd30db890be6811875412ce4913cf5
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208713"
---
# <a name="picker-item-selection-on-ios"></a>在 iOS 上的選擇器項目選取

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 iOS 平台特定的控制項中的項目選取項目發生時[ `Picker` ](xref:Xamarin.Forms.Picker)，讓使用者指定的項目選取瀏覽控制項，項目時，或只要**完成**已按下。 它由在 XAML 中設定`Picker.UpdateMode`附加屬性的值`UpdateMode`列舉型別：

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

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
