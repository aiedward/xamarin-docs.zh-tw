---
title: ListView 外觀
description: 本文說明如何使用標頭、頁尾、群組和變數高度資料格，自訂 Xamarin 中的 Listview 應用程式。
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
ms.openlocfilehash: 90b0e0f3802ce766decb802c9406d72b5966360e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032807"
---
# <a name="listview-appearance"></a>ListView 外觀

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)

除了清單中每個資料列的[`ViewCell`](xref:Xamarin.Forms.ViewCell)實例以外，[Xamarin] [`ListView`](xref:Xamarin.Forms.ListView)還可讓您自訂清單的呈現方式。

## <a name="grouping"></a>群組

大型資料集在持續滾動清單中呈現時可能會變得很困難。 啟用分組可以改善使用者在這些情況下的使用體驗，方法是更有效地組織內容，並啟用平臺特定控制項，讓流覽資料更容易。

啟用 `ListView` 的群組時，會為每個群組新增標頭資料列。

若要啟用群組：

- 建立清單清單（群組清單，每個群組都是元素的清單）。
- 將 `ListView` 的 `ItemsSource` 設定為該清單。
- 將 `IsGroupingEnabled` 設定為 true。
- 將[`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding)設定為系結至做為群組標題使用之群組的屬性。
- 選擇性將[`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding)設定為系結至用來做為群組簡短名稱之群組的屬性。 簡短名稱用於跳躍清單（iOS 上的右側資料行）。

從建立群組的類別開始：

```csharp
public class PageTypeGroup : List<PageModel>
    {
        public string Title { get; set; }
        public string ShortName { get; set; } //will be used for jump lists
        public string Subtitle { get; set; }
        private PageTypeGroup(string title, string shortName)
        {
            Title = title;
            ShortName = shortName;
        }

        public static IList<PageTypeGroup> All { private set; get; }
    }
```

在上述程式碼中，`All` 是要提供給 ListView 做為系結來源的清單。 `Title` 和 `ShortName` 是將用於群組標題的屬性。

在這個階段，`All` 是空的清單。 新增靜態的函式，以便在程式啟動時填入清單：

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alpha", "A"){
                new PageModel("Amelia", "Cedar", new switchCellPage(),""),
                new PageModel("Alfie", "Spruce", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ava", "Pine", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Archie", "Maple", new switchCellPage(), "grapefruit.jpg")
            },
            new PageTypeGroup ("Bravo", "B"){
                new PageModel("Brooke", "Lumia", new switchCellPage(),""),
                new PageModel("Bobby", "Xperia", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Bella", "Desire", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ben", "Chocolate", new switchCellPage(), "grapefruit.jpg")
            }
        };
        All = Groups; //set the publicly accessible list
}
```

在上述程式碼中，我們也可以在 `Groups` 的元素上呼叫 `Add`，這些專案是 `PageTypeGroup` 類型的實例。 這個方法是可能的，因為 `PageTypeGroup` 繼承自 `List<PageModel>`。

以下是用來顯示已群組清單的 XAML：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoListView.GroupingViewPage"
    <ContentPage.Content>
        <ListView  x:Name="GroupedView"
                   GroupDisplayBinding="{Binding Title}"
                   GroupShortNameBinding="{Binding ShortName}"
                   IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding Subtitle}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

此 XAML 會執行下列動作：

- 將 `GroupShortNameBinding` 設定為群組類別中定義的 `ShortName` 屬性
- 將 `GroupDisplayBinding` 設定為群組類別中定義的 `Title` 屬性
- 將 `IsGroupingEnabled` 設定為 true
- 已將 `ListView` 的 `ItemsSource` 變更為群組清單

下列螢幕擷取畫面顯示產生的 UI：

![](customizing-list-appearance-images/grouping-depth.png "ListView Grouping Example")

### <a name="customizing-grouping"></a>自訂群組

如果已在清單中啟用群組，則也可以自訂群組標頭。

類似于 `ListView` 如何定義資料列的顯示方式 `ItemTemplate`，`ListView` 具有 `GroupHeaderTemplate`。

在 XAML 中自訂群組標頭的範例如下所示：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoListView.GroupingViewPage">
    <ContentPage.Content>
        <ListView x:Name="GroupedView"
                  GroupDisplayBinding="{Binding Title}"
                  GroupShortNameBinding="{Binding ShortName}"
                  IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding Subtitle}"
                              TextColor="#f35e20"
                              DetailColor="#503026" />
                </DataTemplate>
            </ListView.ItemTemplate>
            <!-- Group Header Customization-->
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding ShortName}"
                              TextColor="#f35e20"
                              DetailColor="#503026" />
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
            <!-- End Group Header Customization -->
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

## <a name="headers-and-footers"></a>頁首和頁尾

ListView 可以呈現使用清單元素來滾動的頁首和頁尾。 頁首和頁尾可以是文字字串或較複雜的版面配置。 這個行為與[區段群組](#grouping)不同。

您可以將 `Header` 和/或 `Footer` 設定為 `string` 值，也可以將它們設定為較複雜的版面配置。 另外還有 `HeaderTemplate` 和 `FooterTemplate` 屬性，可讓您針對支援資料系結的頁首和頁尾，建立更複雜的版面配置。

若要建立基本的頁首/頁尾，只要將頁首或頁尾屬性設為您想要顯示的文字即可。 程式碼：

```csharp
ListView HeaderList = new ListView()
{
    Header = "Header",
    Footer = "Footer"
};
```

在 XAML 中：

```xaml
<ListView x:Name="HeaderList" 
          Header="Header"
          Footer="Footer">
    ...
