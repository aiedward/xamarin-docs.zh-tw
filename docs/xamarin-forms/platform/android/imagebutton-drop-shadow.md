---
title: Android 上的 ImageButton 投影
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，以在 ImageButton 上啟用陰影。
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d6f3304c9eeb87405ab303a80450a1cd8b2af267
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938056"
---
# <a name="imagebutton-drop-shadows-on-android"></a>Android 上的 ImageButton 投影

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定是用來在上啟用投影 `ImageButton` 。 它會在 XAML 中使用，方法是將可系結 `ImageButton.IsShadowEnabled` 屬性設定為 `true` ，以及一些可控制投影的其他選擇性可系結屬性：

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

或者，您也可以使用 Fluent API，從 c # 取用它：

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
> 陰影會繪製為背景的一部分 `ImageButton` ，而且只有在設定屬性時才會繪製背景 `BackgroundColor` 。 因此，如果未設定屬性，就不會繪製投影 `ImageButton.BackgroundColor` 。

`ImageButton.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 `ImageButton.SetIsShadowEnabled`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 是用來控制是否已在上啟用投影 `ImageButton` 。 此外，您可以叫用下列方法來控制陰影：

- `SetShadowColor`-設定陰影的色彩。 預設色彩為 [`Color.Default`](xref:Xamarin.Forms.Color.Default*) 。
- `SetShadowOffset`-設定陰影的位移。 位移會變更陰影的轉換方向，並指定為 [`Size`](xref:Xamarin.Forms.Size) 值。 `Size`結構值是以與裝置無關的單位來表示，第一個值是左邊的距離（負值）或右邊（正值），而第二個值是上方的距離（負值）或下方（正數值）。 這個屬性的預設值為（0.0，0.0），這會導致在的每一側轉換陰影 `ImageButton` 。
- `SetShadowRadius`–設定用來呈現投影的模糊半徑。 預設半徑值為10.0。

> [!NOTE]
> 您可以藉由呼叫 `GetIsShadowEnabled` 、 `GetShadowColor` 、 `GetShadowOffset` 和方法來查詢陰影的狀態 `GetShadowRadius` 。

結果是可以在上啟用投影 `ImageButton` ：

![具有投影的 ImageButton](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
