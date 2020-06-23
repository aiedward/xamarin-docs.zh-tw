---
title: Xamarin.Forms圖形：路徑轉換
description: Xamarin.Forms轉換會定義如何將 Path 物件從一個座標空間轉換到另一個座標空間。
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8df87f0ee3be78749d70dfc535b5d4d50874f194
ms.sourcegitcommit: 7fc658bbdcb8130cd9d611e55e79a1830fc5d5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133018"
---
# <a name="xamarinforms-shapes-path-transforms"></a>Xamarin.Forms圖形：路徑轉換

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

會 `Transform` 定義如何將 `Path` 物件從一個座標空間轉換成另一個座標空間。 這個對應是由轉換所描述 `Matrix` ，這是三個數據列的集合，其中包含三個數據行的 `double` 值。

3x3 矩陣用於 2D x y 平面的轉換。 仿射轉換矩陣可以相乘以形成任何數目的線性轉換，例如旋轉和扭曲，然後再進行轉譯。 下表顯示矩陣的結構 Xamarin.Forms ：

| | | |
|---------|---------|-----|
| M11     | M12     | 0.0 |
| M21     | M22     | 0.0 |
| OffsetX | OffsetY | 1.0 |

藉由操作矩陣值，您可以旋轉、縮放、扭曲和轉譯 `Path` 物件。 例如，如果您將值變更 `OffsetX` 為100，就可以使用它， `Path` 沿著 X 軸移動物件100裝置獨立單位。 如果您將 `M22` 值變更為3，就可以使用它來將物件延展至 `Path` 其目前高度的三倍。 如果您變更這兩個值，您可以將 `Path` 物件100裝置獨立單位沿著 X 軸移動，並以3的因數延展其高度。

> [!NOTE]
> 仿射轉換矩陣的最後一個資料行等於（0，0，1），所以只需要指定前兩個數據行中的成員。 最後一個資料列中的成員（ `OffsetX` 和 `OffsetY` ）代表轉譯值。

雖然您可以直接使用 `Matrix` 結構來轉譯個別點，但 Xamarin.Forms 也提供下列類別，可讓您在 `Path` 不直接使用矩陣的情況下轉換物件：

- `RotateTransform`，它會 `Path` 依據指定的旋轉 `Angle` 。
- `ScaleTransform`，其 `Path` 依指定和數量來縮放物件 `ScaleX` `ScaleY` 。
- `SkewTransform`，其 `Path` 依指定和數量來扭曲物件 `AngleX` `AngleY` 。
- `TranslateTransform`，可 `Path` 依指定 `X` 和數量移動物件 `Y` 。

Xamarin.Forms也提供下列類別來建立更複雜的轉換：

- `TransformGroup`，表示 `Transform` 由其他物件組成的複合 `Transform` 。
- `CompositeTransform`，表示 `Transform` 由其他物件組成的複合 `Transform` 。
- `MatrixTransform`，它會建立其他類別未提供的自訂轉換 `Transform` 。

所有這些類別都是衍生自 `Transform` 類別，它會定義 `Value` 類型的屬性 `Matrix` 。 這個屬性會將目前的轉換表示為 `Matrix` 物件。

若要將轉換套用至 `Path` ，請建立轉換類別，並將它設定為屬性的值 `Path.RenderTransform` 。

## <a name="rotation-transform"></a>旋轉轉換

「旋轉」轉換會 `Path` 在 2d x y 座標系統中，以順時針方式旋轉物件的指定點。

`RotateTransform`衍生自類別的類別會 `Transform` 定義下列屬性：

- `Angle`，屬於類型 `double` ，表示順時針旋轉的角度（以度為單位）。 這個屬性的預設值為0.0。
- `CenterX`，屬於類型 `double` ，表示旋轉中心點的 x 座標。 這個屬性的預設值為0.0。
- `CenterY`，屬於類型 `double` ，表示旋轉中心點的 y 座標。 這個屬性的預設值為0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

`CenterX`和 `CenterY` 屬性會指定要旋轉物件的時間點 `Path` 。 這個中心點會以轉換之物件的座標空間來表示。 根據預設，旋轉會套用到（0，0），這是物件的左上角 `Path` 。

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

在此範例中， `Path` 物件會圍繞其左上角旋轉45度。

## <a name="scale-transform"></a>調整轉換

尺規轉換會調整 `Path` 2d x y 座標系統中的物件。

`ScaleTransform`衍生自類別的類別會 `Transform` 定義下列屬性：

