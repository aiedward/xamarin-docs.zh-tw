---
title: 在 Xamarin.Forms 中的色彩
description: Xamarin.Forms 提供彈性的跨平台色彩類別。 這篇文章說明色彩類別，以及如何使用它所提供的功能。
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 2a17b037803d1ca6e54000ea7ba3f05c8ce6034f
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888464"
---
# <a name="colors-in-xamarinforms"></a>在 Xamarin.Forms 中的色彩

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_Xamarin.Forms 提供彈性的跨平台色彩類別。_

本文介紹[`Color`](xref:Xamarin.Forms.Color)類別可以在 Xamarin 中使用的各種方式。

[`Color`](xref:Xamarin.Forms.Color)類別提供數種方法來建立色彩實例:

- **命名色彩**-集合的一般具名色彩，包括`Red`， `Green`，和`Blue`。
- **FromHex** -類似于 HTML 中使用之語法的字串值, 例如 "00FF00"。 您可以選擇性地將 Alpha 指定為第一對字元 ("CC00FF00")。
- **FromHsla** -色調、 飽和度和亮度`double`具選擇性的 alpha 值 (介於 0.0-1.0) 的值。
- **FromRgb** -紅色、 綠色和藍色`int`值 (0-255)。
- **FromRgba** -紅色、 綠色、 藍色和 alpha`int`值 (0-255)。
- **FromUint** -將單一`double`值，表示**argb**。

以下是一些範例的色彩，指派給`BackgroundColor`的某些標籤，使用不同的允許的語法變化：

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

這些色彩會顯示下列每個平台上。 請注意完稿色彩- `Accent` -blue-ish 的色彩，適用於 iOS 和 Android; 此值 Xamarin.Forms 所定義。

 [![色彩示範](colors-images/colors-sml.png "色彩示範")](colors-images/colors.png#lightbox "色彩示範")

## <a name="colordefault"></a>Color.Default

使用`Default`來設定 （或重新設定） 色彩值還原為平台預設值 （了解這代表每一個屬性的每個平台上不同的基礎色彩）。

開發人員可以使用此值來設定`Color`屬性但應該**不**查詢 （所有設定為-1） 其元件 RGB 值的這個執行個體。

## <a name="colortransparent"></a>Color.Transparent

設定要清除的色彩。

## <a name="coloraccent"></a>Color.Accent

IOS 和 Android 上這個執行個體設定為以對比的色彩，會顯示在預設的背景上，但不是相同的預設文字色彩。

## <a name="additional-methods"></a>其他方法

[`Color`](xref:Xamarin.Forms.Color)實例包含下列其他方法:

- **AddLuminosity** - `Color`藉由以提供的差異修改亮度來傳回。
- **MultiplyAlpha** - `Color`藉由修改 Alpha 來傳回, 並將其乘以提供的 Alpha 值。
- **ToHex** -傳回的十六進位`string`表示。 `Color`
- **WithHue** - `Color`傳回, 以提供的值取代色調。
- **WithLuminosity** - `Color`傳回, 以提供的值取代亮度。
- **WithSaturation** - `Color`傳回, 以提供的值取代飽和度。

## <a name="implicit-conversions"></a>隱含轉換

之間的隱含轉換`Xamarin.Forms.Color`和`System.Drawing.Color`可以執行類型：

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device.RuntimePlatform

此程式碼片段會使用`Device.RuntimePlatform`屬性來選擇性地設定的色彩`ActivityIndicator`:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>使用 XAML

您也可以使用定義的色彩名稱或這裡所示的十六進位標記法, 在 XAML 中參考色彩:

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> 在使用 XAML 編譯時，色彩名稱不區分大小寫，因此可以撰寫以小寫字母。 如需 XAML 編譯的詳細資訊，請參閱 [XAML 編譯](~/xamarin-forms/xaml/xamlc.md)。

## <a name="related-links"></a>相關連結

- [ColorsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [可繫結選擇器 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
