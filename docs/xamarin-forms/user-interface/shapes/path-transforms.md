---
title: Xamarin.Forms 圖形：路徑轉換
description: Xamarin.Forms轉換會定義如何將路徑物件從一個座標空間轉換到另一個座標空間。
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 37ee7fe2278176024dc1662b87b494e84344b98c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558956"
---
# <a name="no-locxamarinforms-shapes-path-transforms"></a>Xamarin.Forms 圖形：路徑轉換

![發行前 API](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Transform`定義如何將 `Path` 物件從一個座標空間轉換到另一個座標空間。 將轉換套用至 `Path` 物件時，它會變更在 UI 中呈現物件的方式。

轉換可以分類成四個一般分類：旋轉、縮放、扭曲和轉譯。 Xamarin.Forms 定義每個轉換分類的類別：

- `RotateTransform`，這會 `Path` 依指定的來旋轉 `Angle` 。
- `ScaleTransform`，會 `Path` 依指定 `ScaleX` 和數量調整物件 `ScaleY` 。
- `SkewTransform`，會 `Path` 依指定和數量來扭曲物件 `AngleX` `AngleY` 。
- `TranslateTransform`，會 `Path` 依指定 `X` 和數量移動物件 `Y` 。

Xamarin.Forms 也提供下列類別來建立更複雜的轉換：

- `TransformGroup`，表示由多個轉換物件組成的複合轉換。
- `CompositeTransform`，會將多個轉換作業套用至 `Path` 物件。
- `MatrixTransform`，它會建立其他轉換類別未提供的自訂轉換。

所有這些類別都是衍生自 `Transform` 類別，該類別會定義 `Value` 類型的屬性 `Matrix` ，表示目前的轉換為 `Matrix` 物件。 這個屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，也可以設定樣式。 如需結構的詳細資訊 `Matrix` ，請參閱 [轉換矩陣](#transform-matrix)。

若要將轉換套用至 `Path` ，您必須建立轉換類別，並將它設定為屬性的值 `Path.RenderTransform` 。

## <a name="rotation-transform"></a>旋轉轉換

旋轉轉換會 `Path` 針對 2d x-y 座標系統中的指定點，順時針旋轉物件。

`RotateTransform`衍生自類別的類別會 `Transform` 定義下列屬性：

- `Angle`型別為的， `double` 表示順時針旋轉的角度（以度為單位）。 這個屬性的預設值是0.0。
- `CenterX`型別為的， `double` 代表旋轉中心點的 x 座標。 這個屬性的預設值是0.0。
- `CenterY`型別為的， `double` 代表旋轉中心點的 y 座標。 這個屬性的預設值是0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

`CenterX`和 `CenterY` 屬性 `Path` 會指定旋轉物件的點。 這個中心點會以轉換之物件的座標空間來表示。 根據預設，旋轉會套用至 (0，0) ，也就是物件的左上角 `Path` 。

下列範例顯示如何旋轉 `Path` 物件：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <RotateTransform CenterX="0"
                         CenterY="0"
                         Angle="45" />
    </Path.RenderTransform>
</Path>
```

在此範例中， `Path` 物件的左上角會旋轉45度。

## <a name="scale-transform"></a>調整轉換

調整規模轉換會調整 `Path` 2d x y 座標系統中的物件。

`ScaleTransform`衍生自類別的類別會 `Transform` 定義下列屬性：

- `ScaleX`， `double` 代表 X 軸縮放比例的型別。 這個屬性的預設值是1.0。
- `ScaleY`， `double` 代表 y 軸縮放比例的型別。 這個屬性的預設值是1.0。
- `CenterX`，類型為 `double` ，表示此轉換中心點的 x 座標。 這個屬性的預設值是0.0。
- `CenterY`，類型為 `double` ，表示此轉換中心點的 y 座標。 這個屬性的預設值是0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

和的值 `ScaleX` 對 `ScaleY` 所產生的調整有很大的影響：

- 介於0和1之間的值會減少縮放物件的寬度和高度。
- 大於1的值會增加縮放物件的寬度和高度。
- 值為1時，表示該物件不會進行縮放。
- 負數值會以水準和垂直方式翻轉縮放物件。
- 0和-1 之間的值會翻轉刻度物件，並減少其寬度和高度。
- 小於-1 的值會翻轉物件，並增加其寬度和高度。
- -1 的值會翻轉縮放的物件，但不會變更其水準或垂直大小。

`CenterX`和 `CenterY` 屬性 `Path` 會指定調整物件的位置。 這個中心點會以轉換之物件的座標空間來表示。 根據預設，縮放比例會套用至 (0、0) ，也就是物件的左上角 `Path` 。 這有移動 `Path` 物件並使其顯示較大的效果，因為當您套用轉換時，會變更物件所在的座標空間 `Path` 。

下列範例顯示如何調整 `Path` 物件：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <ScaleTransform CenterX="0"
                        CenterY="0"
                        ScaleX="1.5"
                        ScaleY="1.5" />
    </Path.RenderTransform>
</Path>
```

在此範例中， `Path` 物件會調整為大小的1.5 倍。

## <a name="skew-transform"></a>扭曲轉換

扭曲轉換 `Path` 會扭曲 2d x y 座標系統中的物件，而且適用于在2d 物件中建立3d 深度的假像。

`SkewTransform`衍生自類別的類別會 `Transform` 定義下列屬性：

- `AngleX`， `double` 代表 X 軸扭曲角度（以從 y 軸逆時針算起的度為單位）。 這個屬性的預設值是0.0。
- `AngleY`， `double` 代表 y 軸扭曲角度（以從 X 軸逆時針算起的度為單位）。 這個屬性的預設值是0.0。
- `CenterX`，類型為 `double` ，表示轉換中心的 x 座標。 這個屬性的預設值是0.0。
- `CenterY`，類型為 `double` ，表示轉換中心的 y 座標。 這個屬性的預設值是0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

若要預測扭曲轉換的效果，請考慮 `AngleX` 相對於原始座標系統的誤差 X 軸值。 因此，若為 `AngleX` 30，y 軸會將30度旋轉到原點，並將 x 中的值從該原點傾斜30度。 同樣地， `AngleY` 30 會將物件的 y 值 `Path` 從原點傾斜30度。

> [!NOTE]
> 若要 `Path` 就地扭曲物件，請將 `CenterX` 和屬性設定 `CenterY` 為物件的中心點。

下列範例顯示如何扭曲 `Path` 物件：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <SkewTransform CenterX="0"
                       CenterY="0"
                       AngleX="45"
                       AngleY="0" />
    </Path.RenderTransform>
</Path>
```

在此範例中，會將45度的水準誤差套用至 `Path` 物件，從中央點 (0，0) 。

## <a name="translate-transform"></a>轉譯轉換

「轉譯」轉換會移動 2D x-y 座標系統中的物件。

`TranslateTransform`衍生自類別的類別會 `Transform` 定義下列屬性：

- `X`，類型為 `double` ，表示沿著 X 軸移動的距離。 這個屬性的預設值是0.0。
- `Y`，類型為 `double` ，表示沿著 y 軸移動的距離。 這個屬性的預設值是0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

負 `X` 數值會將物件向左移動，而正值則會將物件向右移動。 負 `Y` 數值會向上移動物件，而正值則會將物件向下移動。

下列範例顯示如何轉譯 `Path` 物件：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TranslateTransform X="50"
                            Y="50" />
    </Path.RenderTransform>
</Path>
```

在此範例中， `Path` 物件會將50與裝置無關的單位移動到右邊，並將50裝置獨立的單位向下移動。

## <a name="multiple-transforms"></a>多個轉換

Xamarin.Forms 有兩個類別，可支援將多個轉換套用至 `Path` 物件。 這些是 `TransformGroup` 、和 `CompositeTransform` 。 會 `TransformGroup` 以任何所需的循序執行轉換，而會 `CompositeTransform` 以特定循序執行轉換。

### <a name="transform-groups"></a>轉換群組

轉換群組代表由多個物件組成的複合轉換 `Transform` 。

`TransformGroup`衍生自類別的類別會 `Transform` 定義 `Children` 類型的屬性， `TransformCollection` 代表物件的集合 `Transform` 。 這個屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，也可以設定樣式。

轉換順序對於使用類別的複合轉換而言很重要 `TransformGroup` 。 例如，如果您第一次旋轉，然後進行調整，然後轉譯，則會得到不同的結果，而不是您第一次轉譯、接著旋轉然後調整規模。 其中一個原因是很重要的是，旋轉和縮放之類的轉換都是針對座標系統的原點來執行。 調整在來源置中的物件時，會產生不同的結果，以調整已移離來源的物件。 同樣地，旋轉位於原點中央的物件會產生不同的結果，而不是旋轉離開來源的物件。

下列範例顯示如何使用類別來執行複合轉換 `TransformGroup` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TransformGroup>
            <ScaleTransform ScaleX="1.5"
                            ScaleY="1.5" />
            <RotateTransform Angle="45" />
        </TransformGroup>
    </Path.RenderTransform>
</Path>
```

在此範例中， `Path` 物件會調整為1.5 倍的大小，然後以45度旋轉。

## <a name="composite-transforms"></a>複合轉換

複合轉換會將多個轉換套用至物件。

`CompositeTransform`衍生自類別的類別會 `Transform` 定義下列屬性：

- `CenterX`，類型為 `double` ，表示此轉換中心點的 x 座標。 這個屬性的預設值是0.0。
- `CenterY`，類型為 `double` ，表示此轉換中心點的 y 座標。 這個屬性的預設值是0.0。
- `ScaleX`， `double` 代表 X 軸縮放比例的型別。 這個屬性的預設值是1.0。
- `ScaleY`， `double` 代表 y 軸縮放比例的型別。 這個屬性的預設值是1.0。
- `SkewX`， `double` 代表 X 軸扭曲角度（以從 y 軸逆時針算起的度為單位）。 這個屬性的預設值是0.0。
- `SkewY`， `double` 代表 y 軸扭曲角度（以從 X 軸逆時針算起的度為單位）。 這個屬性的預設值是0.0。
- `Rotation`型別為的， `double` 表示順時針旋轉的角度（以度為單位）。 這個屬性的預設值是0.0。
- `TranslateX`，類型為 `double` ，表示沿著 X 軸移動的距離。 這個屬性的預設值是0.0。
- `TranslateY`，類型為 `double` ，表示沿著 y 軸移動的距離。 這個屬性的預設值是0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

會依下列 `CompositeTransform` 順序套用轉換：

1. 調整 (`ScaleX` 和 `ScaleY`) 。
1. 扭曲 (`SkewX` 和 `SkewY`) 。
1. 輪替 (`Rotation`) 。
1. 轉譯 (`TranslateX` 、 `TranslateY`) 。

如果您想要以不同的順序將多個轉換套用至物件，您應該建立， `TransformGroup` 並依您想要的順序插入轉換。

> [!IMPORTANT]
> 會 `CompositeTransform` 針對所有轉換使用相同的中心點 `CenterX` 和 `CenterY` 。 如果您想要針對每個轉換指定不同的中心點 `TransformGroup` ，請使用

下列範例顯示如何使用類別來執行複合轉換 `CompositeTransform` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <CompositeTransform ScaleX="1.5"
                            ScaleY="1.5"
                            Rotation="45"
                            TranslateX="50"
                            TranslateY="50" />
    </Path.RenderTransform>
</Path>
```

在此範例中， `Path` 物件會調整為1.5 倍的大小，然後以45度旋轉，然後以50裝置獨立單位進行轉譯。

## <a name="transform-matrix"></a>轉換矩陣

轉換可以用3x3 仿射轉換矩陣來描述，在2D 空間中執行轉換。 這個3x3 矩陣是以結構表示 `Matrix` ，這是三個數據列的集合，以及三個 `double` 值的資料行。

`Matrix`結構會定義下列屬性：

- `Determinant`，類型為 `double` ，可取得矩陣的行列式。
- `HasInverse`， `bool` 表示矩陣是否可反轉的型別。
- `Identity`型別為的， `Matrix` 可取得識別矩陣。
- `HasIdentity`， `bool` 表示矩陣是否為識別矩陣的型別。
- `M11`，類型為 `double` ，表示矩陣的第一個資料列和第一個資料行的值。
- `M12`， `double` 代表矩陣的第一個資料列和第二個數據行的值。
- `M21`，類型為 `double` ，表示矩陣的第二個數據列和第一個資料行的值。
- `M22`， `double` 代表矩陣的第二個數據列和第二個數據行的值。
- `OffsetX`， `double` 代表矩陣的第三個數據列和第一個資料行的值。
- `OffsetY`， `double` 代表矩陣的第三個數據列和第二個數據行之值的型別。

`OffsetX`和 `OffsetY` 屬性會命名為，因為它們會分別指定沿著 X 軸和 y 軸轉譯座標空間的數量。

此外，此 `Matrix` 結構會公開一系列可用來操作矩陣值的方法，包括 `Append` 、 `Invert` 、 `Multiply` `Prepend` 和其他更多。

下表顯示矩陣的結構 Xamarin.Forms ：

:::row:::
    :::column:::
        M11
    :::column-end:::
    :::column:::
        M12
    :::column-end:::
    :::column:::
        0.0
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        M21
    :::column-end:::
    :::column:::
        M22
    :::column-end:::
    :::column:::
        0.0
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        OffsetX
    :::column-end:::
    :::column:::
        OffsetY
    :::column-end:::
    :::column:::
        1.0
    :::column-end:::
:::row-end:::

> [!NOTE]
> 仿射轉換矩陣的最後一個資料行等於 (0，0，1) ，因此只需要指定前兩個數據行中的成員。

藉由操作矩陣值，您可以旋轉、縮放、扭曲和轉譯 `Path` 物件。 例如，如果您將值變更 `OffsetX` 為100，則可以使用它 `Path` 沿著 X 軸移動物件100裝置獨立單位。 如果您將 `M22` 值變更為3，您可以使用它來將 `Path` 物件延展到其目前高度的三倍。 如果您變更這兩個值，則會 `Path` 沿著 X 軸移動物件100與裝置無關的單位，並將其高度延展為3倍。 此外，您可以將仿射轉換矩陣相乘來形成任何數目的線性轉換，例如旋轉和扭曲，然後再進行轉譯。

## <a name="custom-transforms"></a>自訂轉換

`MatrixTransform`衍生自類別的類別會 `Transform` 定義 `Matrix` 類型的屬性， `Matrix` 代表定義轉換的矩陣。 這個屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，也可以設定樣式。

您可以使用 `TranslateTransform` 、、或物件描述的任何轉換， `ScaleTransform` `RotateTransform` `SkewTransform` 都可以同樣地描述 `MatrixTransform` 。 不過，在中，、、 `TranslateTransform` `ScaleTransform` `RotateTransform` 和 `SkewTransform` 類別的概念比設定向量元件更容易 `Matrix` 。 因此， `MatrixTransform` 類別通常用來建立 `RotateTransform` 、 `ScaleTransform` 、 `SkewTransform` 或類別未提供的自訂轉換 `TranslateTransform` 。

下列範例顯示如何使用來轉換 `Path` 物件 `MatrixTransform` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform>
            <MatrixTransform.Matrix>
                <!-- M11 stretches, M12 skews -->
                <Matrix OffsetX="10"
                        OffsetY="100"
                        M11="1.5"
                        M12="1" />
            </MatrixTransform.Matrix>
        </MatrixTransform>
    </Path.RenderTransform>
</Path>
```

在此範例中， `Path` 物件會在 X 和 Y 維度中伸展、扭曲和位移。

或者，您可以使用內建的型別轉換子，以簡化的形式撰寫這項功能 Xamarin.Forms ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform Matrix="1.5,1,0,1,10,100" />
    </Path.RenderTransform>
</Path>
```

在此範例中， `Matrix` 屬性會指定為以逗號分隔的字串，其中包含六個成員： `M11` 、 `M12` 、 `M21` 、 `M22` 、 `OffsetX` 、 `OffsetY` 。 雖然在此範例中，成員會以逗號分隔，但它們也可以用一或多個空格分隔。

此外，您可以藉由指定與屬性值相同的六個成員，更進一步簡化上述範例 `RenderTransform` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      RenderTransform="1.5 1 0 1 10 100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z" />
```

## <a name="related-links"></a>相關連結

- [ShapeDemos (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms 形狀](index.md)