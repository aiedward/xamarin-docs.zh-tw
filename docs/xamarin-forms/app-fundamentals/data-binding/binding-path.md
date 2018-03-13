---
title: "繫結路徑"
description: "使用資料繫結至存取子的屬性和集合成員"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: fb385a9c7d1dfd01d95691b77122cdbb84d814e5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="binding-path"></a>繫結路徑

在所有先前的資料繫結範例， [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/)屬性`Binding`類別 (或[ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Path/)屬性`Binding`標記延伸) 已設定指派給單一的屬性。 它可實際設定`Path`至*子屬性*（屬性的屬性），或集合的成員。

例如，假設您的頁面包含`TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

`Time`屬性`TimePicker`的型別`TimeSpan`，但也許您想要建立資料繫結參考`TotalSeconds`屬性，`TimeSpan`值。 以下是資料繫結：

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```
         
`Time`屬性屬於型別`TimeSpan`，其具有`TotalSeconds`屬性。 `Time`和`TotalSeconds`屬性是 simply 連線以句號。 中的項目`Path`字串一律參考屬性而非這些屬性的型別。

範例及數個其他項目會出現在**路徑變化**頁面：

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
                              StringFormat='The first Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

在第二個`Label`，繫結來源是本身的頁面。 `Content`屬性屬於型別`StackLayout`，其具有`Children`型別的屬性`IList<View>`，具有`Count`屬性表示的子系數目。

## <a name="paths-with-indexers"></a>與索引子路徑

第三個中的繫結`Label`中**路徑變化**頁面參考[ `CultureInfo` ](https://developer.xamarin.com/api/type/System.Globalization.CultureInfo/)類別`System.Globalization`命名空間：

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

來源設定為靜態`CultureInfo.CurrentCulture`屬性，這是類型的物件`CultureInfo`。 類別會定義名為的屬性`DateTimeFormat`型別的[ `DateTimeFormatInfo` ](https://developer.xamarin.com/api/type/System.Globalization.DateTimeFormatInfo/)包含`DayNames`集合。 索引會選取第四個項目。

第四個`Label`沒有類似但文化特性相關聯法國的項目。 `Source`繫結的屬性設定為`CultureInfo`物件建構函式：

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

請參閱[傳遞建構函式引數](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)如需有關在 XAML 中指定建構函式引數。

最後，最後一個範例如下第二個，不同之處在於它所參考的子系的其中一個`StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

子系是`Label`，其具有`Text`型別的屬性`String`，其具有`Length`屬性。 第一個`Label`報表`TimeSpan`中設定`TimePicker`，因此，當該文字會變更，最終`Label`跟著變更。

以下是所有三個平台上執行的程式：

[![路徑變化](binding-path-images/pathvariations-small.png "路徑變化")](binding-path-images/pathvariations-large.png#lightbox "路徑變化")

## <a name="debugging-complex-paths"></a>偵錯複雜的路徑

複雜路徑定義很難建構： 您需要知道每一個子屬性的型別，或者要正確地加入 [下一步] 子屬性，集合中的項目類型，但將型別本身不會出現在路徑中。 一個好的技術是以累加方式建置的路徑，並查看中繼結果。 對於該最後一個範例中，您可以開始沒有`Path`完全定義：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

顯示繫結來源的類型或`DataBindingDemos.PathVariationsPage`。 您知道`PathVariationsPage`衍生自`ContentPage`，所以它有`Content`屬性：

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

型別`Content`屬性現在會顯示為`Xamarin.Forms.StackLayout`。 新增`Children`屬性`Path`且類型為`Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`，這是透過 Xamarin.Forms，但很明顯地集合類型的內部類別。 將索引加入至該且類型為`Xamarin.Forms.Label`。 以這種方式繼續。

Xamarin.Forms 會處理繫結路徑，它會安裝`PropertyChanged`上實作的路徑中的任何物件的處理常式`INotifyPropertyChanged`介面。 最後一個繫結，例如會第一個範圍中的變更做出反應`Label`因為`Text`屬性變更。 

如果繫結路徑中的屬性未實作`INotifyPropertyChanged`，將忽略該屬性的任何變更。 某些變更完全無法讓失效的繫結路徑，讓您在屬性和子屬性的字串不會變成無效時，才應該使用這項技術。



## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
