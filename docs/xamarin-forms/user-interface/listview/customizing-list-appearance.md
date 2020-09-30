---
title: ListView 外觀
description: 本文說明如何 Xamarin.Forms 使用標頭、頁尾、群組和可變高度資料格，在應用程式中自訂 listview。
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dcf4e7ca0f081ff000aaa69d8be3a3cb3aa43e8e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560282"
---
# <a name="listview-appearance"></a>ListView 外觀

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 除了 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 清單中每個資料列的實例之外，還可讓您自訂清單的呈現方式。

## <a name="grouping"></a>群組

在連續滾動清單中顯示大型資料集時，可能會變得很困難。 啟用分組可以改善使用者在這些案例中的使用體驗，方法是更妥善地組織內容，並啟用平臺特定的控制項，讓您更輕鬆地流覽資料。

當啟用群組時 `ListView` ，會為每個群組新增標頭資料列。

若要啟用群組：

-  (群組清單建立清單清單，每個群組都是) 的元素清單。
- 將設定 `ListView` `ItemsSource` 為該清單。
- 將 `IsGroupingEnabled` 設定為 True。
- 設定 [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) 為系結至用來作為群組標題的群組屬性。
- 參數設定為 [系結]，以系結 [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) 至用來作為群組簡短名稱的群組屬性。 簡短名稱用於 iOS) 上 (右側資料行的快捷方式清單。

首先，建立群組的類別：

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

在上述程式碼中， `All` 是將會提供給 ListView 作為系結來源的清單。 `Title` 以及 `ShortName` 將用於群組標題的屬性。

在這個階段， `All` 是空的清單。 新增靜態的函式，以便在程式啟動時填入清單：

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

在上述程式碼中，我們也可以呼叫的 `Add` 元素 `Groups` ，也就是類型的實例 `PageTypeGroup` 。 這是可行的方法，因為 `PageTypeGroup` 繼承自 `List<PageModel>` 。

以下是用來顯示群組清單的 XAML：

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

- 設定 `GroupShortNameBinding` 為 `ShortName` 群組類別中定義的屬性
- 設定 `GroupDisplayBinding` 為 `Title` 群組類別中定義的屬性
- 設定 `IsGroupingEnabled` 為 true
- 已將 `ListView` 變更 `ItemsSource` 為群組清單

下列螢幕擷取畫面顯示產生的 UI：

![ListView 群組範例](customizing-list-appearance-images/grouping-depth.png)

### <a name="customizing-grouping"></a>自訂群組

如果清單中已啟用群組，也可以自訂群組標頭。

類似于如何 `ListView` 定義資料列的顯示方式 `ItemTemplate` ， `ListView` 有 `GroupHeaderTemplate` 。

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

ListView 可能會顯示標頭和頁尾，以清單的元素來進行滾動。 頁首和頁尾可以是文字字串或更複雜的版面配置。 這種行為與 [區段群組](#grouping)不同。

您可以將 `Header` 和/或設定 `Footer` 為 `string` 值，也可以將它們設定為更複雜的版面配置。 另外還有一些 `HeaderTemplate` `FooterTemplate` 屬性，可讓您針對支援資料系結的頁首和頁尾建立更複雜的版面配置。

若要建立基本頁首/頁尾，只要將頁首或頁尾屬性設定為您想要顯示的文字。 在程式碼中：

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

![具有頁首和頁尾的 ListView](customizing-list-appearance-images/header-default.png)

若要建立自訂的頁首和頁尾，請定義頁首和頁尾視圖：

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

![具有自訂頁首和頁尾的 ListView](customizing-list-appearance-images/header-custom.png)

## <a name="scrollbar-visibility"></a>捲軸可見度

[`ListView`](xref:Xamarin.Forms.ListView)類別具有 `HorizontalScrollBarVisibility` 和 `VerticalScrollBarVisibility` 屬性，可取得或設定 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) 表示水準或垂直捲動條何時可見的值。 這兩個屬性都可以設定為下列值：

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) 表示平臺的預設捲軸行為，而且是和屬性的預設值 `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` 。
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) 表示捲軸會顯示出來，即使內容符合視野也是如此。
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) 指出不會顯示捲軸，即使內容無法容納在視圖中也一樣。

## <a name="row-separators"></a>資料列分隔符號

`ListView`預設會在 iOS 和 Android 上的元素之間顯示分隔線。 如果您想要隱藏 iOS 和 Android 上的分隔線，請 `SeparatorVisibility` 在您的 ListView 上設定屬性。 的選項 `SeparatorVisibility` 如下：

- **預設值** -顯示 IOS 和 Android 上的分隔線。
- **無** -在所有平臺上隱藏分隔符號。

預設可見度：

C#：

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

Xaml：

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![具有預設資料列分隔符號的 ListView](customizing-list-appearance-images/separator-default.png)

無：

C#：

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

Xaml：

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![不含資料列分隔符號的 ListView](customizing-list-appearance-images/separator-none.png)

您也可以透過屬性來設定分隔線的色彩 `SeparatorColor` ：

C#：

```csharp
SeparatorDemoListView.SeparatorColor = Color.Green;
```

Xaml：

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![具有綠色資料列分隔符號的 ListView](customizing-list-appearance-images/separator-custom.png)

> [!NOTE]
> 在載入之後，在 Android 上設定這些屬性的其中之一會導致 `ListView` 效能大幅下降。

## <a name="row-height"></a>資料列高度

依預設，ListView 中的所有資料列都具有相同的高度。 ListView 有兩個屬性可以用來變更該行為：

- `HasUnevenRows`&ndash; `true`/`false`值，如果設定為，資料列會有不同的高度 `true` 。 預設值為 `false`。
- `RowHeight`&ndash;當為時，設定每個資料列的高度 `HasUnevenRows` `false` 。

您可以藉由在上設定屬性，來設定所有資料列的高度 `RowHeight` `ListView` 。

### <a name="custom-fixed-row-height"></a>自訂固定資料列高度

C#：

```csharp
RowHeightDemoListView.RowHeight = 100;
```

Xaml：

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![具有固定資料列高度的 ListView](customizing-list-appearance-images/height-custom.png)

### <a name="uneven-rows"></a>不平均的資料列

如果您想要讓個別資料列具有不同的高度，可以將 `HasUnevenRows` 屬性設定為 `true` 。 資料列高度不需要手動設定一次 `HasUnevenRows` ，因為系統會 `true` 自動計算高度 Xamarin.Forms 。

C#：

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

Xaml：

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![具有不平均資料列的 ListView](customizing-list-appearance-images/height-uneven.png)

### <a name="resize-rows-at-runtime"></a>在執行時間調整資料列的大小

`ListView`如果 `HasUnevenRows` 屬性設定為，就可以在執行時間以程式設計方式調整個別資料列的大小 `true` 。 [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法會更新資料格的大小（即使目前看不到），如下列程式碼範例所示：

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

`OnImageTapped`系統會執行事件處理常式來回應 [`Image`](xref:Xamarin.Forms.Image) 正在進行點擊的儲存格，並增加 `Image` 儲存格中顯示的大小，以便輕鬆地加以查看。

![使用執行時間資料列調整大小的 ListView](customizing-list-appearance-images/dynamic-row-resizing.png)

> [!WARNING]
> 過度利用執行時間資料列調整大小，可能會導致效能降低。

## <a name="related-links"></a>相關連結

- [群組 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [自訂轉譯器視圖 (範例) ](/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [動態調整資料列大小 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-listview-dynamicunevenlistcells)
- [1.4 版本資訊](https://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 版本資訊](https://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)