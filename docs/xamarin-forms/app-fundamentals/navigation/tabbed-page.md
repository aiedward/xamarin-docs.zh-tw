---
title: Xamarin.FormsTabbedPage
description: Xamarin.FormsTabbedPage 是由索引標籤清單和較大的詳細資料區域所組成，每個索引標籤都會將內容載入至詳細資料區域。 本文示範如何使用 TabbedPage 來巡覽頁面集合。
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 38389867ba52e63d8310e3b59d7838f58e8cf488
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137510"
---
# <a name="xamarinforms-tabbedpage"></a>Xamarin.FormsTabbedPage

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

包含索引標籤 Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 清單和較大的詳細資料區域，每個索引標籤都會將內容載入至詳細資料區域。 下列螢幕擷取畫面顯示 `TabbedPage` iOS 和 Android 上的：

[![TabbedPage 的螢幕擷取畫面，其中包含三個索引標籤（在 iOS 和 Android 上）](tabbed-page-images/tabbedpage-today.png "具有三個索引標籤的 TabbedPage")](tabbed-page-images/tabbedpage-today-large.png#lightbox "具有三個索引標籤的 TabbedPage")

在 iOS 上，索引標籤清單會出現在畫面的底部，詳細資料區域則出現在上方。 每個索引標籤都是由一個標題和一個圖示組成，這應該是具有 Alpha 色板的 PNG 檔案。 在直向方向中，索引標籤列圖示會出現在索引標籤標題上方。 在橫向，圖示和標題並排顯示。 此外，視裝置和方向而定，可能會顯示一般或精簡索引標籤欄。 若有超過五個索引標籤，則會出現 [更多]**** 索引標籤，可用於存取其他索引標籤。 如需圖示需求的相關資訊，請參閱 developer.apple.com 上的索引標籤[欄圖示大小](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size)。

> [!TIP]
> `TabbedRenderer`IOS 的具有可覆寫的 `GetIcon` 方法，可以用來從指定的來源載入索引標籤圖示。 此覆寫可使用 SVG 影像作為 `TabbedPage` 上的圖示。 此外，也可以提供圖示的選取及未選取版本。

在 Android 上，索引標籤清單會出現在畫面頂端，而詳細資料區域如下。 每個索引標籤都是由一個標題和一個圖示組成，這應該是具有 Alpha 色板的 PNG 檔案。 不過，您可以使用平臺特定，將索引標籤移至畫面底部。 如果有五個以上的索引標籤，而且索引標籤清單位於畫面底部，則會出現 [*更多*] 索引標籤，可用來存取其他索引標籤。 如需圖示需求的相關資訊，[請參閱 material.io 上的索引](https://material.io/components/tabs/#)標籤和在 Developer.android.com 上[支援不同的圖元密度](https://developer.android.com/training/multiscreen/screendensities)。 如需將索引標籤移到畫面底部的詳細資訊，請參閱[設定 TabbedPage 工具列位置和色彩](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md)。

> [!TIP]
> `TabbedPageRenderer`適用于 Android AppCompat 的可覆寫 `GetIconDrawable` 方法，可以用來從自訂載入索引標籤圖示 `Drawable` 。 此覆寫可使用 SVG 影像作為 `TabbedPage` 上的圖示，且可使用頂端與底部索引標籤列。 或者，可覆寫的 `SetTabIcon` 方法能用來從自訂 `Drawable` 載入頂端索引標籤列的索引標籤圖示。

在 [通用 Windows 平臺（UWP）上，索引標籤清單會出現在畫面頂端，而 [詳細資料] 區域如下所示。 每個索引標籤都是由一個標題所組成。 不過，您可以將圖示新增至具有平臺特定的每個索引標籤。 如需詳細資訊，請參閱[TabbedPage Windows 上的圖示](~/xamarin-forms/platform/windows/tabbedpage-icons.md)。

## <a name="create-a-tabbedpage"></a>建立 TabbedPage

有兩種方法可以用來建立 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ：

- 在中填入 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 子物件的集合 [`Page`](xref:Xamarin.Forms.Page) ，例如物件的集合 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 如需詳細資訊，請參閱在[TabbedPage 中填入頁面集合](#populate-a-tabbedpage-with-a-page-collection)。
- 將集合指派給 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 屬性，並將 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指派給 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 屬性，以傳回集合中物件的頁面。 如需詳細資訊，請參閱[使用範本填入 TabbedPage](#populate-a-tabbedpage-with-a-template)。

使用這兩種方法時， [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 將會在使用者選取每個索引標籤時顯示每個頁面。

> [!IMPORTANT]
> 建議您 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 只填入和 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 實例。 這有助於跨所有平台確保一致的使用者體驗。

此外，會 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 定義下列屬性：

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor)，屬於類型 [`Color`](xref:Xamarin.Forms.Color) ，索引標籤列的背景色彩。
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor)，屬於類型 [`Color`](xref:Xamarin.Forms.Color) ，索引標籤欄的文字色彩。
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor)，屬於類型 [`Color`](xref:Xamarin.Forms.Color) ，選取此選項時的索引標籤色彩。
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor)，屬於類型，這是未選取的索引卷 [`Color`](xref:Xamarin.Forms.Color) 標色彩。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以樣式化，而屬性可以是資料系結的目標。

> [!WARNING]
> 在中 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ，每個 [`Page`](xref:Xamarin.Forms.Page) 物件都是在結構化時建立的 `TabbedPage` 。 這可能會導致使用者體驗不佳，特別是如果 `TabbedPage` 是應用程式的根頁面。 不過，Shell 可讓您依需求建立透過索引標籤列 Xamarin.Forms 來存取的頁面，以回應導覽。 如需詳細資訊，請參閱[ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>在 TabbedPage 中填入頁面集合

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)可以填入子物件的集合 [`Page`](xref:Xamarin.Forms.Page) ，例如物件的集合 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 這是藉由將 `Page` 物件新增至集合來達成 [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*) 。 這會在 XAML 中完成，如下所示：

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
> [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*)衍生的類別的屬性 [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1) [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 是 `ContentProperty` 的 `MultiPage<T>` 。 因此，在 XAML 中，不需要明確地將 [`Page`](xref:Xamarin.Forms.Page) 物件指派給 `Children` 屬性。

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

在此範例中， [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 會以兩個 [`Page`](xref:Xamarin.Forms.ContentPage) 物件填入。 第一個子系是 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件，而第二個子系是 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 包含 `ContentPage` 物件的。

下列螢幕擷取畫面顯示 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 中的物件 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ：

[![TabbedPage 的螢幕擷取畫面，其中包含三個索引標籤（在 iOS 和 Android 上）](tabbed-page-images/tabbedpage-today.png "具有三個索引標籤的 TabbedPage")](tabbed-page-images/tabbedpage-today-large.png#lightbox "具有三個索引標籤的 TabbedPage")

選取另一個索引標籤會顯示代表索引標籤的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件：

[![IOS 和 Android 上包含索引標籤的 TabbedPage 螢幕擷取畫面](tabbed-page-images/tabbedpage-week.png "具有索引標籤的 TabbedPage")](tabbed-page-images/tabbedpage-week-large.png#lightbox "具有索引標籤的 TabbedPage")

在 [**排程**] 索引標籤上， [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件會包裝在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 物件中。

> [!WARNING]
> 雖然 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 可以放在中 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ，但不建議將放 `TabbedPage` 入中 `NavigationPage` 。 這是因為在 iOS 上，`UITabBarController` 一律會作為 `UINavigationController` 的包裝函式。 如需詳細資訊，請參閱 iOS Developer Library 中的 [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (合併檢視控制器介面)。

## <a name="navigate-within-a-tab"></a>在索引標籤內流覽

導覽可以在索引標籤中執行，前提 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 是物件會包裝在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 物件中。 這是藉由在 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 物件的屬性上叫用方法來完成的 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

所流覽至的頁面會指定為方法的引數 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 。 在此範例中， `UpcomingAppointmentsPage` 頁面會被推送到導覽堆疊上，而它會變成使用中的頁面：

[![在 iOS 和 Android 上的索引標籤內導覽的螢幕擷取畫面](tabbed-page-images/tabbedpage-upcoming.png "TabbedPage 索引標籤中的導覽")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "TabbedPage 索引標籤中的導覽")

如需使用類別執行導覽的詳細資訊 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="populate-a-tabbedpage-with-a-template"></a>使用範本填入 TabbedPage

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)可以藉由將資料的集合指派給屬性來填入頁面 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) ，並將指派 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 給將 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 資料當做物件範本的屬性 [`Page`](xref:Xamarin.Forms.Page) 。 這會在 XAML 中完成，如下所示：

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

在此範例中，每個索引標籤都包含一個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件，該物件會使用 [`Image`](xref:Xamarin.Forms.Image) 和 [`Label`](xref:Xamarin.Forms.Label) 物件來顯示索引標籤的資料：

[![IOS 和 Android 上樣板化 TabbedPage 的螢幕擷取畫面](tabbed-page-images/tabbedpage-template.png "樣板化 TabbedPage")](tabbed-page-images/tabbedpage-template-large.png#lightbox "樣板化 TabbedPage")

選取另一個索引標籤會顯示代表索引標籤的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件。

## <a name="related-links"></a>相關連結

- [TabbedPageWithNavigationPage (Sampls)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (Sampls)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [頁面種類](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPage API](xref:Xamarin.Forms.TabbedPage)
