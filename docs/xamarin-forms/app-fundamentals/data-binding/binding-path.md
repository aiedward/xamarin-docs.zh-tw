---
title: Xamarin.Forms系結路徑
description: 本文說明如何使用資料系結，以系結 Xamarin.Forms 類別的 Path 屬性來存取子屬性和集合成員。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0657f39d0584405ea1a6d7611ac789488817770f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139735"
---
# <a name="xamarinforms-binding-path"></a>Xamarin.Forms系結路徑

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

在所有先前的資料系結範例中， [`Path`](xref:Xamarin.Forms.Binding.Path) 類別的屬性 `Binding` （或 [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) `Binding` 標記延伸的屬性）已設定為單一屬性。 將 `Path` 設成「子屬性」**(屬性的屬性) 或集合成員實際上是可行的。

例如，假設您的頁面包含 `TimePicker`：

```xaml
<TimePicker x:Name="timePicker">
```

`TimePicker` 的 `Time` 屬性類別為 `TimeSpan`，但您可能想要建立資料繫結，參考該 `TimeSpan` 值的 `TotalSeconds` 屬性。 以下是資料繫結：

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

`Time` 屬性的類型為 `TimeSpan`，其具有 `TotalSeconds` 屬性。 `Time` 和 `TotalSeconds` 屬性只簡單使用句號連接。 `Path` 字串中的項目一律表示屬性，且不是這些屬性的類型。

[Path Variations] \(路徑變化\)**** 頁面會顯示該範例及其他許多範例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:globe="clr-namespace:System.Globalization;assembly=netstandard"
             x:Class="DataBindingDemos.PathVariationsPage"
             Title="Path Variations"
             x:Name="page">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="HorizontalTextAlignment" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10, 0">
        <TimePicker x:Name="timePicker" />

        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time.TotalSeconds,
                              StringFormat='{0} total seconds'}" />

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children.Count,
                              StringFormat='There are {0} children in this StackLayout'}" />

        <Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                              Path=DateTimeFormat.DayNames[3],
                              StringFormat='The middle day of the week is {0}'}" />

        <Label>
            <Label.Text>
                <Binding Path="DateTimeFormat.DayNames[3]"
                         StringFormat="The middle day of the week in France is {0}">
                    <Binding.Source>
                        <globe:CultureInfo>
                            <x:Arguments>
                                <x:String>fr-FR</x:String>
                            </x:Arguments>
                        </globe:CultureInfo>
                    </Binding.Source>
                </Binding>
            </Label.Text>
        </Label>

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children[1].Text.Length,
                              StringFormat='The second Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

在第二個 `Label` 中，繫結來源是網頁本身。 `Content` 屬性的類型為 `StackLayout`，其 `Children` 屬性的類型為 `IList<View>`，它又包含指示子系數目的 `Count` 屬性。

## <a name="paths-with-indexers"></a>具有索引子的路徑

[Path Variations] \(路徑變化\)**** 頁面中第三個 `Label` 的繫結會參考 `System.Globalization` 命名空間中的 [`CultureInfo`](xref:System.Globalization.CultureInfo) 類別：

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

來源設為靜態的 `CultureInfo.CurrentCulture` 屬性，其為類型 `CultureInfo` 的物件。 該類別會定義名為且 `DateTimeFormat` 包含集合之類型的屬性 [`DateTimeFormatInfo`](xref:System.Globalization.DateTimeFormatInfo) `DayNames` 。 索引會選取第四個項目。

第四個 `Label` 作用類似，但適用於與法國建立關聯的文化特性。 繫結的 `Source` 屬性設為具有建構函式的 `CultureInfo` 物件：

```xaml
<Label>
    <Label.Text>
        <Binding Path="DateTimeFormat.DayNames[3]"
                 StringFormat="The middle day of the week in France is {0}">
            <Binding.Source>
                <globe:CultureInfo>
                    <x:Arguments>
                        <x:String>fr-FR</x:String>
                    </x:Arguments>
                </globe:CultureInfo>
            </Binding.Source>
        </Binding>
    </Label.Text>
</Label>
```

如需在 XAML 中指定建構函式引數的詳細資料，請參閱[傳遞建構函式引數](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)。

最後，最後一個範例類似第二個，不同之處在於它參考的是 `StackLayout` 子系的其中之一：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

該子系為 `Label`，其 `Text` 屬性的類型為包含 `Length` 屬性的 `String`。 第一個 `Label` 回報 `TimePicker` 中設定的 `TimeSpan`，所以當該文字變更時，最終的 `Label` 也跟著變更。

以下是程式執行情況：

[![路徑變化](binding-path-images/pathvariations-small.png "路徑變化")](binding-path-images/pathvariations-large.png#lightbox "路徑變化")

## <a name="debugging-complex-paths"></a>偵錯複雜路徑

複雜的路徑定義建構困難：您需要知道每個子屬性的類型或集合項目的類型，才能正確新增下一個子屬性，但類型本身不會出現在路徑中。 一個良好技巧是以累加方式建置的路徑，並查看中繼結果。 至於最後一個範例，您完全可以從沒有 `Path` 定義開始：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

它會顯示繫結來源屬性類型或 `DataBindingDemos.PathVariationsPage`。 您知道 `PathVariationsPage` 衍生自 `ContentPage`，所以它有 `Content` 屬性：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

`Content` 屬性的類型現顯示為 `Xamarin.Forms.StackLayout`。 將 `Children` 屬性加入至， `Path` 而類型為 `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]` ，這是的內部類別 Xamarin.Forms ，但明顯是集合類型。 將索引新增至該範例，且類型為 `Xamarin.Forms.Label`。 繼續以這種方式進行。

當 Xamarin.Forms 處理系結路徑時，它會在 `PropertyChanged` 路徑中執行介面的任何物件上安裝處理常式 `INotifyPropertyChanged` 。 例如，因為 `Text` 屬性變更，所以最後一個繫結回應第一個 `Label` 中的變更。

如果繫結路徑中的屬性不實作 `INotifyPropertyChanged`，則忽略該屬性的所有變更。 某些變更可能會讓繫結路徑完全失效，所以您只有在屬性和子屬性的字串還未失效前，才使用這項技術。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
- [書籍中的資料系結章節 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
