---
title: Xamarin.Forms 浮動切換頁面
description: Xamarin.Forms CarouselPage 可讓使用者如同使用資源庫，從頁面的一端撥動到另一端，以便巡覽內容頁面。 本文示範如何使用 CarouselPage 來巡覽頁面集合。
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 5cfaeb8589514972dfcccd11009a02c00f95bfac
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "72696434"
---
# <a name="xamarinforms-carousel-page"></a>Xamarin.Forms 浮動切換頁面

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_Xamarin.Forms 旋轉木馬頁面是一個頁面,用戶可以從一邊滑動到一邊流覽內容頁面,如圖庫。本文演示如何使用旋轉木馬頁面瀏覽頁面集合。_

> [!IMPORTANT]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)已被取代,[`CarouselView`](xref:Xamarin.Forms.CarouselView)它提供了一個可滾動的佈局,用戶可以在其中輕掃以瀏覽專案集合。 有關的詳細資訊,`CarouselView`請參閱[Xamarin.Forms 旋轉木馬視圖](~/xamarin-forms/user-interface/carouselview/index.md)。

以下螢幕截圖在每個平台上顯示一[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)個:

![](carousel-page-images/thirdpage.png "CarouselPage Third Item")

在每個平臺上的[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)佈局是相同的。 頁面可以藉由從右至左撥動以向前巡覽集合，並藉由從左至右撥動以向後巡覽集合。 以下螢幕截圖顯示了實體中第一頁[`CarouselPage`](xref:Xamarin.Forms.CarouselPage):

![](carousel-page-images/firstpage.png "CarouselPage First Item")

從右至左撥動以前往第二個頁面，如下列螢幕擷取畫面所示：

![](carousel-page-images/secondpage.png "CarouselPage Second Item")

再次從右至左撥動可前往第三個頁面，而從左至右撥動會回到前一個頁面。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)不支援 UI 虛擬化。 因此，如果 `CarouselPage` 包含太多子項目，效能可能會受影響。

[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)如果 嵌入到[`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)的[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)頁中[`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty),則應`false`將`CarouselPage`屬性設置為 以`MasterDetailPage`防止 和之間的手勢衝突。

有關的更多資訊,[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)請參閱查理斯·佩佐德的《Xamarin.Forms》一書的第[25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)。

## <a name="create-a-carouselpage"></a>建立 CarouselPage

兩種方法可建立 : [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)

- 以 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體之子系的集合來[填入](#Populating_a_CarouselPage_with_a_Page_Collection)`CarouselPage`。
- 將集合[指派](#Populating_a_CarouselPage_with_a_Template)至 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 屬性，並將 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指派至 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 屬性，以傳回集合中物件的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體。

使用這兩種方法，`CarouselPage` 將會依序顯示每一頁，並顯示前往下一頁的撥動互動。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)只能填充[`ContentPage`](xref:Xamarin.Forms.ContentPage)實例`ContentPage`或導數。

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populate-a-carouselpage-with-a-page-collection"></a>以頁面集合填入 CarouselPage

下面的 XAML 代碼範例[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)顯示了[`ContentPage`](xref:Xamarin.Forms.ContentPage)顯示三個 實體的 實體:

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

每個[`ContentPage`](xref:Xamarin.Forms.ContentPage)只是顯示[`Label`](xref:Xamarin.Forms.Label)一個特定顏色和[`BoxView`](xref:Xamarin.Forms.BoxView)該 顏色。

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populate-a-carouselpage-with-a-template"></a>以範本填入 CarouselPage

下面的 XAML 代碼範例[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)顯示了 透過[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)[`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)設定給 屬性來傳回集合中物件的頁面而建構的:

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

[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)以在代碼背後的檔案建構函數中設定[`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource)屬性來填充資料:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

以下代碼範例顯示 C# 中建立的等效項目[`CarouselPage`](xref:Xamarin.Forms.CarouselPage):

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

每個[`ContentPage`](xref:Xamarin.Forms.ContentPage)只是顯示[`Label`](xref:Xamarin.Forms.Label)一個特定顏色和[`BoxView`](xref:Xamarin.Forms.BoxView)該 顏色。

## <a name="related-links"></a>相關連結

- [頁面品種](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
