---
title: Xamarin.表格分頁
description: Xamarin.Forms TabbedPage 由索引標籤清單和較大的詳細資料區域所組成，每個索引標籤會將內容載入至詳細資料區域中。 本文示範如何使用 TabbedPage 來巡覽頁面集合。
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 986045a4be352da0e439de87fdc70e2958b48d36
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305084"
---
# <a name="xamarinforms-tabbedpage"></a>Xamarin.表格分頁

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

Xamarin.Forms[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)由選項卡清單和更大的詳細資訊區域組成,每個選項卡將內容載入到詳細資訊區域中。 以下屏幕截圖顯示了 iOS`TabbedPage`和安卓上的屏幕截圖:

[![在 iOS 和 Android 上包含三個選項卡的 TabbedPage 的螢幕截圖](tabbed-page-images/tabbedpage-today.png "帶三個選項卡的選項卡頁")](tabbed-page-images/tabbedpage-today-large.png#lightbox "帶三個選項卡的選項卡頁")

在 iOS 上，索引標籤清單會出現在畫面的底部，詳細資料區域則出現在上方。 每個選項卡由標題和圖示組成,該圖示應為具有 Alpha 通道的 PNG 檔。 在縱向方向中,選項卡欄圖示顯示在選項卡標題上方。 在橫向中,圖示和標題並排顯示。 此外,可能會顯示常規或緊湊的選項卡欄,具體取決於設備和方向。 若有超過五個索引標籤，則會出現 [更多]**** 索引標籤，可用於存取其他索引標籤。 有關圖示要求的資訊,請參閱developer.apple.com上的[選項卡欄圖示大小](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size)。

> [!TIP]
> for `TabbedRenderer` iOS 具有一`GetIcon`種可 重寫的方法,可用於從指定源載入選項卡圖示。 此覆寫可使用 SVG 影像作為 `TabbedPage` 上的圖示。 此外，也可以提供圖示的選取及未選取版本。

在 Android 上,選項卡清單顯示在螢幕頂部,詳細資訊區域如下。 每個選項卡由標題和圖示組成,該圖示應為具有 Alpha 通道的 PNG 檔。 但是,選項卡可以使用特定於平台的選項卡移動到螢幕底部。 如果選項卡超過五個,並且選項卡列表位於螢幕底部,則會出現可用於訪問其他選項卡的 *「更多*」選項卡。 有關圖示要求的資訊,請參閱material.io[上的選項卡](https://material.io/components/tabs/#),並支援developer.android.com[上的不同圖元密度](https://developer.android.com/training/multiscreen/screendensities)。 有關將選項卡移至螢幕底部的資訊,請參考[設定 TabbedPage 工具列來放置與顏色](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md)。

> [!TIP]
> 為`TabbedPageRenderer`Android AppCompat 有`GetIconDrawable`一個可 重寫的方法,可`Drawable`用於從自訂 載入選項卡圖示。 此覆寫可使用 SVG 影像作為 `TabbedPage` 上的圖示，且可使用頂端與底部索引標籤列。 或者，可覆寫的 `SetTabIcon` 方法能用來從自訂 `Drawable` 載入頂端索引標籤列的索引標籤圖示。

在通用 Windows 平臺 (UWP) 上,選項卡清單顯示在螢幕頂部,詳細資訊區域如下。 每個選項卡由標題組成。 但是,圖示可以添加到每個選項卡與平臺特定的。 有關詳細資訊,請參閱[Windows 上的 TabbedPage 圖示](~/xamarin-forms/platform/windows/tabbedpage-icons.md)。

## <a name="create-a-tabbedpage"></a>建立 TabbedPage

兩種方法可建立 : [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)

- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)使用子[`Page`](xref:Xamarin.Forms.Page)物件的集合填[`ContentPage`](xref:Xamarin.Forms.ContentPage)充 ,例如物件集合。 有關詳細資訊,請參閱[使用頁面集合填充 TabbedPage。](#populate-a-tabbedpage-with-a-page-collection)
- 將集合指派給 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 屬性，並將 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指派給 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 屬性，以傳回集合中物件的頁面。 有關詳細資訊,請參閱[使用範本填充 TabbedPage。](#populate-a-tabbedpage-with-a-template)

使用這兩種方法,當用戶[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)選擇每個選項卡時,將顯示每個頁面。

> [!IMPORTANT]
> 建議只有填充[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)與[`ContentPage`](xref:Xamarin.Forms.ContentPage)[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)實體 。 這有助於跨所有平台確保一致的使用者體驗。

此外,[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)定義以下屬性:

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor)的類型[`Color`](xref:Xamarin.Forms.Color),選項卡欄的背景顏色。
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor)類型[`Color`](xref:Xamarin.Forms.Color),選項卡欄上的文字顏色。
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor)類型[`Color`](xref:Xamarin.Forms.Color),選擇選項卡時的顏色。
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor)的類型[`Color`](xref:Xamarin.Forms.Color),未選擇選項卡時的標籤的顏色。

所有這些屬性都由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件支援,這意味著它們可以設置樣式,並且這些屬性可以是數據綁定的目標。

> [!WARNING]
> 在[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)中[`Page`](xref:Xamarin.Forms.Page), 每個物件在`TabbedPage`建構 時建立。 這可能導致用戶體驗不佳,特別是如果`TabbedPage`是 應用程式的根頁。 但是,Xamarin.Forms Shell 允許按需創建通過選項卡欄訪問的頁面,以響應導航。 有關詳細資訊,請參閱[Xamarin.Forms 外殼](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>使用頁面集合填充 TabbedPage

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)可以使用子[`Page`](xref:Xamarin.Forms.Page)物件的集合(如物件集合[`ContentPage`](xref:Xamarin.Forms.ContentPage)) 填充。 這是透過物件`Page`加入到集合來實現的[`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*)。 這在 XAML 中完成如下:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" IconImageSource="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

> [!NOTE]
> [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*)衍生的[`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1)[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)類別屬性是`ContentProperty``MultiPage<T>` 因此,在 XAML 中,不必顯[`Page`](xref:Xamarin.Forms.Page)式將 物件分配`Children`給屬性。

