---
title: Xamarin.Forms Master-Detail Page
description: Xamarin.Forms MasterDetailPage 是管理兩個相關資訊頁面的頁面 – 顯示項目的主版頁面，和顯示主版頁面上項目相關詳細資料的詳細資料頁面。 本文說明如何使用 MasterDetailPage 及在其資訊頁面之間巡覽。
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 702ec35bca051f7255c5c9d67d2dc68d4f89ca52
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "68645961"
---
# <a name="xamarinforms-master-detail-page"></a>Xamarin.Forms Master-Detail Page

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_Xamarin.Forms MasterDetailPage 是一個管理兩個相關資訊頁的頁面 , 一個是顯示項的母版頁,另一個是顯示母版頁上有關專案的詳細資訊的詳細資訊頁。本文介紹如何使用 MasterDetailPage 並在其資訊頁之間導航。_

## <a name="overview"></a>概觀

主版頁面通常會顯示一份項目清單，如下列螢幕擷取畫面所示：

[![](master-detail-page-images/masterpage-components.png "Master Page Components")](master-detail-page-images/masterpage-components-large.png#lightbox "Master Page Components")

項目清單的位置在每個平台上都相同，且選取其中一個項目會巡覽至對應的詳細資料頁面。 此外，主版頁面也會提供包含一個按鈕的導覽列，該按鈕可用來巡覽至作用中的詳細資料頁面：

- 在 iOS 上，導覽列會出現在頁面頂端，並有一個按鈕，可巡覽至詳細資料頁面。 此外，可以藉由將主版頁面撥動至左側，巡覽至作用中的詳細資料頁面。
- 在 Android 上，導覽列會出現在頁面頂端，並顯示一個標題、圖示，以及可巡覽至詳細資料頁面的按鈕。 該圖示是在 `[Activity]` 屬性中定義，以裝飾 Android 平台特定專案中的 `MainActivity` 類別。 此外，可以藉由將主版頁面撥動至左側、點選畫面最右邊的詳細資料頁面，和點選畫面底部的 [上一步]** 按鈕，巡覽至作用中的詳細資料頁面。
- 在通用 Windows 平台 (UWP) 上，導覽列出現在頁面頂端，並具有可巡覽至詳細資料頁面的按鈕。

詳細資料頁面會顯示對應至主版頁面上所選取項目的資料，且會顯示詳細資料頁面的主要元件，如下列螢幕擷取畫面所示：

![](master-detail-page-images/detailpage-components.png "Detail Page Components")

詳細資料頁面包含導覽列，其內容會因平台而不同：

- 在 iOS 上,導航欄位於頁面頂部並顯示標題,並且具有返回到母版頁的按鈕,前提是詳細資訊頁實[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)例在 實例中包裝。 此外，可以藉由將詳細資料頁面撥動至右側，回到主版頁面。
- 在 Android 上，導覽列出現在頁面頂端並顯示標題、圖示，以及可返回主版頁面的按鈕。 該圖示是在 `[Activity]` 屬性中定義，以裝飾 Android 平台特定專案中的 `MainActivity` 類別。
- 在 UWP 上，導覽列出現在頁面頂端並顯示標題，並具有可返回主版頁面的按鈕。

### <a name="navigation-behavior"></a>巡覽行為

主版頁面和詳細資料頁面之間的巡覽體驗行為，取決於平台：

- 在 iOS 上，當主版頁面從左邊滑動時，詳細資料頁面會「滑」** 向右邊，詳細資料頁面的左邊部分仍然可見。
- 在 Android 上，詳細資料頁面和主版頁面會彼此「重疊」**。
- 在 UWP 上,母版頁從詳細資訊頁左側的左側滑動,前提[`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)是該`Popover`屬性設定為 。 如需詳細資訊，請參閱[控制詳細資料頁面顯示行為](#Controlling_the_Detail_Page_Display_Behavior)。

在橫向模式中會觀察到類似的行為，只除了 iOS 和 Android 上的主版頁面，會具有與直向模式主版頁面類似的寬度，因此會顯示更多詳細資料頁面。

如需控制巡覽行為的資訊，請參閱[控制詳細資料頁面的顯示行為](#Controlling_the_Detail_Page_Display_Behavior)。

## <a name="creating-a-masterdetailpage"></a>建立 MasterDetailPage

包含[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)[`Master`](xref:Xamarin.Forms.MasterDetailPage.Master)和[`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)屬性都是類型[`Page`](xref:Xamarin.Forms.Page)的 ,分別用於獲取和設置母版和詳細資訊頁。

> [!IMPORTANT]
> A[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)設計為根頁,在其他頁面類型中將其用作子頁可能會導致意外和不一致的行為。 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)此外,建議的母版頁應始終是實例[`ContentPage`](xref:Xamarin.Forms.ContentPage),並且詳細資訊頁應僅填[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)充[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)`ContentPage`, 和 實例。 這有助於跨所有平台確保一致的使用者體驗。

