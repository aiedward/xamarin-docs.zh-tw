---
title: 自訂 ListView 的外觀
description: 這篇文章說明如何在 Xamarin.Forms 應用程式自訂的 Listview，使用標頭、 頁尾、 群組和變數的高度資料格。
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
ms.openlocfilehash: 49bb3f901f2b1f8ed4ae26650cb0509aabfd2369
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647622"
---
# <a name="customizing-listview-appearance"></a>自訂 ListView 的外觀

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)

[`ListView`](xref:Xamarin.Forms.ListView)除了清單中每個資料列的[`ViewCell`](xref:Xamarin.Forms.ViewCell)實例之外, 還能夠控制清單的呈現方式。

<a name="Grouping" />

## <a name="grouping"></a>群組
通常，大型資料集可以變得雜亂無章時持續捲動清單所述。 啟用群組可以改善使用者體驗，在這些情況下，進一步組織內容，並啟用輕鬆巡覽資料的平台專屬控制項。

當群組啟用`ListView`，標頭資料列會加入每個群組。

若要啟用群組：

- 建立一份清單 （群組的清單，每個群組所需項目的清單）。
- 設定`ListView`的`ItemsSource`該清單。
- 設定`IsGroupingEnabled`設為 true。
- 設定[ `GroupDisplayBinding` ](xref:Xamarin.Forms.ListView.GroupDisplayBinding)繫結至屬性群組，做為群組的標題。
- [選用]設定[ `GroupShortNameBinding` ](xref:Xamarin.Forms.ListView.GroupShortNameBinding)繫結至群組的屬性，顯示正做為群組的簡短名稱。 捷徑清單 （如在 iOS 上的資料行右邊） 使用簡短的名稱。

啟動建立群組的類別：

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

在上述程式碼中，`All`是要提供給我們的 ListView，做為繫結來源的清單。 `Title` 和`ShortName`是將用於群組標題的屬性。

在這個階段，`All`是空的清單。 新增靜態建構函式，以便將填入的清單，在程式啟動：

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alfa", "A"){
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
        }
        All = Groups; //set the publicly accessible list
}
```

在上述程式碼中, 我們也可以`Add`呼叫的`groups`元素, 這是類型`PageTypeGroup`的實例。 這可能是因為`PageTypeGroup`繼承自`List<PageModel>`。 這是清單的先前所述的清單模式的範例。

以下是 XAML 來顯示群組的清單：

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

這會產生下列項目：

![](customizing-list-appearance-images/grouping-depth.png "ListView 群組範例")

請注意，我們有：

- 設定`GroupShortNameBinding`至`ShortName`我們群組類別中定義的屬性
- 設定`GroupDisplayBinding`至`Title`我們群組類別中定義的屬性
- 設定`IsGroupingEnabled`設為 true
- 變更`ListView`的`ItemsSource`至群組的清單

### <a name="customizing-grouping"></a>自訂群組

如果已啟用群組清單中，您也可以自訂群組標頭。

類似於如何`ListView`已經`ItemTemplate`定義的資料列的顯示方式，`ListView`具有`GroupHeaderTemplate`。

在 XAML 中的群組標頭的範例如下所示：

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

<a name="Headers_and_Footers" />

## <a name="headers-and-footers"></a>頁首和頁尾
您有 ListView 來呈現清單項目捲動的頁首和頁尾。 頁首和頁尾可以是文字字串或更複雜的版面配置。 請注意，這是分開[區段群組](#Grouping)。

您可以設定`Header`及/或`Footer`成簡單字串值，或者您可以設定它們以更複雜的配置。
另外還有`HeaderTemplate`和`FooterTemplate`屬性，可讓您建立更複雜的頁首和頁尾的版面配置支援資料繫結。

若要建立簡單的頁首/頁尾，只是設定為您想要顯示的文字的頁首或頁尾的屬性。 程式碼：

```csharp
ListView HeaderList = new ListView() {
    Header = "Header",
    Footer = "Footer"
    };
