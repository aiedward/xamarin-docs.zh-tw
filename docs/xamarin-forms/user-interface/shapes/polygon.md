---
title: Xamarin.Forms圖形：多邊形
description: Xamarin.Forms多邊形類別可以用來繪製多邊形，這是一系列形成封閉圖形的連接線。
ms.prod: xamarin
ms.assetid: D6539F60-A5AC-46EF-86EB-E9F508EB1FA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2d67a6a4bbea6a4be27f0c0440d9c28ffd5a188f
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101303"
---
# <a name="xamarinforms-shapes-polygon"></a>Xamarin.Forms圖形：多邊形

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

`Polygon`類別衍生自 `Shape` 類別，可以用來繪製多邊形，這是形成封閉圖形的連接線系列。 如需 `Polygon` 類別繼承自類別之屬性的詳細資訊 `Shape` ，請參閱[ Xamarin.Forms 圖形](index.md)。

`Polygon` 會定義下列屬性：

- `Points`，屬於類型 `PointCollection` ，這是 `Point` 描述多邊形頂點的結構集合。
- `FillRule`，屬於類型 `FillRule` ，指定如何決定圖形的內部填滿。 此屬性的預設值為 `FillRule.EvenOdd`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

## <a name="create-a-polygon"></a>建立多邊形

下列 XAML 範例顯示如何繪製多邊形：

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         HeightRequest="100"
         WidthRequest="200" />
```

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.Forms形狀](index.md)
