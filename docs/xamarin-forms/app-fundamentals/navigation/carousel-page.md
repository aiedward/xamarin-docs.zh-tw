---
title: Xamarin.Forms 浮動切換頁面
description: Xamarin.Forms CarouselPage 是內容的使用者可以向側邊撥動可巡覽，例如資源庫頁面的頁面。 這篇文章會示範如何使用 CarouselPage 瀏覽分頁的集合。
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: bce3a60f3647a537906cfa11fc1dcfcc6f5cf365
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998600"
---
# <a name="xamarinforms-carousel-page"></a>Xamarin.Forms 浮動切換頁面

_Xamarin.Forms CarouselPage 是內容的使用者可以向側邊撥動可巡覽，例如資源庫頁面的頁面。這篇文章會示範如何使用 CarouselPage 瀏覽分頁的集合。_

## <a name="overview"></a>總覽

下列螢幕擷取畫面所示[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)每個平台：

![](carousel-page-images/thirdpage.png "CarouselPage Thid 項目")

版面配置[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)每個平台上都相同。 頁面可以瀏覽，撥動由右至左瀏覽集合中，透過轉送和撥動左到右向後巡覽集合。 下列螢幕擷取畫面顯示的第一頁[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)執行個體：

![](carousel-page-images/firstpage.png "CarouselPage 第一個項目")

揮擊由右至左移到第二個頁面上，如下列螢幕擷取畫面所示：

![](carousel-page-images/secondpage.png "CarouselPage 第二個項目")

撥動從左到右回到前一個頁面時，一次從右到左撥動移動到第三個頁面上。

<!--
> [!NOTE]
> The [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) has been deprecated, and will be removed from Xamarin.Forms in a future release. Instead, the [`CarouselView`](xref:Xamarin.Forms.CarouselView) should be used to provide a gallery-like view, where users can swipe from side to side to move through a collection of items.
-->

## <a name="creating-a-carouselpage"></a>建立 CarouselPage

兩種方法可用來建立[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage):

- [填入](#Populating_a_CarouselPage_with_a_Page_Collection)`CarouselPage`的子集合[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)執行個體。
- [指派](#Populating_a_CarouselPage_with_a_Template)集合[ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource)屬性，並將指派[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)至[ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)屬性以傳回[`ContentPage` ](xref:Xamarin.Forms.ContentPage)集合中物件的執行個體。

使用這兩種方法，`CarouselPage`將然後顯示每一頁，以移至下一個頁面中，要顯示揮擊互動。

> [!NOTE]
> A [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)才會填入與[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)執行個體，或`ContentPage`衍生項目。

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populating-a-carouselpage-with-a-page-collection"></a>填入 CarouselPage 頁面集合

下列 XAML 程式碼範例所示[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)會顯示三個[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)執行個體：

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <ContentPage>
        <ContentPage.Padding>
          <OnPlatform x:TypeArguments="Thickness">
              <On Platform="iOS, Android" Value="0,40,0,0" />
          </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
            <Label Text="Red" FontSize="Medium" HorizontalOptions="Center" />
            <BoxView Color="Red" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
        </StackLayout>
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
</CarouselPage>
```

下列程式碼範例顯示 C# 中的對等的 UI:

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        var redContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                Children = {
                    new Label {
                        Text = "Red",
                        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                        HorizontalOptions = LayoutOptions.Center
                    },
                    new BoxView {
                        Color = Color.Red,
                        WidthRequest = 200,
                        HeightRequest = 200,
                        HorizontalOptions = LayoutOptions.Center,
                        VerticalOptions = LayoutOptions.CenterAndExpand
                    }
                }
            }
        };
        var greenContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };
        var blueContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };

        Children.Add (redContentPage);
        Children.Add (greenContentPage);
        Children.Add (blueContentPage);
    }
}
```

每個[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)只會顯示[ `Label` ](xref:Xamarin.Forms.Label)特定的色彩和[ `BoxView` ](xref:Xamarin.Forms.BoxView)那種色彩。

> [!NOTE]
> [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)不支援 UI 虛擬化。 因此，效能可能會影響如果`CarouselPage`包含太多子項目。

如果[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)內嵌到[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)頁面[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)，則[ `MasterDetailPage.IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty)屬性應設為`false`避免筆勢之間發生的衝突`CarouselPage`而`MasterDetailPage`。

如需詳細資訊[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)，請參閱[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)Charles Petzold 的 Xamarin.Forms 的書籍。

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populating-a-carouselpage-with-a-template"></a>填入 CarouselPage 使用範本

下列 XAML 程式碼範例所示[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)藉由指派建構[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)至[ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)屬性傳回的頁面集合中的物件：

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <CarouselPage.ItemTemplate>
        <DataTemplate>
            <ContentPage>
                <ContentPage.Padding>
                  <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS, Android" Value="0,40,0,0" />
                  </OnPlatform>
                </ContentPage.Padding>
                <StackLayout>
                    <Label Text="{Binding Name}" FontSize="Medium" HorizontalOptions="Center" />
                    <BoxView Color="{Binding Color}" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
                </StackLayout>
            </ContentPage>
        </DataTemplate>
    </CarouselPage.ItemTemplate>
</CarouselPage>
```

[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)藉由設定資料填入[ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource)建構函式程式碼後置檔案中的屬性：

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

下列程式碼範例示範相當[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) C# 建立的：

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        ItemTemplate = new DataTemplate (() => {
            var nameLabel = new Label {
                FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                HorizontalOptions = LayoutOptions.Center
            };
            nameLabel.SetBinding (Label.TextProperty, "Name");

            var colorBoxView = new BoxView {
                WidthRequest = 200,
                HeightRequest = 200,
                HorizontalOptions = LayoutOptions.Center,
                VerticalOptions = LayoutOptions.CenterAndExpand
            };
            colorBoxView.SetBinding (BoxView.ColorProperty, "Color");

            return new ContentPage {
                Padding = padding,
                Content = new StackLayout {
                    Children = {
                        nameLabel,
                        colorBoxView
                    }
                }
            };
        });

        ItemsSource = ColorsDataModel.All;
    }
}
```

每個[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)只會顯示[ `Label` ](xref:Xamarin.Forms.Label)特定的色彩和[ `BoxView` ](xref:Xamarin.Forms.BoxView)那種色彩。

> [!NOTE]
> [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)不支援 UI 虛擬化。 因此，效能可能會影響如果`CarouselPage`包含太多子項目。

如果[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)內嵌到[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)頁面[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)，則[ `MasterDetailPage.IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty)屬性應設為`false`避免筆勢之間發生的衝突`CarouselPage`而`MasterDetailPage`。

如需詳細資訊[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)，請參閱[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)Charles Petzold 的 Xamarin.Forms 的書籍。

## <a name="summary"></a>總結

這篇文章示範如何使用[ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage)來瀏覽分頁的集合。 `CarouselPage`是使用者可以向側邊撥動可巡覽的內容，非常類似的資源庫頁面的頁面。


## <a name="related-links"></a>相關連結

- [頁面變化](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)
- [CarouselPageTemplate （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
