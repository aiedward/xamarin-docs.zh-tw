---
title: Xamarin.Forms 索引標籤式的頁面
description: Xamarin.Forms TabbedPage 清單所組成的索引標籤和較大的 [詳細資料] 區域中，使用內容載入詳細資料區域的每個索引標籤。 這篇文章會示範如何使用 TabbedPage 瀏覽分頁的集合。
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 3eb978780222da2050fc91dfa41c68ef4bd3b6f4
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996291"
---
# <a name="xamarinforms-tabbed-page"></a>Xamarin.Forms 索引標籤式的頁面

_Xamarin.Forms TabbedPage 清單所組成的索引標籤和較大的 [詳細資料] 區域中，使用內容載入詳細資料區域的每個索引標籤。這篇文章會示範如何使用 TabbedPage 瀏覽分頁的集合。_

## <a name="overview"></a>總覽

下列螢幕擷取畫面所示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)每個平台：

![](tabbed-page-images/tab1.png "TabbedPage 範例")

下列螢幕擷取畫面會著重在每個平台上的索引標籤格式：

![](tabbed-page-images/tabbedpage-components.png "TabbedPage 索引標籤上的元件")

版面配置[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，和其索引標籤中，視平台而定：

- 在 iOS 上，索引標籤的清單會顯示螢幕的底部，在詳細資料區域是上方。 每個索引標籤也會有圖示影像應該是 x 30 png，並且具有一般解析的透明度 30、 60 x 60 的高解析度和 90x90 適用於 iPhone 6 Plus 解析。 如果有五個以上的索引標籤*更多* 索引標籤會出現，這可用來存取其他索引標籤。 如需有關載入 Xamarin.Forms 應用程式中的映像的詳細資訊，請參閱[處理映像](~/xamarin-forms/user-interface/images.md)。 如需圖示需求的詳細資訊，請參閱[建立索引標籤式應用程式](~/ios/user-interface/controls/creating-tabbed-applications.md)。

    > [!NOTE]
  > 請注意， `TabbedRenderer` iOS 具有可覆寫`GetIcon`方法，可用來載入指定之來源的索引標籤圖示。 此覆寫可讓您使用 SVG 影像與圖示上`TabbedPage`。 此外，您可以提供圖示選取或未選取版本。

- 在 Android 上，依預設，索引標籤的清單會出現在畫面頂端，並在詳細資料區域如下。 不過，索引標籤清單可以移動與平台特定畫面的底部。 如需詳細資訊，請參閱 <<c0> [ 設定 TabbedPage 工具列位置和色彩](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar)。

    > [!NOTE]
  > 請注意，當使用 AppCompat 在 Android 上，每個索引標籤也會顯示圖示。 颾魤 ㄛ `TabbedPageRenderer` Android AppCompat 具有可覆寫`SetTabIcon`方法，可用來從自訂載入索引標籤圖示`Drawable`。 此覆寫可讓您使用 SVG 影像與圖示上`TabbedPage`。

- Windows 平板電腦-尺寸，在索引標籤不會永遠顯示，而使用者必須向撥動向下 （或按一下滑鼠右鍵，如果它們有附加的滑鼠），檢視中的索引標籤`TabbedPage`（如下所示）。

![](tabbed-page-images/windows-tabs.png "在 Windows 上的 TabbedPage 索引標籤")

## <a name="creating-a-tabbedpage"></a>建立 TabbedPage

兩種方法可用來建立[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

- [填入](#Populating_a_TabbedPage_with_a_Page_Collection) [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)的子集合[ `Page` ](xref:Xamarin.Forms.Page)物件，例如集合[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)執行個體。
- [指派](#Populating_a_TabbedPage_with_a_Template)集合[ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource)屬性，並將指派[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)至[ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)屬性傳回的頁面集合中的物件。

這兩種方法，與[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)會在使用者選取的每個索引標籤，顯示每個頁面。

> [!NOTE]
> 建議[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)應該填入[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)並[ `ContentPage`](xref:Xamarin.Forms.ContentPage)只執行個體。 這有助於確保一致的使用者體驗，跨所有平台。

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>填入 TabbedPage 頁面集合

下列 XAML 程式碼範例所示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)所填入的子集合建構[ `Page` ](xref:Xamarin.Forms.Page)物件：

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

下列程式碼範例示範相當[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) C# 建立的：

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

[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)會填入這兩個子[ `Page` ](xref:Xamarin.Forms.Page)物件。 第一個子系[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)執行個體，，而第二個索引標籤[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)包含`ContentPage`執行個體。

> [!NOTE]
> [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)不支援 UI 虛擬化。 因此，效能可能會影響如果`TabbedPage`包含太多子項目。

下列螢幕擷取畫面所示`TodayPage` [`ContentPage`](xref:Xamarin.Forms.ContentPage)執行個體，會顯示*今天*索引標籤：

![](tabbed-page-images/today-page.png "在 TabbedPage ContentPage")

選取*排程*索引標籤會顯示`SchedulePage` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)執行個體，這會包裝在[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)執行個體，並顯示下列螢幕擷取畫面：

![](tabbed-page-images/schedule-page.png "在 TabbedPage NavigationPage")

如需有關配置的資訊[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，請參閱[執行瀏覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

> [!NOTE]
> 雖然接受放[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)成[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，不建議將`TabbedPage`到`NavigationPage`。 這是因為在 iOS 上，`UITabBarController`一律可做為包裝函式`UINavigationController`。 如需詳細資訊，請參閱 <<c0> [ 結合的檢視控制器介面](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html)iOS Developer Library 中。

#### <a name="navigation-inside-a-tab"></a>瀏覽 索引標籤內

瀏覽可以執行的第二個索引標籤中，藉由叫用[ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*)方法[ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation)屬性[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)執行個體，下列程式碼範例所示：

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

這會使 `UpcomingAppointmentsPage` 執行個體推送至導覽堆疊上，而變成使用中的頁面。 這是由下列的螢幕擷取畫面所示：

![](tabbed-page-images/navigationpage.png "瀏覽 索引標籤內")

如需有關執行使用瀏覽[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)類別，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>填入 TabbedPage 使用範本

下列 XAML 程式碼範例所示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)藉由指派建構[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)至[ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)屬性傳回的頁面集合中的物件：

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

[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)藉由設定資料填入[ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource)建構函式程式碼後置檔案中的屬性：

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

下列程式碼範例示範相當[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) C# 建立的：

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

每個索引標籤會顯示[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)使用的一系列[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)並[ `Label` ](xref:Xamarin.Forms.Label)執行個體，以顯示索引標籤的資料。下列螢幕擷取畫面顯示的內容*Tamarin* 索引標籤：

![](tabbed-page-images/tab3.png "填入 TabbedPage 使用範本")

然後選取另一個索引標籤會顯示該索引標籤上的內容。

> [!NOTE]
> [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)不支援 UI 虛擬化。 因此，效能可能會影響如果`TabbedPage`包含太多子項目。

如需詳細資訊[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)，請參閱[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)Charles Petzold 的 Xamarin.Forms 的書籍。

## <a name="summary"></a>總結

本文示範如何使用 TabbedPage 瀏覽分頁的集合。 Xamarin.Forms [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)清單所組成的索引標籤和較大的 [詳細資料] 區域中，使用內容載入詳細資料區域的每個索引標籤。


## <a name="related-links"></a>相關連結

- [頁面變化](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
