---
title: Xamarin.Forms 索引標籤式頁面
description: Xamarin.Forms TabbedPage 由索引標籤清單和較大的詳細資料區域所組成，每個索引標籤會將內容載入至詳細資料區域中。 本文示範如何使用 TabbedPage 來巡覽頁面集合。
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 85a6bce8a1021c75064ba06f3a5daf69b7fe3e57
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171374"
---
# <a name="xamarinforms-tabbed-page"></a>Xamarin.Forms 索引標籤式頁面

_Xamarin.Forms TabbedPage 由索引標籤清單和較大的詳細資料區域所組成，每個索引標籤會將內容載入至詳細資料區域中。本文示範如何使用 TabbedPage 來巡覽頁面集合。_

## <a name="overview"></a>總覽

下列螢幕擷取畫面顯示每個平台上的 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

![](tabbed-page-images/tab1.png "TabbedPage 範例")

下列螢幕擷取畫面聚焦於每個平台上的索引標籤格式：

![](tabbed-page-images/tabbedpage-components.png "TabbedPage 索引標籤元件")

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 的配置，以及其索引標籤都取決於平台：

- 在 iOS 上，索引標籤清單會出現在畫面的底部，詳細資料區域則出現在上方。 每個索引標籤都具有一個圖示影像，該影像為具有透明度的 PNG，其一般解析度應為 30x30，高解析度應為 60x60，iPhone 6 Plus 解析度則應為 90x90。 若有超過五個索引標籤，則會出現 [更多] 索引標籤，可用於存取其他索引標籤。 如需在 Xamarin.Forms 應用程式中載入影像的詳細資訊，請參閱[使用影像](~/xamarin-forms/user-interface/images.md)。 如需圖示需求的詳細資訊，請參閱[建立索引標籤式應用程式](~/ios/user-interface/controls/creating-tabbed-applications.md)。

  > [!NOTE]
  > 請注意，iOS 的 `TabbedRenderer` 具有可覆寫的 `GetIcon` 方法，可用於從指定來源載入索引標籤圖示。 此覆寫可使用 SVG 影像作為 `TabbedPage` 上的圖示。 此外，也可以提供圖示的選取及未選取版本。

- 在 Android 上，根據預設，索引標籤清單會出現在畫面的頂端，詳細資料區域則會出現在下方。 但是，索引標籤清單可使用平台特定移動到畫面底部。 如需詳細資訊，請參閱[設定 TabbedPage 工具列放置與色彩](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar)。

  > [!NOTE]
  > 請注意，在 Android 上使用 AppCompat 時，每個索引標籤也都會顯示圖示。 此外，Android AppCompat 的 `TabbedPageRenderer` 具有可覆寫的 `GetIconDrawable` 方法，可用於從自訂 `Drawable` 載入索引標籤圖示。 此覆寫可使用 SVG 影像作為 `TabbedPage` 上的圖示，且可使用頂端與底部索引標籤列。 或者，可覆寫的 `SetTabIcon` 方法能用來從自訂 `Drawable` 載入頂端索引標籤列的索引標籤圖示。

- 在 Windows 平板電腦板型規格上，索引標籤並非一律可見，且使用者必須向下撥動 (或在連接滑鼠的情況下以滑鼠右鍵按一下) 來檢視 `TabbedPage` 中的索引標籤 (如下所示)。

![](tabbed-page-images/windows-tabs.png "Windows 上的 TabbedPage 索引標籤")

## <a name="creating-a-tabbedpage"></a>建立 TabbedPage

有兩種方法可用來建立 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

