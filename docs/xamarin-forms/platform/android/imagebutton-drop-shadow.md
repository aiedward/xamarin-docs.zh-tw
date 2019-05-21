---
title: 在 Android 上的 ImageButton 下拉式陰影
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Android 平台特定，可讓 ImageButton 上的陰影。
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: ba666d2474934b0cd4349e607329433ae1988b47
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926811"
---
# <a name="imagebutton-drop-shadows-on-android"></a>在 Android 上的 ImageButton 下拉式陰影

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

此 Android 平台專屬來啟用下拉式陰影`ImageButton`。 它由在 XAML 中設定`ImageButton.IsShadowEnabled`可繫結的屬性，以`true`，以及數個控制陰影的其他選擇性可繫結屬性：

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

或者，它可以取用從 C# 使用 fluent API:

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
> 做為一部分繪製陰影`ImageButton`背景，以及背景只繪製如果`BackgroundColor`屬性設定。 因此，陰影就不會繪製如果`ImageButton.BackgroundColor`屬性未設定。

`ImageButton.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 `ImageButton.SetIsShadowEnabled`方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，可用來控制是否已啟用下拉式陰影`ImageButton`。 此外，下列方法可以叫用來控制陰影：

- `SetShadowColor` – 設定延伸陰影的色彩。 預設色彩是[ `Color.Default` ](xref:Xamarin.Forms.Color.Default*)。
- `SetShadowOffset` – 設定延伸陰影的位移。 位移會變更陰影轉換，並指定為方向[ `Size` ](xref:Xamarin.Forms.Size)值。 `Size`結構的值會以裝置獨立單位，與正在向左 （負值） 或向右 （正值），距離的第一個值和第二個值在上述的距離 （負值） 或下方 （正值）. 這個屬性的預設值是 （0.0，0.0），這會導致陰影正在轉換周圍每一端`ImageButton`。
- `SetShadowRadius`– 設定柔化半徑，用來呈現下拉式陰影。 預設半徑值為 10.0。

> [!NOTE]
> 可查詢下拉式陰影的狀態，藉由呼叫`GetIsShadowEnabled`， `GetShadowColor`， `GetShadowOffset`，和`GetShadowRadius`方法。

結果是，您可以上啟用下拉式陰影`ImageButton`:

![](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png "使用下拉式陰影的 ImageButton")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