- `ScaleX`，屬於類型 `double` ，表示 X 軸縮放比例。 這個屬性的預設值為1.0。
- `ScaleY`，屬於類型 `double` ，表示 y 軸縮放比例。 這個屬性的預設值為1.0。
- `CenterX`，屬於類型 `double` ，表示此轉換中心點的 x 座標。 這個屬性的預設值為0.0。
- `CenterY`，屬於類型 `double` ，表示此轉換中心點的 y 座標。 這個屬性的預設值為0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

和的值 `ScaleX` 對 `ScaleY` 所產生的調整有很大的影響：

- 介於0和1之間的值會減少縮放物件的寬度和高度。
- 大於1的值會增加縮放物件的寬度和高度。
- 值為1表示物件不會縮放。
- 負值會以水準和垂直方式翻轉尺規物件。
- 介於0和-1 之間的值會翻轉尺規物件，並減少其寬度和高度。
- 小於-1 的值會翻轉物件並增加其寬度和高度。
- -1 的值會翻轉縮放的物件，但不會變更其水準或垂直大小。

`CenterX`和 `CenterY` 屬性會指定縮放物件的時間點 `Path` 。 這個中心點會以轉換之物件的座標空間來表示。 根據預設，縮放比例會套用至（0，0），這是物件的左上角 `Path` 。 這會影響移動 `Path` 物件並使其變得更大，因為當您套用轉換時，您會變更物件所在的座標空間 `Path` 。

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
                        ScaleX="2"
                        ScaleY="2" />
    </Path.RenderTransform>
</Path>
```

在此範例中， `Path` 會將物件調整為大小的兩倍。

## <a name="skew-transform"></a>扭曲轉換

扭曲轉換 `Path` 會扭曲 2d x y 座標系統中的物件，並且適用于在2d 物件中建立3d 深度的幻影。

`SkewTransform`衍生自類別的類別會 `Transform` 定義下列屬性：

- `AngleX`，屬於類型 `double` ，表示 X 軸扭曲角度（以從 y 軸逆時針算起的度為測量單位）。 這個屬性的預設值為0.0。
- `AngleY`，屬於類型 `double` ，表示 y 軸扭曲角度（以從 X 軸逆時針算起的度為測量單位）。 這個屬性的預設值為0.0。
- `CenterX`，屬於類型 `double` ，表示轉換中心的 x 座標。 這個屬性的預設值為0.0。
- `CenterY`，屬於類型 `double` ，表示轉換中心的 y 座標。 這個屬性的預設值為0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

若要預測扭曲轉換的效果，請考慮將 `AngleX` 相對於原始座標系統的 X 軸值扭曲。 因此，若為 `AngleX` 30，y 軸會透過原點旋轉30度，並將 x 的值從該原點傾斜30度。 同樣地，若為30，則會將 `AngleY` 物件的 y 值 `Path` 從原點扭曲30度。

若要 `Path` 就地扭曲物件，請將 `CenterX` 和屬性設定 `CenterY` 為物件的中心點。

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

在此範例中，45度的水準誤差會 `Path` 從（0，0）的中心點套用至物件。

## <a name="translate-transform"></a>轉譯轉換

「轉譯」轉換會移動 2D x y 座標系統中的物件。

`TranslateTransform`衍生自類別的類別會 `Transform` 定義下列屬性：

- `X`，屬於類型 `double` ，表示沿著 X 軸移動的距離。 這個屬性的預設值為0.0。
- `Y`，屬於類型 `double` ，表示沿著 y 軸移動的距離。 這個屬性的預設值為0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

負值會 `X` 將物件向左移動，而正值會將物件向右移動。 負值會 `Y` 將物件往上移動，而正值會將物件向下移動。

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

在此範例中， `Path` 物件會將50裝置獨立單位移至右側，並將50裝置獨立單位向下移動。

## <a name="apply-multiple-transforms"></a>套用多個轉換

Xamarin.Forms有兩個類別，可支援將多個轉換套用至 `Path` 物件。 這些是 `TransformGroup` 、和 `CompositeTransform` 。 會 `TransformGroup` 以任何想要的循序執行轉換，而則會依 `CompositeTransform` 特定循序執行轉換。

### <a name="transform-groups"></a>轉換群組

轉換群組代表由多個物件組成的複合轉換 `Transform` 。

`TransformGroup`衍生自類別的類別會 `Transform` 定義下列屬性：

- `Children`，屬於類型 `TransformCollection` ，表示物件的集合 `Transform` 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

在使用類別的複合轉換中，轉換的順序很重要 `TransformGroup` 。 例如，如果您先旋轉，然後調整規模，然後轉譯，則會得到不同的結果，而不是您第一次轉譯，然後旋轉再調整。 其中一個原因是很重要的，就是會針對座標系統的原點執行旋轉和縮放等轉換。 調整以原點為中心的物件時，會產生不同的結果，以縮放已從原點移出的物件。 同樣地，旋轉原點中央的物件，會產生不同的結果，而不是旋轉離開來源的物件。

下列範例顯示如何使用類別執行複合轉換 `TransformGroup` ：

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
            <ScaleTransform ScaleY="2" />
            <RotateTransform Angle="45" />
        </TransformGroup>
    </Path.RenderTransform>
</Path>
```

