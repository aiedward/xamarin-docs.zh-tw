---
title: 自訂 ListView 外觀
description: 本文說明如何以自訂 Listview Xamarin.Forms 應用程式中使用頁首、 頁尾、 群組和可變高度的資料格。
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: febf712848b81c09a4e25c824acc097e8b65e409
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245136"
---
# <a name="customizing-listview-appearance"></a>自訂 ListView 外觀

`ListView` 已控制整體清單中，除了基礎的呈現方式選項`ViewCell`s。 這些選項包括：

- [**群組**](#Grouping) &ndash; ListView 中的項目，方便瀏覽和改良的組織。
- [**頁首和頁尾**](#Headers_and_Footers) &ndash;開頭和結尾會與其他項目一起捲動檢視中顯示的資訊。
- [**資料列分隔符號**](#Row_Separators) &ndash;顯示或隱藏項目之間的分隔線。
- [**變數的高度資料列**](#Row_Heights) &ndash;依預設，所有資料列都具有相同的高度，但是這可以變更為允許不同的高度，要顯示的資料列。

<a name="Grouping" />

## <a name="grouping"></a>群組
通常，大型資料集可能會變得不便持續捲動清單中呈現時。 啟用群組可以改善使用者經驗，在這些情況下更組織內容，以及啟用平台專屬的控制項巡覽資料更容易。

當群組啟動`ListView`，每個群組中加入標頭資料列。

若要啟用群組：

- 建立一份清單 （群組的清單，每個群組的項目清單）。
- 設定`ListView`的`ItemsSource`此清單。
- 設定`IsGroupingEnabled`為 true。
- 設定[ `GroupDisplayBinding` ](http://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupDisplayBinding/)繫結至群組的屬性，顯示目前已使用群組的標題。
- [選用]設定[ `GroupShortNameBinding` ](http://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupShortNameBinding/)繫結至群組的屬性，顯示正做為群組的簡短名稱。 簡短名稱用於跳躍清單 （在 iOS 上的右邊資料行）。

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

在上述程式碼中，`All`是將提供給我們 ListView 做為繫結來源的清單。 `Title` 和`ShortName`是用於群組標題的屬性。

在這個階段，`All`為空白清單。 新增靜態建構函式，以便在程式啟動會填入清單：

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

上述程式碼中我們也可以呼叫`Add`的項目上`groups`，這是類型的執行個體`PageTypeGroup`。 這可能是因為`PageTypeGroup`繼承自`List<PageModel>`。 這是清單模式有上述清單的範例。

以下是 XAML，用於顯示群組的清單：

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

- 設定`GroupShortNameBinding`至`ShortName`群組類別中定義的內容
- 設定`GroupDisplayBinding`至`Title`群組類別中定義的內容
- 設定`IsGroupingEnabled`為 true
- 變更`ListView`的`ItemsSource`群組清單

### <a name="customizing-grouping"></a>自訂群組

如果已啟用群組清單中，您也可以自訂群組標頭。

類似於如何`ListView`具有`ItemTemplate`來定義資料列的顯示方式，`ListView`具有`GroupHeaderTemplate`。

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
很可能讓 ListView 呈現捲動的頁首和頁尾與清單的項目。 頁首和頁尾可以是文字或更複雜的版面配置的字串。 請注意，這不同於[區段群組](#Grouping)。

您可以設定`Header`及/或`Footer`成簡單字串值，或者您可以設定更複雜的配置。
另外還有`HeaderTemplate`和`FooterTemplate`屬性，可讓您建立更複雜的頁首和頁尾的配置支援資料繫結。

若要建立簡單頁首/頁尾，只想要顯示的文字設定頁首或頁尾內容。 程式碼：

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

![](customizing-list-appearance-images/header-default.png "頁首和頁尾的 ListView")

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

<a name="Row_Separators" />

## <a name="row-separators"></a>資料列分隔符號
之間顯示分隔符號行`ListView`預設會在 iOS 和 Android 上的項目。 如果您想要隱藏在 iOS 和 Android 的分隔線，設定`SeparatorVisibility`您 ListView 上的屬性。 選項`SeparatorVisibility`是：

* **預設**-iOS 和 Android 上顯示分隔線。
* **無**-隱藏所有平台上的分隔符號。

預設值的可見性：

C#: 

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "預設資料列分隔符號的清單檢視")

None:

C#: 

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "沒有資料列分隔符號的清單檢視")

您也可以設定透過分隔線的色彩`SeparatorColor`屬性：

C#: 

```csharp
SepratorDemoListView.SeparatorColor = Color.Green;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "綠色資料列分隔符號的清單檢視")

> [!NOTE]
> 設定其中一個屬性在 Android 上，於載入後`ListView`會產生大量的效能負面影響。

<a name="Row_Heights" />

## <a name="row-heights"></a>資料列高度
根據預設，在 ListView 中的所有資料列具有相同的高度。 ListView 中有兩種屬性，可以用來變更該行為：

- `HasUnevenRows` &ndash; `true`/`false` 值，資料列有不同高度設為`true`。 預設值為 `false`。
- `RowHeight` &ndash; 設定每個高度列時`HasUnevenRows`是`false`。

您可以藉由設定來設定所有資料列的高度`RowHeight`屬性`ListView`。

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


### <a name="uneven-rows"></a>以不平均的資料列

如果您想要個別資料列有不同的高度，您可以設定`HasUnevenRows`屬性`true`。
請注意，資料列高度不需要手動設定一次`HasUnevenRows`已設定為`true`，因為由 Xamarin.Forms 會自動計算高度。


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

個別`ListView`資料列可以在執行階段，但前提是以程式設計方式調整`HasUnevenRows`屬性設定為`true`。 [ `Cell.ForceUpdateSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.ForceUpdateSize()/)方法會更新儲存格的大小，即使它沒有目前可見，如下列程式碼範例所示：

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

`OnImageTapped`事件處理常式會執行以回應[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)點選的資料格中所，並增加大小`Image`資料格中顯示，以便輕鬆地檢視它。

![](customizing-list-appearance-images/dynamic-row-resizing.png "調整大小的執行階段資料列的 ListView")

請注意，強式的效能降低可能如果這項功能過度使用而使。



## <a name="related-links"></a>相關連結

- [群組 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [自訂轉譯器檢視 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [動態調整大小的資料列 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/DynamicUnevenListCells/)
- [1.4 版本資訊](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 版的版本資訊](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
