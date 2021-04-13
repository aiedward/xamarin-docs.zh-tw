---
title: 色彩 Xamarin.Forms
description: Xamarin.Forms色彩結構可讓您將色彩指定為 RGB 值、HSL 值、HSV 值或色彩名稱。
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/12/2021
ms.custom: contperf-fy21q4
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 99fb6a9f366dc1a851b28d29ad3fc55736e0b837
ms.sourcegitcommit: 77b501252287e29a597f85efc1242035348f2222
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368789"
---
# <a name="colors-in-xamarinforms"></a>色彩 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/workingwithcolors)

此 [`Color`](xref:Xamarin.Forms.Color) 結構可讓您以紅-綠-藍 (RGB) 值、色調-飽和度-亮度 (HSL) 值、色調-飽和度-值 (HSV) 值，或使用色彩名稱指定色彩。 也可以使用 Alpha 色板指出透明度。

[`Color`](xref:Xamarin.Forms.Color) 您可以使用函式來建立物件 `Color` ，這些函式可用來指定 [灰色陰影](xref:Xamarin.Forms.Color.%23ctor(System.Double))、 [rgb 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))或 [具有透明度的 rgb 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))。 在所有情況下，引數都是 `double` 介於0到1之間的值。

您也可以使用靜態方法來建立 [`Color`](xref:Xamarin.Forms.Color) 物件：

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) 適用于 `double` 從0到1的 RGB 值。
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) 從0到255的整數 RGB 值。
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) 適用于 `double` 具有透明度的 RGB 值。
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) 適用于具有透明度的整數 RGB 值。
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) 適用于 `double` 具有透明度的 HSL 值。
- [`Color.FromHsv`](xref:Xamarin.Forms.Color.FromHsv(System.Double,System.Double,System.Double)) 用於 `double` 從0到1的 HSV 值。
- [`Color.FromHsv`](xref:Xamarin.Forms.Color.FromHsv(System.Int32,System.Int32,System.Int32)) 從0到255的整數 HSV 值。
- [`Color.FromHsva`](xref:Xamarin.Forms.Color.FromHsva(System.Double,System.Double,System.Double,System.Double)) 適用于 `double` 具有透明度的 HSV 值。
- [`Color.FromHsva`](xref:Xamarin.Forms.Color.FromHsva(System.Int32,System.Int32,System.Int32,System.Int32)) 具有透明度的整數 HSV 值。
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) 針對 `uint` 計算為 (B + 256 \* (G + 256 \* (R + 256) ) ) 的值 \* 。
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) 針對 `string` 格式為 "#AARRGGBB" 或 "#RRGGBB" 或 "#ARGB" 或 "#RGB" 的十六進位數位，其中每個字母都對應至 Alpha、紅色、綠色和藍色通道的十六進位數位。

一旦建立之後， [`Color`](xref:Xamarin.Forms.Color) 物件就是不可變的。 您可以從下列屬性取得色彩的特性：

- [`R`](xref:Xamarin.Forms.Color.R)，表示色彩的紅色通道。
- [`G`](xref:Xamarin.Forms.Color.G)，表示色彩的綠色通道。
- [`B`](xref:Xamarin.Forms.Color.B)，表示色彩的藍色通道。
- [`A`](xref:Xamarin.Forms.Color.A)，表示色彩的 Alpha 色板。
- [`Hue`](xref:Xamarin.Forms.Color.Hue)，表示色彩的色調通道。
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)，表示色彩的飽和度頻道。
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)，表示色彩的亮度通道。

這些屬性都是 `double` 範圍從0到1的值。

## <a name="named-colors"></a>命名色彩

此 [`Color`](xref:Xamarin.Forms.Color) 結構也會定義通用色彩的240公用靜態唯讀欄位，例如 [`AliceBlue`](xref:Xamarin.Forms.Color.AliceBlue) 。

## <a name="coloraccent"></a>色彩。輔色

此 [`Color.Accent`](xref:Xamarin.Forms.Color.Accent) 值會產生平臺特定的 (，有時也會顯示在深色或淺色背景上可見的使用者可選取) 色彩。

## <a name="colordefault"></a>Color。預設值

[`Color.Default`](xref:Xamarin.Forms.Color.Default)值會定義將 `Color` 所有通道設定為-1 的，且其目的是要強制執行平臺的色彩配置。 因此，在不同的平臺上，它在不同的內容中有不同的意義。 平臺色彩配置預設為：

- iOS：淺色背景的深色文字。
- Android：淺色背景的深色文字。
- Windows：淺色背景上的深色文字。

## <a name="colortransparent"></a>色彩。透明

[`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)值會定義將 `Color` 所有通道設定為零的。

## <a name="modify-a-color"></a>修改色彩

有幾個實例方法允許修改現有的色彩以建立新的色彩：

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double)) 藉 `Color` 由提供的 delta 修改亮度來傳回。
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double)) 藉 `Color` 由修改 Alpha 來傳回，然後將它乘以提供的 Alpha 值。
- [`ToHex`](xref:Xamarin.Forms.Color.ToHex*) 傳回的十六進位 `string` 標記法 `Color` 。
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double)) 傳回 `Color` ，以提供的值取代色調。
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double)) 傳回 `Color` ，將亮度取代為提供的值。
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double)) 傳回 `Color` ，以提供的值取代飽和度。

## <a name="implicit-conversions"></a>隱含的轉換

您 [`Xamarin.Forms.Color`](xref:Xamarin.Forms.Color) 可以執行和類型之間的隱含轉換 `System.Drawing.Color` ：

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="examples"></a>範例

在 XAML 中，色彩通常會使用其命名值或其十六進位標記法來參考：

```xaml
<Label Text="Sea color"
       TextColor="Aqua" />
<Label Text="RGB"
       TextColor="#00FF00" />
<Label Text="Alpha plus RGB"
       TextColor="#CC00FF00" />
<Label Text="Tiny RGB"
       TextColor="#0F0" />
<Label Text="Tiny Alpha plus RGB"
       TextColor="#C0F0" />
```

> [!NOTE]
> 使用 XAML 編譯時，色彩名稱不區分大小寫，因此可以用小寫寫入。 如需 XAML 編譯的詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。

在 c # 中，通常會使用其命名值或其靜態方法來參考色彩：

```csharp
Label red    = new Label { Text = "Red",    TextColor = Color.Red };
Label orange = new Label { Text = "Orange", TextColor = Color.FromHex("FF6A00") };
Label yellow = new Label { Text = "Yellow", TextColor = Color.FromHsla(0.167, 1.0, 0.5, 1.0) };
Label green  = new Label { Text = "Green",  TextColor = Color.FromRgb (38, 127, 0) };
Label blue   = new Label { Text = "Blue",   TextColor = Color.FromRgba(0, 38, 255, 255) };
Label indigo = new Label { Text = "Indigo", TextColor = Color.FromRgb (0, 72, 255) };
Label violet = new Label { Text = "Violet", TextColor = Color.FromHsla(0.82, 1, 0.25, 1) };
```

下列範例會使用 `OnPlatform` 標記延伸來選擇性地設定的色彩 `ActivityIndicator` ：

```xaml
<ActivityIndicator Color="{OnPlatform iOS=Black, Default=Default}"
                   IsRunning="True" />
```

對等的 C# 程式碼為：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="related-links"></a>相關連結

- [ColorsSample](/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [可系結選擇器 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