在此範例中， `Path` 物件會調整為其大小的兩倍，並旋轉45度。

## <a name="composite-transforms"></a>複合轉換

複合轉換會將多個轉換套用至物件。

`CompositeTransform`衍生自類別的類別會 `Transform` 定義下列屬性：

- `CenterX`，屬於類型 `double` ，表示此轉換中心點的 x 座標。 這個屬性的預設值為0.0。
- `CenterY`，屬於類型 `double` ，表示此轉換中心點的 y 座標。 這個屬性的預設值為0.0。
- `ScaleX`，屬於類型 `double` ，表示 X 軸縮放比例。 這個屬性的預設值為1.0。
- `ScaleY`，屬於類型 `double` ，表示 y 軸縮放比例。 這個屬性的預設值為1.0。
- `SkewX`，屬於類型 `double` ，表示 X 軸扭曲角度（以從 y 軸逆時針算起的度為測量單位）。 這個屬性的預設值為0.0。
- `SkewY`，屬於類型 `double` ，表示 y 軸扭曲角度（以從 X 軸逆時針算起的度為測量單位）。 這個屬性的預設值為0.0。
- `Rotation`，屬於類型 `double` ，表示順時針旋轉的角度（以度為單位）。 這個屬性的預設值為0.0。
- `TranslateX`，屬於類型 `double` ，表示沿著 X 軸移動的距離。 這個屬性的預設值為0.0。
- `TranslateY`，屬於類型 `double` ，表示沿著 y 軸移動的距離。 這個屬性的預設值為0.0。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

會依照下列 `CompositeTransform` 順序套用轉換：

1. 縮放（ `ScaleX` 和 `ScaleY` ）。
1. 扭曲（ `SkewX` 和 `SkewY` ）。
1. 旋轉（ `Rotation` ）。
1. 轉譯（ `TranslateX` 、 `TranslateY` ）。

如果您想要以不同的順序將多個轉換套用至物件，您應該建立， `TransformGroup` 並依您想要的順序插入轉換。

> [!IMPORTANT]
> 會 `CompositeTransform` 針對所有轉換使用相同的中心點 `CenterX` 和 `CenterY` 。 如果您想要針對每個轉換指定不同的中心點，請使用 `TransformGroup` 、

下列範例顯示如何使用類別執行複合轉換 `CompositeTransform` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <CompositeTransform ScaleX="2"
                            ScaleY="2"
                            Rotation="45"
                            TranslateX="50"
                            TranslateY="50" />
    </Path.RenderTransform>
</Path>
```

在此範例中， `Path` 物件會調整為其大小的兩倍，旋轉45度，並以50裝置獨立單位轉譯。

## <a name="custom-transforms"></a>自訂轉換

矩陣轉換會使用仿射矩陣來操作2D 平面中的物件或座標系統。 3x3 矩陣用於轉換。 您可以乘以仿射矩陣轉換來形成線性轉換，例如轉換後的旋轉和扭曲。

`MatrixTransform`衍生自類別的類別會 `Transform` 定義下列屬性：

- `Matrix`，屬於類型 `Matrix` ，代表定義轉換的矩陣。

此屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以是資料系結的目標，以及樣式化的。

`MatrixTransform`類別是用來建立 `RotateTransform` 、 `ScaleTransform` 、 `SkewTransform` 或類別未提供的自訂轉換 `TranslateTransform` 。

下列範例顯示如何使用來轉換 `Path` 物件 `MatrixTransform` ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform>
            <MatrixTransform.Matrix>
                <!-- M11 stretches, M12 skews -->
                <Matrix OffsetX="10"
                        OffsetY="100"
                        M11="3"
                        M12="2" />
            </MatrixTransform.Matrix>
        </MatrixTransform>
    </Path.RenderTransform>
</Path>
```

在此範例中， `Path` 物件會在 X 和 Y 維度中伸展、扭曲和位移。

或者，也可以使用內建在中的型別轉換器，以簡化的形式撰寫 Xamarin.Forms ：

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform Matrix="3,2,0,1,10,100" />
    </Path.RenderTransform>
</Path>
```

在此範例中， `Matrix` 屬性會指定為以逗號分隔的字串，由六個成員組成： `M11` 、 `M12` 、 `M21` 、 `M22` 、 `OffsetX` 、 `OffsetY` 。

## <a name="related-links"></a>相關連結

- [ShapeDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms形狀](index.md)
