---
title: Xamarin.Forms 繫結後援
description: 本文說明如何定義繫結失敗時要使用的後援值，以讓繫結更穩固。
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: 67fd8070ae36bdc1a90b8a33b25f13369d8d995d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "68650203"
---
# <a name="xamarinforms-binding-fallbacks"></a>Xamarin.Forms 繫結後援

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

有時，資料繫結會因為無法解析繫結來源，或繫結成功但卻傳回 `null` 值而失敗。 雖然這些情況都可以透過值轉換器或其他額外程式碼來處理，但您可以藉由定義繫結程序失敗時要使用的後援值，讓資料繫結更穩固。 這可以通過定義綁定表達式[`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue)中的[`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue)和 屬性來實現。 由於這些屬性駐留在類中[`BindingBase`](xref:Xamarin.Forms.BindingBase),因此它們可用於綁定、編譯綁定`Binding`和 標記擴展。

> [!NOTE]
> 綁定表示式中的[`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue)[`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue)和 屬性的使用是可選的。

## <a name="defining-a-fallback-value"></a>定義後援值

屬性[`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue)允許定義一個回退值,該值將在無法解析綁定*來源*時使用。 設定這個屬性的常見案例是：當您要繫結之來源屬性並未存在於異質類型繫結集合中的所有物件時。

**MonkeyDetail** 頁面說明如何設定 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 屬性：

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

上的綁定[`Label`](xref:Xamarin.Forms.Label)定義一[`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue)個 值,如果無法解析綁定源,將在目標上設置該值。 因此，如果繫結物件上不存在 `Population` 屬性，即會顯示 `FallbackValue` 屬性所定義的值。 請注意，這裡的 `FallbackValue` 屬性值會以單引號字元分隔。

建議將它們定義為[`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue)[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中的資源,而不是內聯定義屬性值。 這種方法的優點是這類值在單一位置中只會定義一次，並更容易進行當地語系化。 接著，即可使用 `StaticResource` 標記延伸來擷取資源：

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> 您無法使用繫結運算式來設定 `FallbackValue` 屬性。

以下是程式執行情況：

![遞迴退值繫結](binding-fallbacks-images/bindingunavailable-detail-cropped.png "遞迴退值繫結")

當`FallbackValue`屬性未在綁定表示式中設置且綁定路徑或路徑的一部分未解析時[`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue), 則設置在目標上。 不過，若已設定 `FallbackValue` 屬性，但未解析繫結路徑或部分路徑時，則會在目標上設定 `FallbackValue` 值屬性的值。 因此，**MonkeyDetail** 頁面中的 [`Label`](xref:Xamarin.Forms.Label) 會顯示 "Population size unknown" (母體大小未知)，因為繫結物件缺少 `Population` 屬性。

> [!IMPORTANT]
> 設置[`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue)屬性時,不會在綁定表達式中執行定義的值轉換器。

## <a name="defining-a-null-replacement-value"></a>定義 Null 取代值

屬性[`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue)允許定義替換值,該值將在解析繫結*源 時*使用,但該`null`值為 。 設定這個屬性的常見案例是：當您要繫結的來源屬性可能是繫結集合中的 `null` 時。

**Monkeys** 頁面說明如何設定 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 屬性：

```xaml
<ListView ItemsSource="{Binding Monkeys}"
          ...>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Image Source="{Binding ImageUrl, TargetNullValue='https://upload.wikimedia.org/wikipedia/commons/2/20/Point_d_interrogation.jpg'}"
                           ... />
                    ...
                    <Label Text="{Binding Location, TargetNullValue='Location unknown'}"
                           ... />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

[`Image`](xref:Xamarin.Forms.Image)和上的[`Label`](xref:Xamarin.Forms.Label)綁定定義[`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue)將在綁定路徑返回`null`時應用的值。 因此，針對集合中未定義 `ImageUrl` 和 `Location` 屬性的任何物件，即會顯示 `TargetNullValue` 屬性所定義的值。 請注意，這裡的 `TargetNullValue` 屬性值會以單引號字元分隔。

建議將它們定義為[`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue)[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中的資源,而不是內聯定義屬性值。 這種方法的優點是這類值在單一位置中只會定義一次，並更容易進行當地語系化。 接著，即可使用 `StaticResource` 標記延伸來擷取資源：

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> 您無法使用繫結運算式來設定 `TargetNullValue` 屬性。

以下是程式執行情況：

[![目標空值繫結](binding-fallbacks-images/bindingunavailable-small.png "目標空值繫結")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "目標空值繫結")

當繫結運算式未設定 `TargetNullValue` 屬性時，會轉換 `null` 的來源值 (如果已定義值轉換器)，並加以格式化 (如果已定義 `StringFormat`)，然後在目標上設定結果。 不過，若已設定 `TargetNullValue` 屬性，則會轉換 `null` 的來源值 (如果已定義值轉換器)；如果轉換後仍為 `null`，就會在目標上設定 `TargetNullValue`。

> [!IMPORTANT]
> 若已設定 `TargetNullValue` 屬性，字串格式就不會套用到繫結運算式中。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
