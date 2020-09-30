---
title: Android 上的按鈕填補和遮蔽
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的 Android 平臺，它會使用 Android 按鈕的預設填補和陰影值。
ms.prod: xamarin
ms.assetid: BD2B60D1-DE6E-4691-A777-8EC5F560A4E9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fa0bf466886309978743f4225b72520eb340fd1a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563805"
---
# <a name="button-padding-and-shadows-on-android"></a>Android 上的按鈕填補和遮蔽

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定的控制 Xamarin.Forms 按鈕是否使用 Android 按鈕的預設填補和陰影值。 它是在 XAML 中使用，方法是將 [`Button.UseDefaultPadding`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) 和 [`Button.UseDefaultShadow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) 附加屬性設定為 `boolean` 值：

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

`Button.On<Android>`方法指定此平臺特定的只會在 Android 上執行。 [ `Button.SetUseDefaultPadding` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. SetUseDefaultPadding (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。按鈕}、system.string) # A3 和 [ `Button.SetUseDefaultShadow` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. SetUseDefaultShadow (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。按鈕}、system.string) # A7 方法（在命名空間中）， [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 可用來控制按鈕是否 Xamarin.Forms 使用 Android 按鈕的預設填補和陰影值。 此外，[ `Button.UseDefaultPadding` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. UseDefaultPadding (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。按鈕} ) # A3 和 [ `Button.UseDefaultShadow` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. UseDefaultShadow (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。按鈕} ) # A7 方法可以用來傳回按鈕是否分別使用預設的填補值和預設的陰影值。

結果是 Xamarin.Forms 按鈕可以使用 Android 按鈕的預設填補和陰影值：

![Android 按鈕上的預設填補和陰影值](button-padding-shadow-images/button-padding-and-shadow.png)

請注意，在上面的螢幕擷取畫面中，每個 [`Button`](xref:Xamarin.Forms.Button) 都有相同的定義，不同之處在于右手邊 `Button` 使用 Android 按鈕的預設填補和陰影值。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)