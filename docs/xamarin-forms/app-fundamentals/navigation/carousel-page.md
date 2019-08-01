---
title: Xamarin.Forms 浮動切換頁面
description: Xamarin.Forms CarouselPage 可讓使用者如同使用資源庫，從頁面的一端撥動到另一端，以便巡覽內容頁面。 本文示範如何使用 CarouselPage 來巡覽頁面集合。
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: b96877045a3f289cb4b9ba83a8159d26ed768d99
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655600"
---
# <a name="xamarinforms-carousel-page"></a>Xamarin.Forms 浮動切換頁面

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_Xamarin.Forms CarouselPage 可讓使用者如同使用資源庫，從頁面的一端撥動到另一端，以便巡覽內容頁面。本文示範如何使用 CarouselPage 來巡覽頁面集合。_

## <a name="overview"></a>總覽

下列螢幕擷取畫面顯示每個平台上的 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)：

![](carousel-page-images/thirdpage.png "CarouselPage 第三個項目")

[`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 的配置在每個平台上都相同。 頁面可以藉由從右至左撥動以向前巡覽集合，並藉由從左至右撥動以向後巡覽集合。 下列螢幕擷取畫面顯示 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 執行個體的第一頁：

![](carousel-page-images/firstpage.png "CarouselPage 第一個項目")

從右至左撥動以前往第二個頁面，如下列螢幕擷取畫面所示：

![](carousel-page-images/secondpage.png "CarouselPage 第二個項目")

再次從右至左撥動可前往第三個頁面，而從左至右撥動會回到前一個頁面。

<!--
> [!NOTE]
> The [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) has been deprecated, and will be removed from Xamarin.Forms in a future release. Instead, the [`CarouselView`](xref:Xamarin.Forms.CarouselView) should be used to provide a gallery-like view, where users can swipe from side to side to move through a collection of items.
-->

## <a name="creating-a-carouselpage"></a>建立 CarouselPage

有兩種方法可用來建立 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)：

- 以 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體之子系的集合來[填入](#Populating_a_CarouselPage_with_a_Page_Collection) `CarouselPage`。
- 將集合[指派](#Populating_a_CarouselPage_with_a_Template)至 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 屬性，並將 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指派至 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 屬性，以傳回集合中物件的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體。

使用這兩種方法，`CarouselPage` 將會依序顯示每一頁，並顯示前往下一頁的撥動互動。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 只能透過 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體或 `ContentPage` 衍生項目來填入。

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populating-a-carouselpage-with-a-page-collection"></a>透過頁面集合填入 CarouselPage

下列 XAML 程式碼範例示範會顯示三個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體的 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)：

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

下列程式碼範例顯示 C# 中的對等 UI：

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

每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 只會顯示特定色彩的 [`Label`](xref:Xamarin.Forms.Label)，以及該色彩的 [`BoxView`](xref:Xamarin.Forms.BoxView)。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 不支援 UI 虛擬化。 因此，如果 `CarouselPage` 包含太多子項目，效能可能會受影響。

如果 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 內嵌到 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 的 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 頁面中，則 [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) 屬性應設為 `false` 以避免在 `CarouselPage` 和 `MasterDetailPage` 之間發生手勢衝突。

如需 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 的詳細資訊，請參閱 Charles Petzold 所著 Xamarin.Forms 一書的[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)。

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populating-a-carouselpage-with-a-template"></a>透過範本填入 CarouselPage

下列 XAML 程式碼範例示範 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)，其藉由將 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指派至 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 屬性以傳回集合中物件的頁面來建構：

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

[`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 會藉由在程式碼後置檔案的建構函式中設定 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 屬性來填入資料：

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

下列程式碼範例示範以 C# 建立的相等 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)：

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

每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 只會顯示特定色彩的 [`Label`](xref:Xamarin.Forms.Label)，以及該色彩的 [`BoxView`](xref:Xamarin.Forms.BoxView)。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 不支援 UI 虛擬化。 因此，如果 `CarouselPage` 包含太多子項目，效能可能會受影響。

如果 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 內嵌到 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 的 [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 頁面中，則 [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) 屬性應設為 `false` 以避免在 `CarouselPage` 和 `MasterDetailPage` 之間發生手勢衝突。

如需 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 的詳細資訊，請參閱 Charles Petzold 所著 Xamarin.Forms 一書的[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)。

## <a name="summary"></a>總結

本文示範如何使用 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 來巡覽頁面集合。 `CarouselPage` 可讓使用者如同使用資源庫，從頁面的一端撥動到另一端，以便巡覽內容頁面。


## <a name="related-links"></a>相關連結

- [頁面變化](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
