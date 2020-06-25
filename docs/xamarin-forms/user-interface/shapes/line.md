---
title: Xamarin.Forms圖形：線條
description: Xamarin.FormsLine 類別可以用來繪製線條。
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f97e6930f543f47383e2b8da09b36573bd3192d0
ms.sourcegitcommit: ef3d4a70e70927c4f231b763842c5355f1571d15
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243746"
---
# <a name="xamarinforms-shapes-line"></a>Xamarin.Forms圖形：線條

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Line`類別衍生自 `Shape` 類別，可以用來繪製線條。 如需 `Line` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Line` 會定義下列屬性：

- `X1`，屬於 double 類型，表示線條開始點的 x 座標。 這個屬性的預設值為0.0。
- `Y1`，屬於 double 類型，表示線條開始點的 y 座標。 這個屬性的預設值為0.0。
- `X2`（屬於 double 類型）表示線條結束點的 x 座標。 這個屬性的預設值為0.0。
- `Y2`，屬於 double 類型，表示線條結束點的 y 座標。 這個屬性的預設值為0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

如需控制如何繪製線條結束的詳細資訊，請參閱[控制行尾](index.md#control-line-ends)。

## <a name="create-a-line"></a>建立線條

若要繪製線條，請建立 `Line` 物件，並將其 `X1` 和 `Y1` 屬性設定為其起點，並將其 `X2` 和屬性設 `Y` 為其結束點。 此外，將其屬性設定為，因為不會 `Stroke` [`Color`](xref:Xamarin.Forms.Color) 隱藏沒有筆觸的線條。

> [!NOTE]
> 設定的 `Fill` 屬性不會 `Line` 有任何作用，因為行沒有內部。

下列 XAML 範例顯示如何繪製線條：

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="Red" />
```

在此範例中，會從（40，0）到（0120）繪製紅色對角線：

![線](line-images/line.png "線")

由於 `X1` 、 `Y1` 、 `X2` 和屬性的 `Y2` 預設值為0，因此您可以使用最少的語法來繪製一些行：

```xaml
<Line Stroke="Red"
      X2="200" />
```

在此範例中，定義了200裝置獨立單位長度的水平線。 由於其他屬性預設為0，因此會從（0，0）到（200，0）繪製線條。

下列 XAML 範例顯示如何繪製虛線：

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="DarkBlue"
      StrokeDashArray="1,1"
      StrokeDashOffset="6" />
```

在此範例中，深藍色虛線對角線會從（40，0）繪製到（0120）：

![虛線](line-images/dashed-line.png "虛線")

如需繪製虛線的詳細資訊，請參閱[繪製虛線形狀](index.md#draw-dashed-shapes)。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms形狀](index.md)
