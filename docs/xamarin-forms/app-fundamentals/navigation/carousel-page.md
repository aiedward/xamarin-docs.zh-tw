---
title: Xamarin.Forms 浮動切換頁面
description: Xamarin.FormsCarouselPage 是使用者可以從側邊滑到一邊以流覽內容頁面的頁面，例如資源庫。 本文示範如何使用 CarouselPage 來巡覽頁面集合。
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0110fa7c348c4facaf60cd261c8a584904aa0b8e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563064"
---
# <a name="no-locxamarinforms-carousel-page"></a>Xamarin.Forms 浮動切換頁面

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_Xamarin.FormsCarouselPage 是使用者可以從側邊滑到一邊以流覽內容頁面的頁面，例如資源庫。本文示範如何使用 CarouselPage 來導覽頁面的集合。_

> [!IMPORTANT]
> 已 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 由取代 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，可提供可滾動的版面配置，讓使用者可以在專案集合中滑動以移動。 如需的詳細資訊 `CarouselView` ，請參閱[ Xamarin.Forms CarouselView](~/xamarin-forms/user-interface/carouselview/index.md)。

下列螢幕擷取畫面顯示 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 每個平臺上的：

![CarouselPage 第三個項目](carousel-page-images/thirdpage.png)

的配置 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 在每個平臺上都相同。 頁面可以藉由從右至左撥動以向前巡覽集合，並藉由從左至右撥動以向後巡覽集合。 下列螢幕擷取畫面顯示實例中的第一頁 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) ：

![CarouselPage 第一個項目](carousel-page-images/firstpage.png)

從右至左撥動以前往第二個頁面，如下列螢幕擷取畫面所示：

![CarouselPage 第二個項目](carousel-page-images/secondpage.png)

再次從右至左撥動可前往第三個頁面，而從左至右撥動會回到前一個頁面。

> [!NOTE]
> 不 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 支援 UI 虛擬化。 因此，如果 `CarouselPage` 包含太多子項目，效能可能會受影響。

如果內嵌在的 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) 頁面 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 中，則 [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) 應該將屬性設定為， `false` 以防止和之間的手勢衝突 `CarouselPage` `MasterDetailPage` 。

如需的詳細資訊 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) ，請參閱 Charles Petzold 本書的第 [25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) Xamarin.Forms 。

## <a name="create-a-carouselpage"></a>建立 CarouselPage

有兩種方法可用來建立 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) ：

- 以 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體之子系的集合來[填入](#populate-a-carouselpage-with-a-page-collection)`CarouselPage`。
- 將集合[指派](#populate-a-carouselpage-with-a-template)至 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 屬性，並將 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指派至 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 屬性，以傳回集合中物件的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體。

使用這兩種方法，`CarouselPage` 將會依序顯示每一頁，並顯示前往下一頁的撥動互動。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)只能填入 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 實例或 `ContentPage` 衍生。

### <a name="populate-a-carouselpage-with-a-page-collection"></a>以頁面集合填入 CarouselPage

下列 XAML 程式碼範例顯示 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 會顯示三個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 實例的：

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

每個都 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 只 [`Label`](xref:Xamarin.Forms.Label) 會顯示特定色彩的，以及 [`BoxView`](xref:Xamarin.Forms.BoxView) 該色彩的。

### <a name="populate-a-carouselpage-with-a-template"></a>以範本填入 CarouselPage

下列 XAML 程式碼範例示範 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 如何將指派 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 給 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 屬性，以傳回集合中物件的頁面：

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

[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)會 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 在程式碼後端檔案的函式中設定屬性，以填入資料：

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

下列程式碼範例顯示 [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 在 c # 中建立的對等專案：

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

每個都 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 只 [`Label`](xref:Xamarin.Forms.Label) 會顯示特定色彩的，以及 [`BoxView`](xref:Xamarin.Forms.BoxView) 該色彩的。

## <a name="related-links"></a>相關連結

- [頁面種類](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (Samples)](/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate (Samples)](/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)