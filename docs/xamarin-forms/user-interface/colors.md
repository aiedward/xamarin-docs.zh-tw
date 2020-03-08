---
title: Xamarin 中的色彩
description: Xamarin 提供彈性的跨平臺色彩類別。 本文說明 Color 類別所提供的功能，以及如何使用它。
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 2a17b037803d1ca6e54000ea7ba3f05c8ce6034f
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912781"
---
# <a name="colors-in-xamarinforms"></a>Xamarin 中的色彩

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_Xamarin 提供彈性的跨平臺色彩類別。_

本文介紹可在 Xamarin 中使用[`Color`](xref:Xamarin.Forms.Color)類別的各種方式。

[`Color`](xref:Xamarin.Forms.Color)類別提供數種方法來建立色彩實例：

- **命名色彩**-一種常見的命名色彩集合，包括 `Red`、`Green`和 `Blue`。
- **FromHex** -類似于 HTML 中使用之語法的字串值，例如 "00FF00"。 您可以選擇性地將 Alpha 指定為第一對字元（"CC00FF00"）。
- **FromHsla** -色調、飽和度和亮度 `double` 值，具有選擇性的 Alpha 值（0.0-1.0）。
- **FromRgb** -紅色、綠色和藍色 `int` 值（0-255）。
- **FromRgba** -紅色、綠色、藍色和 Alpha `int` 值（0-255）。
- **FromUint** -設定代表**argb**的單一 `double` 值。

以下是一些範例色彩，並使用不同的允許語法變化，指派給某些標籤的 `BackgroundColor`：

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

這些色彩會顯示在以下的每個平臺上。 請注意，最終的色彩 `Accent`-是 iOS 和 Android 的藍色 ish 色彩;這個值是由 Xamarin 所定義。

 [![色彩示範](colors-images/colors-sml.png "色彩示範")](colors-images/colors.png#lightbox "色彩示範")

## <a name="colordefault"></a>Color。 Default

使用 [`Default`] 將色彩值設定（或重新設定）回平臺預設（瞭解這代表每個屬性在每個平臺上的不同基礎色彩）。

開發人員可以使用這個值來設定 `Color` 屬性，但**不**應查詢此實例的 RGB 值（全都設定為-1）。

## <a name="colortransparent"></a>色彩。透明

將色彩設定為 [清除]。

## <a name="coloraccent"></a>Color. 輔色

在 iOS 和 Android 上，這個實例會設定為在預設背景可見，但與預設文字色彩不同的對比色彩。

## <a name="additional-methods"></a>其他方法

[`Color`](xref:Xamarin.Forms.Color)實例包含下列其他方法：

- **AddLuminosity** -藉由以提供的差異修改亮度來傳回 `Color`。
- **MultiplyAlpha** -藉由修改 Alpha 來傳回 `Color`，並將其乘以提供的 Alpha 值。
- **ToHex** -傳回 `Color`的十六進位 `string` 標記法。
- **WithHue** -傳回 `Color`，以提供的值取代色調。
- **WithLuminosity** -傳回 `Color`，以提供的值取代亮度。
- **WithSaturation** -傳回 `Color`，以提供的值取代飽和度。

## <a name="implicit-conversions"></a>隱含的轉換

可以執行 `Xamarin.Forms.Color` 與 `System.Drawing.Color` 類型之間的隱含轉換：

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device. RuntimePlatform

此程式碼片段會使用 `Device.RuntimePlatform` 屬性，選擇性地設定 `ActivityIndicator`的色彩：

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>從 XAML 使用

您也可以使用定義的色彩名稱或這裡所示的十六進位標記法，在 XAML 中參考色彩：

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> 使用 XAML 編譯時，色彩名稱不區分大小寫，因此可以用小寫來撰寫。 如需 XAML 編譯的詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。

## <a name="related-links"></a>相關連結

- [ColorsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [可系結的選擇器（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