- 使用子系 [`Page`](xref:Xamarin.Forms.Page) 物件集合 (例如 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體的集合) [填入](#Populating_a_TabbedPage_with_a_Page_Collection)[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)。
- 將集合[指派](#Populating_a_TabbedPage_with_a_Template)給 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 屬性，並將 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指派給 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 屬性，以傳回集合中物件的頁面。

使用這兩種方法，[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 即會在使用者選取每個索引標籤時顯示每個頁面。

> [!NOTE]
> 建議僅以 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 和 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體填入 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)。 這有助於跨所有平台確保一致的使用者體驗。

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>使用頁面集合填入 TabbedPage

下列 XAML 程式碼範例會顯示透過填入子系 [`Page`](xref:Xamarin.Forms.Page) 物件集合建構的 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" Icon="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

下列程式碼範例會顯示以 C# 建立的相同 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    var navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.Icon = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 會使用兩個子系 [`Page`](xref:Xamarin.Forms.Page) 物件填入。 第一個子系是 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體，第二個索引標籤則是 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)，其中包含一個 `ContentPage` 執行個體。

> [!NOTE]
> [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 不支援 UI 虛擬化。 因此，如果 `TabbedPage` 包含太多子項目，效能可能會受影響。

下列螢幕擷取畫面會顯示 `TodayPage`[`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體，該執行個體會顯示在 [Today] \(今日\) 索引標籤上：

![](tabbed-page-images/today-page.png "TabbedPage 中的 ContentPage")

選取 [Schedule] \(排程\) 索引標籤會顯示 `SchedulePage` [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體，該執行個體會包裝在 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 執行個體中，如下列螢幕擷取畫面所示：

![](tabbed-page-images/schedule-page.png "TabbedPage 中的 NavigationPage")

如需 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 配置的資訊，請參閱[執行導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

> [!NOTE]
> 雖然可以將 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 放到 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 內，但我們不建議將 `TabbedPage` 放到 `NavigationPage` 內。 這是因為在 iOS 上，`UITabBarController` 一律會作為 `UINavigationController` 的包裝函式。 如需詳細資訊，請參閱 iOS Developer Library 中的 [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (合併檢視控制器介面)。

#### <a name="navigation-inside-a-tab"></a>在索引標籤中巡覽

您可以從第二個索引標籤內，透過在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體的 [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) 屬性上叫用 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) 方法來執行導覽，如下列程式碼範例中所示範：

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

這會使 `UpcomingAppointmentsPage` 執行個體推送至導覽堆疊上，而變成使用中的頁面。 如下列螢幕擷取畫面所示：

![](tabbed-page-images/navigationpage.png "在索引標籤中巡覽")

如需使用 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 類別執行導覽的詳細資訊，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>使用範本填入 TabbedPage

下列 XAML 程式碼範例會顯示一個 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)，其建構方式是透過將 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 指派給 [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) 屬性，來傳回集合中物件的頁面：

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage">
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" Icon="monkeyicon.png">
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

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 會透過在程式碼後置檔案的建構函式中設定 [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) 屬性來進行填入：

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

下列程式碼範例會顯示以 C# 建立的相同 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)：

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() => {
      var nameLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage {
        Icon = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children = {
            nameLabel,
            image,
            new StackLayout {
              Padding = new Thickness (50, 10),
              Children = {
                new StackLayout {
                  Orientation = StackOrientation.Horizontal,
                  Children = {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                ...
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

每個索引標籤都會顯示一個 [`ContentPage`](xref:Xamarin.Forms.ContentPage)，其使用一系列的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 和 [`Label`](xref:Xamarin.Forms.Label) 執行個體來顯示該索引標籤的資料。下列螢幕擷取畫面會顯示 [Tamarin]  索引標籤的內容：

![](tabbed-page-images/tab3.png "使用範本填入 TabbedPage")

選取另一個索引標籤，然後顯示該索引標籤的內容。

> [!NOTE]
> [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 不支援 UI 虛擬化。 因此，如果 `TabbedPage` 包含太多子項目，效能可能會受影響。

如需 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 的詳細資訊，請參閱 Charles Petzold 所著 Xamarin.Forms 書籍的[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)。

## <a name="summary"></a>總結

本文示範如何使用 TabbedPage 來巡覽頁面集合。 Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 由索引標籤清單和較大的詳細資料區域所組成，每個索引標籤會將內容載入至詳細資料區域中。


## <a name="related-links"></a>相關連結

- [頁面變化](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (Sampls)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage (Sampls)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