下面的 XAML 代碼範例[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)顯示[`Master`](xref:Xamarin.Forms.MasterDetailPage.Master)了[`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)設定與 屬性的 a:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  xmlns:local="clr-namespace:MasterDetailPageNavigation;assembly=MasterDetailPageNavigation"
                  x:Class="MasterDetailPageNavigation.MainPage">
    <MasterDetailPage.Master>
        <local:MasterPage x:Name="masterPage" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

以下代碼範例顯示 C# 中建立的等效項目[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage):

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        masterPage = new MasterPageCS ();
        Master = masterPage;
        Detail = new NavigationPage (new ContactsPageCS ());
        ...
    }
    ...
}
```

屬性[`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master)設置為[`ContentPage`](xref:Xamarin.Forms.ContentPage)實例。 屬性[`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)設定[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)為包含實體的`ContentPage`。

### <a name="creating-the-master-page"></a>建立主版頁面

下面的 XAML 代碼範例`MasterPage`顯示了 物件的聲明,[`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master)該聲明透過屬性引用:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             IconImageSource="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:MasterPageItem}">
                    <local:MasterPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:MasterPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:MasterPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid Padding="5,10">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="30"/>
                                <ColumnDefinition Width="*" />
                            </Grid.ColumnDefinitions>
                            <Image Source="{Binding IconSource}" />
                            <Label Grid.Column="1" Text="{Binding Title}" />
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

該頁由一個[`ListView`](xref:Xamarin.Forms.ListView)通過在 XAML 中填充[`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource)數據,將其`MasterPageItem`屬性設置為 實例陣列。 每個 `MasterPageItem` 會定義 `Title`、`IconSource` 和 `TargetType` 屬性。

將[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)配置到[`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)屬性,以顯示`MasterPageItem`每個 。 `DataTemplate`包含[`ViewCell`](xref:Xamarin.Forms.ViewCell)[`Image`](xref:Xamarin.Forms.Image)由[`Label`](xref:Xamarin.Forms.Label)與的 將顯示[`Image`](xref:Xamarin.Forms.Image)`IconSource`屬性值,[`Label`](xref:Xamarin.Forms.Label)並顯示`Title``MasterPageItem`每個的屬性值。

頁面已設置其[`Title`](xref:Xamarin.Forms.Page.Title)[`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource)和 屬性。 圖示會出現在詳細資料頁面上，前提是詳細資料頁面有標題列。 必須在 iOS 上通過在實例中包裝詳細資訊頁實[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)例來 啟用此功能。

> [!NOTE]
> 頁面[`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master)必須設置[`Title`](xref:Xamarin.Forms.Page.Title)其 屬性,否則將發生異常。

下列程式碼範例示範以 C# 建立的相等頁面：

```csharp
public class MasterPageCS : ContentPage
{
  public ListView ListView { get { return listView; } }

  ListView listView;

  public MasterPageCS ()
  {
    var masterPageItems = new List<MasterPageItem> ();
    masterPageItems.Add (new MasterPageItem {
      Title = "Contacts",
      IconSource = "contacts.png",
      TargetType = typeof(ContactsPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "TodoList",
      IconSource = "todo.png",
      TargetType = typeof(TodoListPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "Reminders",
      IconSource = "reminders.png",
      TargetType = typeof(ReminderPageCS)
    });

    listView = new ListView {
      ItemsSource = masterPageItems,
      ItemTemplate = new DataTemplate (() => {
        var grid = new Grid { Padding = new Thickness(5, 10) };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(30) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });

        var image = new Image();
        image.SetBinding(Image.SourceProperty, "IconSource");
        var label = new Label { VerticalOptions = LayoutOptions.FillAndExpand };
        label.SetBinding(Label.TextProperty, "Title");

        grid.Children.Add(image);
        grid.Children.Add(label, 1, 0);

        return new ViewCell { View = grid };
      }),
      SeparatorVisibility = SeparatorVisibility.None
    };

    IconImageSource = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

下列螢幕擷取畫面顯示每個平台上的主版頁面：

![](master-detail-page-images/masterpage.png "Master Page Example")

### <a name="creating-and-displaying-the-detail-page"></a>建立和顯示詳細資料頁面

實體包含`MasterPage``ListView`屬性,該[`ListView`](xref:Xamarin.Forms.ListView)屬性公開其實體,`MainPage`[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)以便實體可以註冊事件處理程式來處理該[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件。 這使`MainPage`實例能夠[`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)將 屬性設置為表示`ListView`所選項的頁面。 下列程式碼範例示範事件處理常式：

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

`OnItemSelected` 方法會執行下列動作：

- 它將[`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)從[`ListView`](xref:Xamarin.Forms.ListView)實例中檢索 ,`null`前提是 它不是,則將詳細資訊頁設置到`TargetType``MasterPageItem`存儲在 屬性 中的頁類型的新實例。 頁面類型包裝在[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)實例中,以確保通過 inon 上[`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource)`MasterPage`的屬性引用的圖示顯示在 iOS 中的詳細資訊頁上。
- [`ListView`](xref:Xamarin.Forms.ListView)中的選定項`null`設置為,以確保下次顯示`ListView``MasterPage`時 不會選擇任何專案。
- 通過將[`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented)屬性設置`false`為 ,向使用者顯示詳細資訊頁。 此屬性控制要顯示主版頁面還是詳細資料頁面。 如果要顯示主版頁面，它應該設定為 `true`，若要顯示詳細資料頁面，則設定為 `false`。

下列螢幕擷取畫面顯示 `ContactPage` 詳細資料頁面，在主版頁面上選取它之後便會加以顯示：

![](master-detail-page-images/detailpage.png "Detail Page Example")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>控制詳細資料頁面顯示行為

如何[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)管理母版和詳細資訊頁取決於應用程式是否在手機或平板電腦上運行、設備方向[`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)以及 屬性的值。 此屬性會決定詳細資料頁面的顯示方式。 可能值如下：

- **預設** – 會使用平台預設來顯示頁面。
- **Popover** – 詳細資料頁面會蓋住或部分蓋住主版頁面。
- **分割** – 主版頁面顯示在左邊，詳細資料頁面在右邊。
- **SplitOnLandscape** – 裝置處於橫向時，會使用分割畫面。
- **SplitOnPortrait** – 裝置處於直向時，會使用分割畫面。

以下 XAML 代碼範例展示如何[`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)在[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)上設定 屬性 :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

以下代碼範例顯示 C# 中建立的等效項目[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage):

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        MasterBehavior = MasterBehavior.Popover;
        ...
    }
}
```

但是,[`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)屬性的值僅影響在平板電腦或桌面上運行的應用程式。 在手機上執行的應用程式一律為 *Popover* 行為。

## <a name="summary"></a>總結

本文演示了如何使用[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)和 導航在其信息頁之間。 Xamarin.Forms `MasterDetailPage` 是管理兩個相關資訊頁面的頁面 – 顯示項目的主版頁面，和顯示主版頁面上項目相關詳細資料的詳細資料頁面。

## <a name="related-links"></a>相關連結

- [頁面品種](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
