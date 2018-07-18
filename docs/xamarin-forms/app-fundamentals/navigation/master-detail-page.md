---
title: Xamarin.Forms 主版詳細資料頁面
description: Xamarin.Forms MasterDetailPage 是資訊的管理兩個相關的頁面-顯示項目、 主版頁面和主版頁面顯示項目的相關詳細資料的詳細資料頁面的頁面。 這篇文章說明如何使用 MasterDetailPage 和其資訊的頁面之間巡覽。
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: a3d0edbd933339ee8b8a0a277a4f2493cc8dc70e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997461"
---
# <a name="xamarinforms-master-detail-page"></a>Xamarin.Forms 主版詳細資料頁面

_Xamarin.Forms MasterDetailPage 是資訊的管理兩個相關的頁面-顯示項目、 主版頁面和主版頁面顯示項目的相關詳細資料的詳細資料頁面的頁面。這篇文章說明如何使用 MasterDetailPage 和其資訊的頁面之間巡覽。_

## <a name="overview"></a>總覽

主版頁面通常會顯示一份項目，如下列螢幕擷取畫面所示：

[![](master-detail-page-images/masterpage-components.png "主版頁面元件")](master-detail-page-images/masterpage-components-large.png#lightbox "主版頁面元件")

項目清單的位置等同於每個平台，並選取其中一個項目會巡覽至對應的詳細資料頁面。 此外，主版頁面也會提供包含一個按鈕，可用來瀏覽至作用中的詳細資料頁面巡覽列：

- 在 iOS 上，瀏覽列會出現在頁面頂端，並有一個按鈕，巡覽至詳細資料頁面。 此外，作用中的詳細資料頁面可以瀏覽至撥動左側的主版頁面。
- 在 Android 上，瀏覽列會出現在頁面頂端，並顯示 詳細資料頁面的 標題、 圖示和瀏覽按鈕。 中所定義的圖示`[Activity]`裝飾的屬性`MainActivity`Android 平台專屬專案中的類別。 此外，作用中的詳細資料頁面可以瀏覽至撥動左側的主版頁面、 依序點選 [詳細資料] 頁面最右邊的畫面中，和點選*回*在畫面底部的按鈕。
- 在通用 Windows 平台 (UWP)，瀏覽列會出現在頁面頂端，並有一個按鈕，巡覽至詳細資料頁面。

對應至項目的詳細資料頁面會顯示資料 頁面上，選取在主機上，下列螢幕擷取畫面會顯示詳細資料頁面的主要元件：

![](master-detail-page-images/detailpage-components.png "詳細資料頁面元件")

[詳細資料] 頁面包含導覽列中，其內容會平台而不同：

- 在 iOS 上，瀏覽列會出現在頁面頂端顯示標題，並有主版頁面中，會傳回一個按鈕，前提是詳細資料頁面執行個體包裝在[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)執行個體。 此外，主版頁面可以返回撥動右邊的 [詳細資料] 頁面。
- 在 Android 上，瀏覽列會出現在頁面頂端，並顯示標題、 圖示和按鈕回到主版頁面。 中所定義的圖示`[Activity]`裝飾的屬性`MainActivity`Android 平台專屬專案中的類別。
- 在 UWP 上導覽列會出現在頁面頂端顯示標題，並有一個按鈕，傳回至主版頁面。

### <a name="navigation-behavior"></a>導覽行為

瀏覽體驗之間 master] 和 [詳細資料頁面的行為取決於平台：

- 在 iOS 上，詳細資料頁面*投影片*作為主版頁面的投影片右邊，從左側和左側的組件的詳細資料頁面是仍然可見。
- 在 Android 上，詳細資料] 和 [主版頁面都*重疊*彼此。
- UWP 上的詳細資料] 和 [主版頁面會*交換*。

會以橫向模式中，觀察到類似的行為，之處在於 iOS 和 Android 上的主版頁面會有在直向模式中，主版頁面為類似的寬度讓更多詳細資料頁面會顯示。

如需控制瀏覽行為的資訊，請參閱[控制詳細資料頁面的顯示行為](#Controlling_the_Detail_Page_Display_Behavior)。

## <a name="creating-a-masterdetailpage"></a>建立 MasterDetailPage

A [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)包含[ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)並[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)這兩個類型的屬性[ `Page` ](xref:Xamarin.Forms.Page)，用來取得，並分別設定 master] 和 [詳細資料頁面。

> [!IMPORTANT]
> A [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)設計為根頁面上，並使用它，因為子分頁在其他的頁面類型可能會導致非預期且不一致的行為。 此外，建議的主版頁面[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)應該總是[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)執行個體，並詳細資料頁面只會填入與[ `TabbedPage`](xref:Xamarin.Forms.TabbedPage)， [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，和`ContentPage`執行個體。 這有助於確保一致的使用者體驗，跨所有平台。

下列 XAML 程式碼範例所示[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)如此[ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)並[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)屬性：

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

下列程式碼範例示範相當[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) C# 建立的：

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

[ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)屬性設定為[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)執行個體。 [ `MasterDetailPage.Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)屬性設定為[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)包含`ContentPage`執行個體。

### <a name="creating-the-master-page"></a>建立主版頁面

下列 XAML 程式碼範例顯示的宣告`MasterPage`物件，可用來參考[ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)屬性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             Icon="hamburger.png"
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

頁面組成[ `ListView` ](xref:Xamarin.Forms.ListView) ，並在 XAML 中的資料填入藉由設定其[ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource)屬性的陣列`MasterPageItem`執行個體。 每個`MasterPageItem`定義`Title`， `IconSource`，和`TargetType`屬性。

A [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)指派給[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)屬性，以顯示每個`MasterPageItem`。 `DataTemplate`包含[ `ViewCell` ](xref:Xamarin.Forms.ViewCell) ，組成[ `Image` ](xref:Xamarin.Forms.Image)並[ `Label` ](xref:Xamarin.Forms.Label)。 [ `Image` ](xref:Xamarin.Forms.Image)會顯示`IconSource`屬性值，而[ `Label` ](xref:Xamarin.Forms.Label)顯示`Title`屬性值，每個`MasterPageItem`。

此頁面有其[ `Title` ](xref:Xamarin.Forms.Page.Title)並[ `Icon` ](xref:Xamarin.Forms.Page.Icon)屬性集。 圖示會出現在 [詳細資料] 頁面中，前提是詳細資料頁面的標題列。 這必須由包裝的詳細資料頁面的執行個體中啟用 iOS 上[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)執行個體。

> [!NOTE]
> [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)分頁必須具有其[ `Title` ](xref:Xamarin.Forms.Page.Title)設定屬性，或會發生例外狀況。

下列程式碼範例示範在 C# 中建立的對等頁面：

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

    Icon = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

以下的螢幕擷取畫面顯示每個平台上的主版頁面：

![](master-detail-page-images/masterpage.png "主版頁面範例")

### <a name="creating-and-displaying-the-detail-page"></a>建立和顯示詳細資料頁面

`MasterPage`執行個體包含`ListView`公開 （expose） 的屬性及其[ `ListView` ](xref:Xamarin.Forms.ListView)執行個體以便`MainPage` [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)可以註冊執行個體事件處理常式來處理[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件。 這可讓`MainPage`若要設定的執行個體[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)屬性來表示所選之網頁`ListView`項目。 下列程式碼範例示範事件處理常式：

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

`OnItemSelected`方法會執行下列動作：

- 它會擷取[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)從[ `ListView` ](xref:Xamarin.Forms.ListView)執行個體，並提供不是`null`，設定的詳細資訊頁面中儲存的頁面類型的新執行個體`TargetType`屬性`MasterPageItem`。 頁面類型包裝在[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)執行個體，以確保透過參考圖示[ `Icon` ](xref:Xamarin.Forms.Page.Icon)屬性`MasterPage`會顯示在詳細資料頁面上，在 iOS 中。
- 在 選取的項目[ `ListView` ](xref:Xamarin.Forms.ListView)設為`null`以確保沒有任何`ListView`下一次會選取項目`MasterPage`呈現。
- 詳細資料頁面會呈現給使用者，藉由設定[ `MasterDetailPage.IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented)屬性設`false`。 此屬性控制是否要顯示 master] 或 [詳細資料頁面。 它應該設定為`true`顯示主版頁面中，以及`false`來顯示詳細資料頁面。

下列螢幕擷取畫面顯示`ContactPage`詳細資料頁面上之後它已被選取的主版頁面上, 顯示：

![](master-detail-page-images/detailpage.png "詳細資料頁面範例")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>控制詳細資料頁面顯示行為

如何[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)管理 master] 和 [詳細資料頁面取決於應用程式在手機或平板電腦、 裝置的方向以及 windows 7 上執行[ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)屬性。 此屬性會決定將會顯示詳細資料頁面的方式。 它的可能值如下：

- **預設**– 頁面會顯示使用平台預設值。
- **Popover** – 詳細資料頁面涵蓋，或部分涵蓋的主版頁面。
- **分割**： 主版頁面會顯示在左邊，並且詳細資料頁面在右邊。
- **SplitOnLandscape** – 當設備處於橫向方向時使用分屏。
- **SplitOnPortrait** -裝置為直向時，會使用分割畫面。

下列 XAML 程式碼範例示範如何設定[ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)上的屬性[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

下列程式碼範例示範相當[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) C# 建立的：

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

不過，windows 7 [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior)屬性只會影響在平板電腦或桌上型電腦上執行的應用程式。 一律在手機上執行的應用程式都有*Popover*行為。

## <a name="summary"></a>總結

這篇文章示範如何使用[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)和其資訊的頁面之間巡覽。 Xamarin.Forms`MasterDetailPage`是管理兩個頁面的相關資訊-顯示項目、 主版頁面和主版頁面顯示項目的相關詳細資料的詳細資料頁面的頁面。


## <a name="related-links"></a>相關連結

- [頁面變化](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage （範例）](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
