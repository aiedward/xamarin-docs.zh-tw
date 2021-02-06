---
title: Xamarin.Forms 圖形：線條
description: Xamarin.Forms線條類別可以用來繪製線條。
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/05/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 962800c5deb546ddf6a74d1f52ffaf60734ae463
ms.sourcegitcommit: 06701714021545eb5e932847829b876082194ffc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585841"
---
# <a name="xamarinforms-shapes-line"></a>Xamarin.Forms 圖形：線條

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Line`類別衍生自 `Shape` 類別，而且可以用來繪製線條。 如需 `Line` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Line` 會定義下列屬性：

- `X1`，屬於 double 類型，表示線條起點的 x 座標。 這個屬性的預設值是0.0。
- `Y1`，屬於 double 類型，表示線條起點的 y 座標。 這個屬性的預設值是0.0。
- `X2`，屬於 double 類型，表示線條之結束點的 x 座標。 這個屬性的預設值是0.0。
- `Y2`，屬於 double 類型，表示線條之結束點的 y 座標。 這個屬性的預設值是0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

如需控制如何繪製行尾的詳細資訊，請參閱 [控制行結尾](index.md#control-line-ends)。

## <a name="create-a-line"></a>建立線條

若要繪製線條，請建立 `Line` 物件，並將其 `X1` 和 `Y1` 屬性設定為其起點，並將其和屬性設定為 `X2` 它的 `Y` 結束點。 此外，將其 `Stroke` 屬性設定為 [`Brush`](xref:Xamarin.Forms.Brush) 衍生的物件，因為不會隱藏沒有筆劃的行。 如需物件的詳細資訊 `Brush` ，請參閱[ Xamarin.Forms 筆刷](~/xamarin-forms/user-interface/brushes/index.md)。

> [!NOTE]
> 設定的 `Fill` 屬性 `Line` 沒有任何作用，因為一行沒有內部。

下列 XAML 範例顯示如何繪製線條：

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="Red" />
```

在此範例中，會將紅色對角線從 (40，0) 繪製至 (0120) ：

![對角線](line-images/line.png "線條")

由於 `X1` 、、 `Y1` `X2` 和屬性的 `Y2` 預設值為0，因此可以使用最基本的語法來繪製一些行：

```xaml
<Line Stroke="Red"
      X2="200" />
```

在此範例中，會定義200裝置獨立單位長度的水平線條。 由於其他屬性預設為0，因此會從 (0、0) 到 (200、0) 繪製線條。

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

在此範例中，會將深藍色虛線對角線從 (40，0) 繪製至 (0120) ：

![虛線](line-images/dashed-line.png "虛線")

如需繪製虛線的詳細資訊，請參閱 [繪製虛線圖形](index.md#draw-dashed-shapes)。

## <a name="related-links"></a>相關連結

- [ShapeDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形狀](index.md)
- [Xamarin.Forms 刷](~/xamarin-forms/user-interface/brushes/index.md)
