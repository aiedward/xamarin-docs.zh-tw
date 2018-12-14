---
title: Xamarin.Forms 繫結後援
description: 本文說明如何定義繫結失敗時要使用的後援值，以讓繫結更穩固。
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: 2a4b29df9148ce695f8f3ca5377e5848af1b775a
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171595"
---
# <a name="xamarinforms-binding-fallbacks"></a>Xamarin.Forms 繫結後援

有時，資料繫結會因為無法解析繫結來源，或繫結成功但卻傳回 `null` 值而失敗。 雖然這些情況都可以透過值轉換器或其他額外程式碼來處理，但您可以藉由定義繫結程序失敗時要使用的後援值，讓資料繫結更穩固。 您可以在繫結運算式中定義 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 和 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 屬性來完成上述作業。 因為這些屬性位於 [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) 類別中，因此可以搭配使用繫結、編譯的繫結與 `Binding` 標記延伸。

> [!NOTE]
> 您可以選擇性地使用繫結運算式中的 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 和 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 屬性。

## <a name="defining-a-fallback-value"></a>定義後援值

[`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 屬性可讓您定義在無法解析繫結「來源」時要使用的後援值。 設定這個屬性的常見案例是：當您要繫結之來源屬性並未存在於異質類型繫結集合中的所有物件時。

**MonkeyDetail** 頁面說明如何設定 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 屬性：

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

[`Label`](xref:Xamarin.Forms.Label) 上的繫結可定義當無法解析繫結來源時要在目標上設定的 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 值。 因此，如果繫結物件上不存在 `Population` 屬性，即會顯示 `FallbackValue` 屬性所定義的值。 請注意，這裡的 `FallbackValue` 屬性值會以單引號字元分隔。

建議您將 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 屬性值定義為 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中的資源，而不是定義為內嵌。 這種方法的優點是這類值在單一位置中只會定義一次，並更容易進行當地語系化。 接著，即可使用 `StaticResource` 標記延伸來擷取資源：

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> 您無法使用繫結運算式設定 `FallbackValue` 屬性。

以下是程式執行情況：

![FallbackValue 繫結](binding-fallbacks-images/bindingunavailable-detail-cropped.png "FallbackValue 繫結")

若繫結運算式未設定 `FallbackValue` 屬性，且未解析繫結路徑或部分路徑時，會在目標上設定 [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue)。 不過，若已設定 `FallbackValue` 屬性，但未解析繫結路徑或部分路徑時，則會在目標上設定 `FallbackValue` 值屬性的值。 因此，**MonkeyDetail** 頁面中的 [`Label`](xref:Xamarin.Forms.Label) 會顯示 "Population size unknown" (母體大小未知)，因為繫結物件缺少 `Population` 屬性。

> [!IMPORTANT]
> 設定 [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) 屬性時，繫結運算式不會執行已定義的值轉換器。

## <a name="defining-a-null-replacement-value"></a>定義 Null 取代值

[`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 屬性可讓您定義在已解析繫結「來源」但值為 `null` 時要使用的取代值。 設定這個屬性的常見案例是：當您要繫結的來源屬性可能是繫結集合中的 `null` 時。

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

[`Image`](xref:Xamarin.Forms.Image) 和 [`Label`](xref:Xamarin.Forms.Label) 上的繫結均可定義當繫結路徑傳回 `null` 時要套用的 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 值。 因此，針對集合中未定義 `ImageUrl` 和 `Location` 屬性的任何物件，即會顯示 `TargetNullValue` 屬性所定義的值。 請注意，這裡的 `TargetNullValue` 屬性值會以單引號字元分隔。

建議您將 [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) 屬性值定義為 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中的資源，而不是定義為內嵌。 這種方法的優點是這類值在單一位置中只會定義一次，並更容易進行當地語系化。 接著，即可使用 `StaticResource` 標記延伸來擷取資源：

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> 您無法使用繫結運算式來設定 `TargetNullValue` 屬性。

以下是程式執行情況：

[![TargetNullValue 繫結](binding-fallbacks-images/bindingunavailable-small.png "TargetNullValue 繫結")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "TargetNullValue 繫結")

當繫結運算式未設定 `TargetNullValue` 屬性時，會轉換 `null` 的來源值 (如果已定義值轉換器)，並加以格式化 (如果已定義 `StringFormat`)，然後在目標上設定結果。 不過，若已設定 `TargetNullValue` 屬性，則會轉換 `null` 的來源值 (如果已定義值轉換器)；如果轉換後仍為 `null`，就會在目標上設定 `TargetNullValue`。

> [!IMPORTANT]
> 若已設定 `TargetNullValue` 屬性，字串格式就不會套用到繫結運算式中。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) (資料繫結示範 (範例))
