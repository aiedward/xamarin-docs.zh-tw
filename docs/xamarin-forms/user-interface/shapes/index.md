---
title: Xamarin.Forms形狀
description: Xamarin.Forms圖形是可讓您在螢幕上繪製圖形的檢視類型。
ms.prod: xamarin
ms.assetid: 4E749FE8-852C-46DA-BB1E-652936106357
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9c7c552abe724dca6b06265b73346a399d35c3cb
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101390"
---
# <a name="xamarinforms-shapes"></a>Xamarin.Forms形狀

![](~/media/shared/preview.png "This API is currently pre-release")

`Shape`是的一種類型 [`View`](xref:Xamarin.Forms.View) ，可讓您在螢幕上繪製圖形。 `Shape`物件可以在版面配置類別和大部分的控制項內使用，因為 `Shape` 類別衍生自 `View` 類別。

Xamarin.Forms圖形適用于 `Xamarin.Forms.Shapes` iOS、Android、macOS、通用 Windows 平臺（UWP）和 Windows Presentation Foundation （WPF）的命名空間。

> [!IMPORTANT]
> Xamarin.Forms圖形目前為實驗性，而且只能透過設定旗標來使用 `Shapes_Experimental` 。 如需詳細資訊，請參閱[實驗性旗標](~/xamarin-forms/internals/experimental-flags.md)。

`Shape` 會定義下列屬性：

- `Aspect`，屬於類型 `Stretch` ，描述圖形如何填滿其已配置的空間。 此屬性的預設值為 `Stretch.None`。
- `Fill`，屬於類型 `Color` ，表示用來繪製圖形內部的色彩。
- `Stroke`，屬於類型 `Color` ，表示用來繪製圖形外框的色彩。
- `StrokeDashArray`，屬於類型 `DoubleCollection` ，表示值的集合，表示 `double` 用來勾勒出圖形的虛線和間距模式。
- `StrokeDashOffset`，屬於類型 `double` ，指定虛線模式內虛線開始的距離。 這個屬性的預設值為0.0。
- `StrokeLineCap`，屬於類型 `PenLineCap` ，描述行或線段開頭和結尾的圖形。 此屬性的預設值為 `PenLineCap.Flat`。
- `StrokeLineJoin`，屬於類型 `PenLineJoin` ，可指定用於圖形頂點的聯結類型。 此屬性的預設值為 `PenLineJoin.Miter`。
- `StrokeThickness`，屬於類型 `double` ，表示圖形外框的寬度。 這個屬性的預設值為1.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

Xamarin.Forms定義從類別衍生的一些物件 `Shape` 。 其中包括 `Ellipse`、`Line`、`Path`、`Polygon`、`Polyline` 和 `Rectangle`。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
