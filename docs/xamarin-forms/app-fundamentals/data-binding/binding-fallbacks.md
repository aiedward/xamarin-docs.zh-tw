---
title: Xamarin.Forms 繫結後援
description: 這篇文章說明如何讓繫結的更穩固，藉由定義後援的值，如果繫結會失敗。
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: fa375720730630065609e328b343e16578c6f1df
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131191"
---
# <a name="xamarinforms-binding-fallbacks"></a>Xamarin.Forms 繫結後援

有時資料繫結失敗，因為繫結來源不能解析，或繫結成功，但卻傳回`null`值。 雖然可以處理這些情況下，值轉換器或其他額外的程式碼，資料繫結可以成為更強固藉由定義要使用的繫結程序失敗時的後援值。 這可藉由定義[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)並[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)繫結運算式中的屬性。 因為這些屬性位於[ `BindingBase` ](xref:Xamarin.Forms.BindingBase)類別，它們可以用於搭配繫結，已編譯的繫結，與`Binding`標記延伸。

> [!NOTE]
> 利用[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)並[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)是選擇性的繫結運算式中的屬性。

## <a name="defining-a-fallback-value"></a>定義後援值

[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)屬性允許將使用後援值定義時繫結*來源*無法解析。 繫結至異質性類型的繫結集合中的所有物件可能不存在的來源屬性時設定這個屬性的常見案例。

**MonkeyDetail**頁說明設定[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)屬性：

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

上的繫結[ `Label` ](xref:Xamarin.Forms.Label)定義[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)會在目標設定，當繫結來源不能解析的值。 因此，所定義的值`FallbackValue`會顯示屬性，如果`Population`屬性不存在繫結物件上。 請注意，這裡`FallbackValue`屬性值以單引號 （撇號） 字元分隔。

而不是定義[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)內嵌屬性值，建議您將其定義為中的資源[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 這種方法的優點是這類值在單一位置中，定義一次，並會更方便進行當地語系化。 資源可以再使用擷取`StaticResource`標記延伸模組：

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> 不可以設定`FallbackValue`屬性繫結運算式。

以下是所有三個平台上執行的程式：

![FallbackValue 繫結](binding-fallbacks-images/bindingunavailable-detail-cropped.png "FallbackValue 繫結")

當`FallbackValue`屬性未設定繫結運算式和繫結路徑或路徑的一部分仍未解析， [ `BindableProperty.DefaultValue` ](xref:Xamarin.Forms.BindableProperty.DefaultValue)設定在目標上。 不過，當`FallbackValue`屬性是設定和繫結路徑或路徑的一部分無法解決，值`FallbackValue`value 屬性會設定在目標上。 因此，在**MonkeyDetail**頁[ `Label` ](xref:Xamarin.Forms.Label)會顯示 「 未知的母體大小 」，因為在繫結的物件缺少`Population`屬性。

> [!IMPORTANT]
> 繫結運算式中不會執行已定義的值轉換器時[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue)屬性設定。

## <a name="defining-a-null-replacement-value"></a>定義為 null 的取代值

[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)屬性允許將使用的取代值定義時繫結*來源*即解決，但值為`null`。 設定這個屬性的常見案例是當繫結至來源屬性可能`null`繫結集合中。

**猴仔**頁說明設定[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)屬性：

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

上的繫結[ `Image` ](xref:Xamarin.Forms.Image)並[ `Label` ](xref:Xamarin.Forms.Label)均定義[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)值將會套用，如果繫結路徑傳回`null`. 因此，所定義的值`TargetNullValue`屬性會顯示集合中的任何物件所在`ImageUrl`和`Location`屬性未定義。 請注意，這裡`TargetNullValue`屬性值以單引號 （撇號） 字元分隔。

而不是定義[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue)內嵌屬性值，建議您將其定義為中的資源[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 這種方法的優點是這類值在單一位置中，定義一次，並會更方便進行當地語系化。 資源可以再使用擷取`StaticResource`標記延伸模組：

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> 不可以設定`TargetNullValue`屬性繫結運算式。

以下是所有三個平台上執行的程式：

[![TargetNullValue 繫結](binding-fallbacks-images/bindingunavailable-small.png "TargetNullValue 繫結")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "TargetNullValue 繫結")

當`TargetNullValue`屬性未設定在繫結運算式中，來源值的`null`會被轉換，如果已定義的值轉換器，格式化如果`StringFormat`定義，則結果然後設定在目標上。 不過，當`TargetNullValue`設定屬性時，來源值`null`如果已定義的值轉換器，，和其仍會被轉換`null`轉換之後，windows 7`TargetNullValue`目標上設定屬性。

> [!IMPORTANT]
> 字串格式不會套用繫結運算式時`TargetNullValue`屬性設定。

## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
