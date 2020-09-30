---
title: 在 Android 上 ImageButton 陰影
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用可在 ImageButton 上啟用陰影的 Android 平臺特定。
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c355816ec1b89801814b217cad8a23be2f453226
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563792"
---
# <a name="imagebutton-drop-shadows-on-android"></a>在 Android 上 ImageButton 陰影

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定用來啟用上的投影 `ImageButton` 。 它是在 XAML 中使用，方法是將可系結 `ImageButton.IsShadowEnabled` 屬性設定為 `true` ，以及一些可控制陰影的其他選擇性可系結屬性：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
       <ImageButton ...
                    Source="XamarinLogo.png"
                    BackgroundColor="GhostWhite"
                    android:ImageButton.IsShadowEnabled="true"
                    android:ImageButton.ShadowColor="Gray"
                    android:ImageButton.ShadowRadius="12">
            <android:ImageButton.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </android:ImageButton.ShadowOffset>
        </ImageButton>
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var imageButton = new Xamarin.Forms.ImageButton { Source = "XamarinLogo.png", BackgroundColor = Color.GhostWhite, ... };
imageButton.On<Android>()
           .SetIsShadowEnabled(true)
           .SetShadowColor(Color.Gray)
           .SetShadowOffset(new Size(10, 10))
           .SetShadowRadius(12);
```

> [!IMPORTANT]
> 陰影會繪製為背景的一部分 `ImageButton` ，而且只有在設定屬性時，才會繪製背景 `BackgroundColor` 。 因此，如果未設定屬性，將不會繪製陰影 `ImageButton.BackgroundColor` 。

`ImageButton.On<Android>`方法指定此平臺特定的只會在 Android 上執行。 在 `ImageButton.SetIsShadowEnabled` 命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 是用來控制是否在上啟用了陰影 `ImageButton` 。 此外，也可以叫用下列方法來控制陰影：

- `SetShadowColor` –設定陰影的色彩。 預設色彩為 [`Color.Default`](xref:Xamarin.Forms.Color.Default*) 。
- `SetShadowOffset` –設定陰影的位移。 位移會變更陰影轉換的方向，並指定為 [`Size`](xref:Xamarin.Forms.Size) 值。 `Size`結構值是以與裝置無關的單位來表示，而第一個值是左邊的距離 (負數值) 或右 (正值) ，而第二個值則是超過 (負數值) 或低於 (正數值) 的距離。 這個屬性的預設值是 (0.0，0.0) ，這會導致在的每一側周圍轉換影子 `ImageButton` 。
- `SetShadowRadius`–設定用來呈現投影的模糊半徑。 預設半徑值為10.0。

> [!NOTE]
> 您可以藉由呼叫 `GetIsShadowEnabled` 、 `GetShadowColor` 、 `GetShadowOffset` 和方法，來查詢陰影的狀態 `GetShadowRadius` 。

結果是可以在上啟用陰影 `ImageButton` ：

![具有陰影的 ImageButton](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)