對等的 C# 程式碼為：

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    NavigationPage navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.IconImageSource = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

這個選項, 使用兩[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)[`Page`](xref:Xamarin.Forms.ContentPage)個物件填滿 。 第一個子級[`ContentPage`](xref:Xamarin.Forms.ContentPage)是物件,第二個子[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)級`ContentPage`是包含 物件的。

以下螢幕截圖顯示了[`ContentPage`](xref:Xamarin.Forms.ContentPage)[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)的 物件:

[![在 iOS 和 Android 上包含三個選項卡的 TabbedPage 的螢幕截圖](tabbed-page-images/tabbedpage-today.png "帶三個選項卡的選項卡頁")](tabbed-page-images/tabbedpage-today-large.png#lightbox "帶三個選項卡的選項卡頁")

選擇另一個選項卡[`ContentPage`](xref:Xamarin.Forms.ContentPage)將顯示表示選項卡的物件:

[![iOS 和 Android 上包含選項卡的 TabbedPage 的螢幕截圖](tabbed-page-images/tabbedpage-week.png "帶選項卡的選項卡頁")](tabbed-page-images/tabbedpage-week-large.png#lightbox "帶選項卡的選項卡頁")

在「**計劃」** 選項卡[`ContentPage`](xref:Xamarin.Forms.ContentPage)上 ,物件將[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)包裹在 物件中。

> [!WARNING]
> 雖然[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)可以放置在[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)中 ,但不建議`TabbedPage``NavigationPage`將 。 這是因為在 iOS 上，`UITabBarController` 一律會作為 `UINavigationController` 的包裝函式。 如需詳細資訊，請參閱 iOS Developer Library 中的 [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (合併檢視控制器介面)。

## <a name="navigate-within-a-tab"></a>在選項卡內導覽

導航可以在選項卡中執行,前提是[`ContentPage`](xref:Xamarin.Forms.ContentPage)物件包裝[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)在 物件中。 這是透過在物件[`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*)[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)的屬性上調用方法來實現的[`ContentPage`](xref:Xamarin.Forms.ContentPage):

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

要導航到的頁面被指定為方法的[`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*)參數。 在此範例中,`UpcomingAppointmentsPage`頁面推送到導覽堆疊上,該堆疊將成為活動頁:

[![在 iOS 和 Android 上選項卡內導航的螢幕截圖](tabbed-page-images/tabbedpage-upcoming.png "選項卡的選項卡頁導覽")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "選項卡的選項卡頁導覽")

有關使用 類別的資訊,[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)請參考[分層導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="populate-a-tabbedpage-with-a-template"></a>使用樣本填充選項卡式頁面

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)可以通過[`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource)為 屬性分配數據集合,以及將[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)分配 為 物件[`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)[`Page`](xref:Xamarin.Forms.Page)範本化數據 的屬性來填充頁。 這在 XAML 中完成如下:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage"
            ItemsSource="{x:Static local:MonkeyDataModel.All}">            
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" IconImageSource="monkeyicon.png">
        <StackLayout Padding="5, 25">
          <Label Text="{Binding Name}" Font="Bold,Large" HorizontalOptions="Center" />
          <Image Source="{Binding PhotoUrl}" WidthRequest="200" HeightRequest="200" />
          <StackLayout Padding="50, 10">
            <StackLayout Orientation="Horizontal">
              <Label Text="Family:" HorizontalOptions="FillAndExpand" />
              <Label Text="{Binding Family}" Font="Bold,Medium" />
            </StackLayout>
            ...
          </StackLayout>
        </StackLayout>
      </ContentPage>
    </DataTemplate>
  </TabbedPage.ItemTemplate>
</TabbedPage>
```

對等的 C# 程式碼為：

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() =>
    {
      var nameLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage
      {
        IconImageSource = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children =
          {
            nameLabel,
            image,
            new StackLayout
            {
              Padding = new Thickness (50, 10),
              Children =
              {
                new StackLayout
                {
                  Orientation = StackOrientation.Horizontal,
                  Children =
                  {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                // ...
              }
            }
          }
        }
      };
      contentPage.SetBinding (TitleProperty, "Name");
      return contentPage;
    });
    ItemsSource = MonkeyDataModel.All;
  }
}
```

此選項,每個選項卡由一個[`ContentPage`](xref:Xamarin.Forms.ContentPage)物件組成,該物件[`Image`](xref:Xamarin.Forms.Image)使用[`Label`](xref:Xamarin.Forms.Label)與物件來顯示選項卡的資料:

[![iOS 和 Android 上樣本化 TabbedPage 的螢幕截圖](tabbed-page-images/tabbedpage-template.png "樣本化選項卡頁")](tabbed-page-images/tabbedpage-template-large.png#lightbox "樣本化選項卡頁")

選擇另一個選項卡[`ContentPage`](xref:Xamarin.Forms.ContentPage)將顯示表示選項卡的物件。

## <a name="related-links"></a>相關連結

- [TabbedPageWithNavigationPage (Sampls)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (Sampls)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [頁面品種](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [選項卡頁 API](xref:Xamarin.Forms.TabbedPage)