</ListView>
```

![](customizing-list-appearance-images/header-default.png "ListView with Header and Footer")

若要建立自訂的頁首和頁尾，請定義頁首和頁尾的視圖：

```xaml
<ListView.Header>
    <StackLayout Orientation="Horizontal">
        <Label Text="Header"
               TextColor="Olive"
               BackgroundColor="Red" />
    </StackLayout>
</ListView.Header>
<ListView.Footer>
    <StackLayout Orientation="Horizontal">
        <Label Text="Footer"
               TextColor="Gray"
               BackgroundColor="Blue" />
    </StackLayout>
</ListView.Footer>
```

![](customizing-list-appearance-images/header-custom.png "ListView with Customized Header and Footer")

## <a name="scrollbar-visibility"></a>捲軸可見度

[`ListView`](xref:Xamarin.Forms.ListView)類別具有 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 屬性，可取得或設定[`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility)值，以表示水準或垂直捲動條何時可見。 這兩個屬性都可以設定為下列值：

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)表示平臺的預設捲軸行為，而是 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 屬性的預設值。
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)表示捲軸會顯示出來，即使內容適合在視圖中也一樣。
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)表示不會顯示捲軸，即使該內容無法放入視圖中也一樣。

## <a name="row-separators"></a>資料列分隔符號

預設會在 iOS 和 Android 上 `ListView` 元素之間顯示分隔線。 如果您想要隱藏 iOS 和 Android 上的分隔線，請在您的 ListView 上設定 [`SeparatorVisibility`] 屬性。 `SeparatorVisibility` 的選項包括：

- **預設值**-顯示 IOS 和 Android 上的分隔線。
- **無**-隱藏所有平臺上的分隔符號。

預設可見度：

C#:

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XML

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "ListView with Default Row Separators")

無

C#:

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XML

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "ListView without Row Separators")

您也可以透過 [`SeparatorColor`] 屬性來設定分隔線的色彩：

C#:

```csharp
SeparatorDemoListView.SeparatorColor = Color.Green;
```

XML

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "ListView with Green Row Separators")

> [!NOTE]
> 在載入 `ListView` 之後，在 Android 上設定其中一個屬性會導致效能大幅下降。

## <a name="row-height"></a>資料列高度

根據預設，ListView 中的所有資料列都有相同的高度。 ListView 有兩個可以用來變更該行為的屬性：

- `HasUnevenRows` &ndash; `true` / `false` 值，如果設定為 [`true`]，則資料列會有不同的高度。 預設值為 `false`。
- `RowHeight` &ndash; 會在 `false` `HasUnevenRows` 時，設定每個資料列的高度。

您可以藉由設定 `ListView` 上的 [`RowHeight`] 屬性，來設定所有資料列的高度。

### <a name="custom-fixed-row-height"></a>自訂固定資料列高度

C#:

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XML

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "ListView with Fixed Row Height")

### <a name="uneven-rows"></a>不平均的資料列

如果您想要讓個別資料列具有不同的高度，可以將 `HasUnevenRows` 屬性設定為 [`true`]。 一旦 `HasUnevenRows` 設定為 `true`，資料列高度就不需要手動設定一次，因為 Xamarin 會自動計算高度。

C#:

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XML

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "ListView with Uneven Rows")

### <a name="resize-rows-at-runtime"></a>在執行時間調整資料列的大小

個別 `ListView` 的資料列可以在執行時間以程式設計方式調整大小，前提是 `HasUnevenRows` 屬性已設定為 [`true`]。 [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize)的方法會更新儲存格的大小，即使目前看不到也一樣，如下列程式碼範例所示：

```csharp
void OnImageTapped (object sender, EventArgs args)
{
    var image = sender as Image;
    var viewCell = image.Parent.Parent as ViewCell;

    if (image.HeightRequest < 250) {
        image.HeightRequest = image.Height + 100;
        viewCell.ForceUpdateSize ();
    }
}
```

系統會執行 `OnImageTapped` 事件處理常式，以回應所選儲存格中的[`Image`](xref:Xamarin.Forms.Image) ，並增加儲存格中顯示的 `Image` 大小，以方便您查看。

![](customizing-list-appearance-images/dynamic-row-resizing.png "ListView with Runtime Row Resizing")

> [!WARNING]
> 過度執行執行時間資料列調整大小可能會導致效能降低。

## <a name="related-links"></a>相關連結

- [群組（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [自訂轉譯器視圖（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [動態調整資料列大小（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-dynamicunevenlistcells)
- [1.4 版本資訊](https://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 版本資訊](https://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
