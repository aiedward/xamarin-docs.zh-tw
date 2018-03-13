---
title: "色彩"
description: "Xamarin.Forms 提供彈性的跨平台色彩類別。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: 9c40d99d80766c5d32a76e424e902bd1b7d141ba
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="colors"></a>色彩

_Xamarin.Forms 提供彈性的跨平台色彩類別。_

本文介紹各種`Color`類別可以用在 Xamarin.Forms。

`Color`類別會提供多種方法來建置色彩執行個體

-  **名為色彩**-的通用命名的色彩，包括集合`Red`， `Green`，和`Blue`。
-  **FromHex** -字串值，在 HTML 中，例如"00FF00 」 使用的語法類似。 Alpha 可以選擇性地指定為第一對的字元 ("CC00FF00")。
-  **FromHsla** -色調、 飽和度和亮度`double`值，並選擇性的 alpha 值 (0.0-1.0)。
-  **FromRgb** -紅色、 綠色和藍色`int`值 (0-255)。
-  **FromRgba** -紅色、 綠色、 藍色及 alpha`int`值 (0-255)。
-  **FromUint** -設定單一`double`值代表**argb**。

以下是一些範例色彩，指派給`BackgroundColor`的使用不同的變化，允許語法的一些標籤：

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

這些色彩會顯示下列每個平台上。 請注意的完稿色彩- `Accent` -為 iOS 和 Android; blue-ish 色彩這個值由 Xamarin.Forms 所定義。 在 Windows Phone 上`Accent`顯示為紅色*因為這是該裝置的使用者選取的輔色*; 此值的變更取決於使用者的喜好設定。

 [![色彩示範](colors-images/colors-sml.png "色彩示範")](colors-images/colors.png#lightbox "色彩示範")

## <a name="colordefault"></a>Color.Default

使用`Default`來設定 （或重新設定） 色彩值還原為平台預設值 （了解這代表每一個屬性的每個平台上的不同基礎色彩）。

開發人員可以使用此值來設定`Color`屬性但應該**不**查詢它的元件 RGB 值 （這些所有設定為-1） 的這個執行個體。

## <a name="colortransparent"></a>Color.Transparent

設定要清除的色彩。

## <a name="coloraccent"></a>Color.Accent

在 Windows Phone，這是由使用者選擇的互補色。 好 Windows Phone 應用程式使用這個做為其設定樣式的一部分提供原生的外觀及操作。

IOS 和 Android 上這個執行個體設定為對比的色彩，會顯示預設的背景，但不是相同的預設文字色彩。

## <a name="additional-methods"></a>其他方法

`Color` 執行個體包含額外的方法可以用來建立新的色彩：

-  **AddLuminosity** -傳回新的色彩亮度修改所提供的差異。
-  **WithHue** -傳回新的色彩，以提供的值取代色調。
-  **WithLuminosity** -傳回新的色彩，以提供的值取代亮度。
-  **WithSaturation** -傳回新的色彩、 飽和度取代成所提供的值。
-  **MultiplyAlpha** -藉由修改 alpha，乘以所提供的 alpha 值會傳回新的色彩。

## <a name="deviceruntimeplatform"></a>Device.RuntimePlatform

此程式碼片段會使用`Device.RuntimePlatform`屬性選擇性地設定色彩`ActivityIndicator`:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>使用從 XAML

色彩可以輕鬆地參考在 XAML 中使用的已定義的色彩名稱或十六進位表示法，如下所示：

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

## <a name="summary"></a>總結

Xamarin.Forms`Color`類別用來建立參考感知平台的色彩。 它可以用於共用程式碼和 XAML。


## <a name="related-links"></a>相關連結

- [ColorsSample](https://developer.xamarin.com/samples/WorkingWithColors)
- [可繫結選擇器 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
