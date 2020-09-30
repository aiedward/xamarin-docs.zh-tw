---
title: Xamarin.Forms 系結的回退
description: 本文說明如何定義繫結失敗時要使用的後援值，以讓繫結更穩固。
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 24a9a351dbe6932b09add2ee7c3111256e013201
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560685"
---
# <a name="no-locxamarinforms-binding-fallbacks"></a>Xamarin.Forms 系結的回退

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

有時，資料繫結會因為無法解析繫結來源，或繫結成功但卻傳回 `null` 值而失敗。 雖然這些情況都可以透過值轉換器或其他額外程式碼來處理，但您可以藉由定義繫結程序失敗時要使用的後援值，讓資料繫結更穩固。 您可以藉由在系結 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 運算式中定義和屬性來完成這 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 項作業。 因為這些屬性位於類別中 [`BindingBase`](xref:Xamarin.Forms.BindingBase) ，所以它們可以搭配系結、多系結、編譯的系結，以及加上 `Binding` 標記延伸模組使用。

> [!NOTE]
> 在系結 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 運算式中使用和屬性是選擇性的。

## <a name="defining-a-fallback-value"></a>定義後援值

[`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue)屬性可讓您定義要在無法解析系結*來源*時使用的 fallback 值。 設定這個屬性的常見案例是：當您要繫結之來源屬性並未存在於異質類型繫結集合中的所有物件時。

**MonkeyDetail** 頁面說明如何設定 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 屬性：

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

上的系結 [`Label`](xref:Xamarin.Forms.Label) 會定義在 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 無法解析系結來源時，要在目標上設定的值。 因此，如果繫結物件上不存在 `Population` 屬性，即會顯示 `FallbackValue` 屬性所定義的值。 請注意，這裡的 `FallbackValue` 屬性值會以單引號字元分隔。

[`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue)建議您將屬性值定義為中的資源，而不是定義內嵌的屬性值 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 這種方法的優點是這類值在單一位置中只會定義一次，並更容易進行當地語系化。 接著，即可使用 `StaticResource` 標記延伸來擷取資源：

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> 您無法使用繫結運算式來設定 `FallbackValue` 屬性。

以下是程式執行情況：

![FallbackValue 系結](binding-fallbacks-images/bindingunavailable-detail-cropped.png "FallbackValue 系結")

當系結 `FallbackValue` 運算式中未設定屬性，而且未解析系結路徑或部分路徑時， [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) 會在目標上設定。 不過，若已設定 `FallbackValue` 屬性，但未解析繫結路徑或部分路徑時，則會在目標上設定 `FallbackValue` 值屬性的值。 因此，**MonkeyDetail** 頁面中的 [`Label`](xref:Xamarin.Forms.Label) 會顯示 "Population size unknown" (母體大小未知)，因為繫結物件缺少 `Population` 屬性。

> [!IMPORTANT]
> 當設定屬性時，不會在系結運算式中執行定義的值轉換器 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 。

## <a name="defining-a-null-replacement-value"></a>定義 Null 取代值

[`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue)屬性可讓您定義將在解析系結*來源*時使用的取代值，但值為 `null` 。 設定這個屬性的常見案例是：當您要繫結的來源屬性可能是繫結集合中的 `null` 時。

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

和兩者的系 [`Image`](xref:Xamarin.Forms.Image) 結 [`Label`](xref:Xamarin.Forms.Label) 都會定義系結路徑傳回時要套用的 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 值 `null` 。 因此，針對集合中未定義 `ImageUrl` 和 `Location` 屬性的任何物件，即會顯示 `TargetNullValue` 屬性所定義的值。 請注意，這裡的 `TargetNullValue` 屬性值會以單引號字元分隔。

[`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue)建議您將屬性值定義為中的資源，而不是定義內嵌的屬性值 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 這種方法的優點是這類值在單一位置中只會定義一次，並更容易進行當地語系化。 接著，即可使用 `StaticResource` 標記延伸來擷取資源：

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> 您無法使用繫結運算式來設定 `TargetNullValue` 屬性。

以下是程式執行情況：

[![>targetnullvalue 系結](binding-fallbacks-images/bindingunavailable-small.png ">targetnullvalue 系結")](binding-fallbacks-images/bindingunavailable-large.png#lightbox ">targetnullvalue 系結")

當繫結運算式未設定 `TargetNullValue` 屬性時，會轉換 `null` 的來源值 (如果已定義值轉換器)，並加以格式化 (如果已定義 `StringFormat`)，然後在目標上設定結果。 不過，若已設定 `TargetNullValue` 屬性，則會轉換 `null` 的來源值 (如果已定義值轉換器)；如果轉換後仍為 `null`，就會在目標上設定 `TargetNullValue`。

> [!IMPORTANT]
> 若已設定 `TargetNullValue` 屬性，字串格式就不會套用到繫結運算式中。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))