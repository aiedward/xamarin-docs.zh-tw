---
title: Xamarin.Forms圖形：線條
description: Xamarin.FormsLine 類別可以用來繪製線條。
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a69c505fe83618f06bafe6a49b8b5ce84aef096b
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101295"
---
# <a name="xamarinforms-shapes-line"></a>Xamarin.Forms圖形：線條

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

`Line`類別衍生自 `Shape` 類別，可以用來繪製線條。 如需 `Line` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Line` 會定義下列屬性：

- `X1`，屬於 double 類型，表示線條開始點的 x 座標。 這個屬性的預設值為0.0。
- `X2`（屬於 double 類型）表示線條結束點的 x 座標。 這個屬性的預設值為0.0。
- `Y1`，屬於 double 類型，表示線條開始點的 y 座標。 這個屬性的預設值為0.0。
- `Y2`，屬於 double 類型，表示線條結束點的 y 座標。 這個屬性的預設值為0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

## <a name="create-a-line"></a>建立線條

下列 XAML 範例顯示如何繪製線條：

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="DarkBlue"
      StrokeThickness="4"
      HeightRequest="120"
      WidthRequest="120" />
```

> [!NOTE]
> 設定的 `Fill` 屬性不會 `Line` 有任何作用，因為行沒有內部。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.Forms形狀](index.md)
