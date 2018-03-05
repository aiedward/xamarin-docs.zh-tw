---
title: "索引標籤式的頁面"
description: "Xamarin.Forms TabbedPage 清單所組成的索引標籤和較大的詳細資料區域與詳細資料區域將內容載入每個索引標籤。 本文示範如何使用 TabbedPage 來瀏覽分頁的集合。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 97ca114f1160168c7fd9439e31dc475bc37b0467
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="tabbed-page"></a>索引標籤式的頁面

_Xamarin.Forms TabbedPage 清單所組成的索引標籤和較大的詳細資料區域與詳細資料區域將內容載入每個索引標籤。本文示範如何使用 TabbedPage 來瀏覽分頁的集合。_

## <a name="overview"></a>總覽

下列螢幕擷取畫面顯示[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)上每個平台：

![](tabbed-page-images/tab1.png "TabbedPage 範例")

下列螢幕擷取畫面焦點放在每個平台上的索引標籤格式：

![](tabbed-page-images/tabbedpage-components.png "TabbedPage 索引標籤上的元件")

配置[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)，和其索引標籤上，取決於平台：

- 在 iOS 上，索引標籤的清單會出現在畫面底部，且在詳細資料區域上方。 每個索引標籤也會有圖示影像應該是一般解析的透明度與 30 PNG x 30、 60 x 60 為高解析度，且 90 x 90 適用於 iPhone 6 Plus 解析度。 如果有超過五個索引標籤上，*詳細* 索引標籤隨即出現，這可用來存取其他索引標籤。 如需載入 Xamarin.Forms 應用程式中的映像的詳細資訊，請參閱[處理映像](~/xamarin-forms/user-interface/images.md)。 如需圖示需求的詳細資訊，請參閱[建立索引標籤式的應用程式](~/ios/user-interface/controls/creating-tabbed-applications.md)。

    > [!NOTE]
  > 請注意， `TabbedRenderer` iOS 具有可覆寫`GetIcon`方法可以用來載入指定的來源 索引標籤圖示。 此覆寫會讓您能夠使用作為圖示上的 SVG 影像`TabbedPage`。 此外，您可以提供選取及未選取的圖示版本。

- 在 Android 上，索引標籤的清單會出現在螢幕頂端，以下是詳細資料區域。 索引標籤名稱會自動都大寫，以及使用者可以向上捲動的索引標籤集合，如果有太多而無法放入一個螢幕。

    > [!NOTE]
  > 請注意，當使用 AppCompat 在 Android 上，每個索引標籤也會顯示圖示。 此外， `TabbedPageRenderer` Android AppCompat 具有可覆寫`SetTabIcon`方法可以用來從自訂載入索引標籤圖示`Drawable`。 此覆寫會讓您能夠使用作為圖示上的 SVG 影像`TabbedPage`。

- Windows Phone 上索引標籤的清單會出現在螢幕頂端，以下是詳細資料區域。 如果有太多而無法放入一個螢幕的名稱會自動轉換為小寫和使用者 索引標籤可捲動索引標籤的集合。
- Windows 平板電腦的尺寸，在索引標籤不一定可見，並且使用者需要撥動清單 （或按一下滑鼠右鍵，如果有連接滑鼠），檢視中的索引標籤`TabbedPage`（如下所示）。

![](tabbed-page-images/windows-tabs.png "在 Windows 上的 TabbedPage 索引標籤")

## <a name="creating-a-tabbedpage"></a>建立 TabbedPage

兩種方法可以用來建立[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/):

- [填入](#Populating_a_TabbedPage_with_a_Page_Collection) [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)的子集合[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)物件，例如集合[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)執行個體。
- [指派](#Populating_a_TabbedPage_with_a_Template)集合[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/)屬性並指派[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)至[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/)屬性傳回的頁面集合中的物件。

這兩種方法，與[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)在使用者選取每個索引標籤會顯示每一頁。

> [!NOTE]
> 建議[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)應該填入[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)和[ `ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)僅限執行個體。 這有助於確保所有平台的一致的使用者經驗。

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>填入 TabbedPage 頁面集合

下列 XAML 程式碼範例所示[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)所填入的子項目集合建構[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)物件：

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

下列程式碼範例顯示的同等[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)以 C# 建立：

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

[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)會填入這兩個子[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)物件。 第一個子系是[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)執行個體，而且第二個索引標籤是[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)包含`ContentPage`執行個體。

> [!NOTE]
> **請注意**: [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)不支援 UI 虛擬化。 因此，效能可能會受到影響如果`TabbedPage`包含太多子項目。

下列螢幕擷取畫面顯示`TodayPage` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)執行個體，其會顯示在*今天* 索引標籤：

![](tabbed-page-images/today-page.png "在 TabbedPage ContentPage")

選取*排程*索引標籤會顯示`SchedulePage` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)執行個體，會包裝在[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)執行個體，並如下所示下列螢幕擷取畫面：

![](tabbed-page-images/schedule-page.png "在 TabbedPage NavigationPage")

如需有關的配置資訊[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)，請參閱[執行瀏覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

> [!NOTE]
> 而是將可接受的[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)到[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)，它不是建議放置`TabbedPage`到`NavigationPage`。 這是因為在 iOS、`UITabBarController`一律做為包裝函式`UINavigationController`。 如需詳細資訊，請參閱[結合檢視控制器介面](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html)iOS Developer Library 中。

#### <a name="navigation-inside-a-tab"></a>在索引標籤內的導覽

您可以從第二個索引標籤執行瀏覽，藉由叫用[ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/)方法[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)屬性[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)執行個體，下列程式碼範例所示：

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

這會使 `UpcomingAppointmentsPage` 執行個體推送至導覽堆疊上，而變成使用中的頁面。 下列螢幕擷取畫面所示：

![](tabbed-page-images/navigationpage.png "在索引標籤內的導覽")

如需有關執行瀏覽使用[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)類別，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>填入 TabbedPage 使用範本

下列 XAML 程式碼範例所示[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)建構藉由指派[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)至[ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/)屬性傳回的頁面集合中的物件：

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

[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)藉由設定填入資料[ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/)建構函式的程式碼後置檔案中的屬性：

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

下列程式碼範例顯示的同等[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)以 C# 建立：

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

每個索引標籤會顯示[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) ，使用一系列的[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)和[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)即可顯示索引標籤的資料執行個體。下列螢幕擷取畫面顯示的內容*Tamarin*索引標籤：

![](tabbed-page-images/tab3.png "填入 TabbedPage 使用範本")

然後選取另一個索引標籤會顯示該索引標籤上的內容。

> [!NOTE]
> [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)不支援 UI 虛擬化。 因此，效能可能會受到影響如果`TabbedPage`包含太多子項目。

如需有關[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)，請參閱[第 25 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)Charles Petzold Xamarin.Forms 活頁簿。

## <a name="summary"></a>總結

本文示範如何使用 TabbedPage 來瀏覽分頁的集合。 Xamarin.Forms [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)清單所組成的索引標籤和較大的詳細資料區域與詳細資料區域將內容載入每個索引標籤。


## <a name="related-links"></a>相關連結

- [頁面須](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)
