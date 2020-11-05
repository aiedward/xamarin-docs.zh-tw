---
title: 色彩 Xamarin.Forms
description: Xamarin.Forms 提供彈性的跨平臺色彩類別。 本文說明 Color 類別所提供的功能，以及其使用方式。
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6018946f280afa3f02d8f81bfc64338e561950fe
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375287"
---
# <a name="colors-in-no-locxamarinforms"></a>色彩 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_Xamarin.Forms 提供彈性的跨平臺色彩類別。_

本文介紹 [`Color`](xref:Xamarin.Forms.Color) 可在中使用類別的各種方式 Xamarin.Forms 。

[`Color`](xref:Xamarin.Forms.Color)類別提供數種方法來建立 `Color` 實例：

- **命名色彩** -一般命名色彩的集合，包括 `Red` 、 `Green` 和 `Blue` 。
- `FromHex` -字串值類似于 HTML 中使用的語法，例如 "00FF00"。 您可以選擇性地指定 Alpha ( "CC00FF00" ) 的第一對字元。
- `FromHsla` -色調、飽和度和亮度 `double` 值，選擇性的 Alpha 值 (0.0-1.0) 。
- `FromHsv` -色調、飽和度和值 `int` 或 `double` 值。
- `FromHsva` -色調、飽和度和值 `int` 或 `double` 值。
- `FromRgb` -紅色、綠色和藍色的 `int` 值 (0-255) 。
- `FromRgba` -紅色、綠色、藍色和 Alpha  `int` 值 (0-255) 。
- `FromUint` -設定 `double` 代表 **argb** 的單一值。

以下是一些 `BackgroundColor` 使用不同的允許語法變化，指派給某些標籤之的範例色彩：

```csharp
var red    = new Label { Text = "Red",   BackgroundColor = Color.Red };
var orange = new Label { Text = "Orange",BackgroundColor = Color.FromHex("FF6A00") };
var yellow = new Label { Text = "Yellow",BackgroundColor = Color.FromHsla(0.167, 1.0, 0.5, 1.0) };
var green  = new Label { Text = "Green", BackgroundColor = Color.FromRgb (38, 127, 0) };
var blue   = new Label { Text = "Blue",  BackgroundColor = Color.FromRgba(0, 38, 255, 255) };
var indigo = new Label { Text = "Indigo",BackgroundColor = Color.FromRgb (0, 72, 255) };
var violet = new Label { Text = "Violet",BackgroundColor = Color.FromHsla(0.82, 1, 0.25, 1) };

var transparent = new Label { Text = "Transparent",BackgroundColor = Color.Transparent };
var @default = new Label    { Text = "Default",    BackgroundColor = Color.Default };
var accent = new Label      { Text = "Accent",     BackgroundColor = Color.Accent };
```

這些色彩會顯示在下列每個平臺上。 請注意，最後的色彩- `Accent` -是適用于 iOS 和 Android 的藍色低色彩，此值是由定義 Xamarin.Forms 。

 [![色彩示範](colors-images/colors-sml.png "色彩示範")](colors-images/colors.png#lightbox "色彩示範")

## <a name="colordefault"></a>Color。預設值

您 `Default` 可以使用來設定 (或重新設定) 將色彩值重新設定為平臺預設值 (瞭解這代表每個平臺上的不同基礎色彩) 。

開發人員可以使用此值來設定 `Color` 屬性，但 **不** 應該查詢此實例的元件 RGB 值 (它們全都設定為-1) 。

## <a name="colortransparent"></a>色彩。透明

將色彩設定為 [清除]。

## <a name="coloraccent"></a>色彩。輔色

在 iOS 和 Android 上，這個實例會設定為在預設背景上可見的對比色彩，但與預設的文字色彩不同。

## <a name="additional-methods"></a>其他方法

[`Color`](xref:Xamarin.Forms.Color) 實例包含下列其他方法：

- `AddLuminosity` -透過 `Color` 提供的 delta 修改亮度來傳回。
- `MultiplyAlpha` -藉 `Color` 由修改 Alpha 來傳回，然後將它乘以提供的 Alpha 值。
- `ToHex` -傳回的十六進位 `string` 標記法 `Color` 。
- `WithHue` -傳回 `Color` ，以提供的值取代色調。
- `WithLuminosity` -傳回 `Color` ，將亮度取代為提供的值。
- `WithSaturation` -傳回 `Color` ，以提供的值取代飽和度。

## <a name="implicit-conversions"></a>隱含的轉換

您 `Xamarin.Forms.Color` 可以執行和類型之間的隱含轉換 `System.Drawing.Color` ：

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>RuntimePlatform

此程式碼片段會使用 `Device.RuntimePlatform` 屬性來選擇性地設定的色彩 `ActivityIndicator` ：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="use-from-xaml"></a>從 XAML 使用

您也可以在 XAML 中使用所定義的色彩名稱或十六進位標記法來參考色彩：

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> 使用 XAML 編譯時，色彩名稱不區分大小寫，因此可以用小寫寫入。 如需 XAML 編譯的詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。

## <a name="related-links"></a>相關連結

- [ColorsSample](/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [可系結選擇器 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)