```

在 XAML 中：

```xaml
<ListView  x:Name="HeaderList"  Header="Header" Footer="Footer"></ListView>
```

![](customizing-list-appearance-images/header-default.png "使用頁首和頁尾的 ListView")

若要建立自訂的頁首和頁尾，定義的頁首和頁尾的檢視：

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

![](customizing-list-appearance-images/header-custom.png "使用自訂的頁首和頁尾的 ListView")

## <a name="scrollbar-visibility"></a>捲軸可見度

[`ListView`](xref:Xamarin.Forms.ListView)具有`HorizontalScrollBarVisibility`和`VerticalScrollBarVisibility`屬性[,`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility)可取得或設定表示水準或垂直捲動條何時可見的值。 這兩個屬性都可以設定為下列值:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)表示平臺的預設捲軸行為, 而是`HorizontalScrollBarVisibility`和`VerticalScrollBarVisibility`屬性的預設值。
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)表示捲軸會顯示出來, 即使內容適合在視圖中也一樣。
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)表示即使內容無法放在視圖中, 也不會顯示捲軸。

<a name="Row_Separators" />

## <a name="row-separators"></a>資料列分隔符號
之間顯示分隔符號行`ListView`預設會在 iOS 和 Android 上的項目。 如果您不希望隱藏在 iOS 和 Android 上的分隔線，請設定`SeparatorVisibility`您 ListView 上的屬性。 選項`SeparatorVisibility`是：

* **預設**-iOS 和 Android 上顯示的分隔線。
* **無**-隱藏所有平台上的分隔符號。

預設的可見性：

C#:

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "使用預設資料列分隔符號的 ListView")

None:

C#:

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "不含資料列分隔符號的 ListView")

您也可以設定透過分隔線的色彩`SeparatorColor`屬性：

C#:

```csharp
SepratorDemoListView.SeparatorColor = Color.Green;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "具有綠色資料列分隔符號的 ListView")

> [!NOTE]
> 設定這些屬性在 Android 上載入之後`ListView`帶來大量的效能。

<a name="Row_Heights" />

## <a name="row-heights"></a>資料列高度
根據預設，在 ListView 中的所有資料列具有相同的高度。 ListView 包含可用來變更該行為的兩個屬性：

- `HasUnevenRows` &ndash; `true`/`false` 值，資料列有不同的高度設為`true`。 預設值為 `false`。
- `RowHeight` &ndash; 設定高度的每個資料列`HasUnevenRows`是`false`。

您可以藉由設定中設定的所有資料列高度`RowHeight`屬性上的`ListView`。

### <a name="custom-fixed-row-height"></a>自訂的固定資料列高度

C#:

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "具有固定的資料列高度的 ListView")


### <a name="uneven-rows"></a>不平均的資料列

如果您想要個別資料列都具有不同的高度，您可以設定`HasUnevenRows`屬性設`true`。
請注意，資料列高度也不需要手動設定一次`HasUnevenRows`已設為`true`，因為由 Xamarin.Forms 會自動計算高度。


C#:

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "以不平均的資料列的 ListView")

### <a name="runtime-resizing-of-rows"></a>執行階段調整大小的資料列

個別`ListView`資料列可以以程式設計方式調整大小在執行階段，但前提`HasUnevenRows`屬性設定為`true`。 [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法會更新資料格的大小，即使它沒有目前可見的如下列程式碼範例所示：

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

`OnImageTapped`事件處理常式會執行以回應[ `Image` ](xref:Xamarin.Forms.Image)點選的資料格中所，並增加的大小`Image`顯示在資料格中，以便輕鬆地檢視它。

![](customizing-list-appearance-images/dynamic-row-resizing.png "使用調整大小的執行階段資料列的 ListView")

請注意，有強式的效能降低可能如果這項功能過度使用而使。



## <a name="related-links"></a>相關連結

- [群組 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [自訂轉譯器檢視 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [動態調整大小的資料列 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-dynamicunevenlistcells)
- [1.4 的版本資訊](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 的版本資訊](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
