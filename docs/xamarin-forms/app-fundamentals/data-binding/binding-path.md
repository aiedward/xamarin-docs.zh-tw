---
title: Xamarin.Forms 繫結路徑
description: 這篇文章說明如何使用 Xamarin.Forms 資料繫結來存取子的屬性和繫結類別的路徑屬性的集合成員。
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 5ffc167b1e5695663dff6005f3d7e0ba0ea958db
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172102"
---
# <a name="xamarinforms-binding-path"></a>Xamarin.Forms 繫結路徑

在所有先前的資料繫結範例， [ `Path` ](xref:Xamarin.Forms.Binding.Path)屬性`Binding`類別 (或[ `Path` ](xref:Xamarin.Forms.Xaml.BindingExtension.Path)屬性`Binding`標記延伸模組) 已設定指派給單一的屬性。 您可實際設定`Path`要*子屬性*（屬性的屬性），或集合的成員。

例如，假設您的頁面包含`TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

`Time`的屬性`TimePicker`別的`TimeSpan`，但也許您想要建立資料繫結參考`TotalSeconds`屬性`TimeSpan`值。 以下是資料繫結：

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

`Time`屬性的類型是`TimeSpan`，其中包含`TotalSeconds`屬性。 `Time`和`TotalSeconds`屬性都只連線以句號。 中的項目`Path`字串一律會參考屬性，不適用於這些屬性的型別。

範例和其他許多選項，會顯示在**路徑變化**頁面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:globe="clr-namespace:System.Globalization;assembly=mscorlib"
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

在第二個`Label`，繫結來源是網頁本身。 `Content`屬性的類型是`StackLayout`，其中包含`Children`屬性的型別`IList<View>`，其中包含`Count`屬性，指示子系數目。

## <a name="paths-with-indexers"></a>使用索引子路徑

第三個中的繫結`Label`中**路徑變化**頁面參考[ `CultureInfo` ](xref:System.Globalization.CultureInfo)類別`System.Globalization`命名空間：

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

來源會設定為靜態`CultureInfo.CurrentCulture`屬性，這是型別的物件`CultureInfo`。 類別會定義名為的屬性`DateTimeFormat`型別的[ `DateTimeFormatInfo` ](xref:System.Globalization.DateTimeFormatInfo) ，其中包含`DayNames`集合。 索引會選取第四個項目。

第四個`Label`作用也類似但文化特性相關聯法國。 `Source`繫結的屬性設定為`CultureInfo`物件的建構函式：

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

請參閱[傳遞建構函式引數](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)如需詳細資訊在 XAML 中指定建構函式引數。

最後，最後一個範例大致第二個，不同之處在於它所參考的子系的其中一個`StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

是該子`Label`，其中包含`Text`型別的屬性`String`，其中包含`Length`屬性。 第一個`Label`報表`TimeSpan`集中`TimePicker`，因此，當該文字會變更，最終`Label`跟著變更。

以下是執行的程式：

[![路徑變化](binding-path-images/pathvariations-small.png "路徑變化")](binding-path-images/pathvariations-large.png#lightbox "路徑變化")

## <a name="debugging-complex-paths"></a>偵錯複雜的路徑

複雜的路徑定義可能很難建構： 您需要知道每一個子屬性的類型或要正確地加入 [下一步] 的子屬性，集合中的項目類型，但將型別本身不會出現在路徑中。 一個很好的技巧，就是以累加方式建置的路徑，並看看中繼結果。 對於該最後一個範例中，您可以開始沒有`Path`完全定義：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

所顯示的繫結來源，類型或`DataBindingDemos.PathVariationsPage`。 您知道`PathVariationsPage`衍生自`ContentPage`，所以它沒有`Content`屬性：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

型別`Content`屬性現在會顯示為`Xamarin.Forms.StackLayout`。 新增`Children`屬性，以`Path`且類型為`Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`，這是內部 Xamarin.Forms，但顯然是集合型別類別。 將索引加入至該且類型為`Xamarin.Forms.Label`。 在這種方式繼續進行。

Xamarin.Forms 處理繫結路徑，它會安裝`PropertyChanged`實作的路徑中的任何物件上的處理常式`INotifyPropertyChanged`介面。 最後一個繫結，例如回應變更，以在第一個`Label`因為`Text`屬性變更。

如果繫結路徑中的屬性不會實作`INotifyPropertyChanged`，將會忽略該屬性的任何變更。 某些變更可能完全會失效繫結路徑，因此您應該在屬性和子屬性的字串永遠不會變成無效時，才使用這項技術。



## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
