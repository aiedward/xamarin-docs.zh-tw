---
title: 浮動切換 頁面
description: Xamarin.Forms CarouselPage 是使用者可以撥動左右巡覽內容，例如主機庫頁面的頁面。 本文示範如何使用 CarouselPage 來瀏覽分頁的集合。
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 9259e2a85a7375106891eaae5fe22d6babfa2fcf
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846454"
---
# <a name="carousel-page"></a>浮動切換 頁面

_Xamarin.Forms CarouselPage 是使用者可以撥動左右巡覽內容，例如主機庫頁面的頁面。本文示範如何使用 CarouselPage 來瀏覽分頁的集合。_

## <a name="overview"></a>總覽

下列螢幕擷取畫面顯示[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)上每個平台：

![](carousel-page-images/thirdpage.png "CarouselPage Thid 項目")

配置[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)每個平台上都相同。 可以透過巡覽頁面，透過撥動由右至左瀏覽集合中，透過轉送及撥動左到右向後巡覽集合。 下列螢幕擷取畫面顯示中的第一頁[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)執行個體：

![](carousel-page-images/firstpage.png "CarouselPage 第一個項目")

下列螢幕擷取畫面所示撥動由右至左移到第二個頁面上:

![](carousel-page-images/secondpage.png "CarouselPage 第二個項目")

撥動從左到右回到前一個頁面時，一次從右到左撥動移動到第三個頁面上。

<!--
> [!NOTE]
> The [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) has been deprecated, and will be removed from Xamarin.Forms in a future release. Instead, the [`CarouselView`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselView/) should be used to provide a gallery-like view, where users can swipe from side to side to move through a collection of items.
-->

## <a name="creating-a-carouselpage"></a>建立 CarouselPage

兩種方法可以用來建立[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/):

- [填入](#Populating_a_CarouselPage_with_a_Page_Collection)`CarouselPage`的子集合[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)執行個體。
- [指派](#Populating_a_CarouselPage_with_a_Template)集合[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/)屬性並指派[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)至[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/)屬性，以傳回[`ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)集合中物件的執行個體。

這兩種方法，與`CarouselPage`接著將移至下一個頁面上顯示撥動互動會再顯示每一頁。 

> [!NOTE]
> A [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)才會填入與[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)執行個體，或`ContentPage`衍生項目。

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populating-a-carouselpage-with-a-page-collection"></a>填入 CarouselPage 頁面集合

下列 XAML 程式碼範例所示[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)會顯示三個[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)執行個體：

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

每個[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)只會顯示[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)特定色彩與[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)該色彩。

> [!NOTE]
> [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)不支援 UI 虛擬化。 因此，效能可能會受到影響如果`CarouselPage`包含太多子項目。

如果[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)內嵌至[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)頁面[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)、 [ `MasterDetailPage.IsGestureEnabled` ](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty/)屬性應該設定為`false`避免筆勢之間發生的衝突`CarouselPage`和`MasterDetailPage`。

如需有關[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)，請參閱[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)Charles Petzold Xamarin.Forms 活頁簿。

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populating-a-carouselpage-with-a-template"></a>填入 CarouselPage 使用範本

下列 XAML 程式碼範例所示[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)建構藉由指派[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)至[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/)屬性傳回的頁面集合中的物件：

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

[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)藉由設定填入資料[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/)建構函式的程式碼後置檔案中的屬性：

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

下列程式碼範例顯示的同等[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)以 C# 建立：

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

每個[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)只會顯示[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)特定色彩與[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)該色彩。

> [!NOTE]
> [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)不支援 UI 虛擬化。 因此，效能可能會受到影響如果`CarouselPage`包含太多子項目。

如果[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)內嵌至[ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/)頁面[ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)、 [ `MasterDetailPage.IsGestureEnabled` ](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty/)屬性應該設定為`false`避免筆勢之間發生的衝突`CarouselPage`和`MasterDetailPage`。

如需有關[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)，請參閱[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)Charles Petzold Xamarin.Forms 活頁簿。

## <a name="summary"></a>總結

本文示範如何使用[ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)來瀏覽分頁的集合。 `CarouselPage`是使用者可以撥動左右來瀏覽頁面的內容，非常類似的組件庫的頁面。


## <a name="related-links"></a>相關連結

- [頁面須](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)
- [CarouselPageTemplate （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/)
- [CarouselPage](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)
