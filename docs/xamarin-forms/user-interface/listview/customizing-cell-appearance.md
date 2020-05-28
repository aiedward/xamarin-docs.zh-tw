---
title: ''
description: 本文將探討在應用程式中呈現資料的選項 Xamarin.Forms ，同時利用 ListView 控制項的便利性。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cdede547e3ef7cf9f7b6d89751c7476a2ce66d3d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129008"
---
# <a name="customizing-listview-cell-appearance"></a>自訂 ListView 儲存格外觀

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 類別是用來呈現可透過使用元素自訂的可滾動清單 `ViewCell` 。 `ViewCell`元素可以顯示文字和影像、表示 true/false 狀態，以及接收使用者輸入。

## <a name="built-in-cells"></a>內建資料格
Xamarin.Forms隨附適用于許多應用程式的內建資料格：

- [`TextCell`](#textcell)控制項用於以選擇性的第二行顯示詳細資料文字的文字。
- [`ImageCell`](#imagecell)控制項類似于， `TextCell` 但包含文字左邊的影像。
- `SwitchCell`控制項可用來呈現和捕捉開啟/關閉或 true/false 狀態。
- `EntryCell`控制項可用來呈現使用者可以編輯的文字資料。

[`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell)和控制項在的 [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) 內容中較為常用 [`TableView`](~/xamarin-forms/user-interface/tableview.md) 。

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell)這是用來顯示文字的儲存格，選擇性地以第二行作為詳細資料文字。 下列螢幕擷取畫面顯示 `TextCell` iOS 和 Android 上的專案：

![](customizing-cell-appearance-images/text-cell-default.png "Default TextCell Example")

TextCells 在執行時間會轉譯為原生控制項，因此相較于自訂，效能會非常理想 `ViewCell` 。 TextCells 是可自訂的，可讓您設定下列屬性：

- `Text`&ndash;以大字型顯示在第一行的文字。
- `Detail`&ndash;以較小的字型顯示在第一行下方的文字。
- `TextColor`&ndash;文字的色彩。
- `DetailColor`&ndash;詳細資料文字的色彩

下列螢幕擷取畫面顯示 `TextCell` 具有自訂色彩屬性的專案：

![](customizing-cell-appearance-images/text-cell-custom.png "Custom TextCell Example")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell)就像一樣 `TextCell` ，可以用來顯示文字和次要詳細資料文字，並使用每個平臺的原生控制項提供絕佳的效能。 `ImageCell`與不同之處在于 `TextCell` ，它會在文字左邊顯示影像。

下列螢幕擷取畫面顯示 `ImageCell` iOS 和 Android 上的專案：「![預設 ImageCell 範例](customizing-cell-appearance-images/image-cell-default.png "預設 ImageCell 範例")」

`ImageCell`當您需要顯示具有視覺外觀的資料清單（例如連絡人或電影清單）時，非常有用。 `ImageCell`可自訂，可讓您設定：

- `Text`&ndash;以大字型顯示在第一行的文字
- `Detail`&ndash;在第一行下方以較小的字型顯示的文字
- `TextColor`&ndash;文字的色彩
- `DetailColor`&ndash;詳細資料文字的色彩
- `ImageSource`&ndash;要顯示在文字旁邊的影像

下列螢幕擷取畫面顯示 `ImageCell` 具有自訂色彩屬性的專案：「![自訂 ImageCell 範例](customizing-cell-appearance-images/image-cell-custom.png "自訂 ImageCell 範例")」

## <a name="custom-cells"></a>自訂資料格
自訂資料格可讓您建立內建資料格不支援的資料格版面配置。 例如，您可能會想要呈現具有相同權數之兩個標籤的資料格。 `TextCell`因為 `TextCell` 有一個較小的標籤，所以不會有足夠的空間。 大部分的資料格自訂會新增額外的唯讀資料（例如，其他標籤、影像或其他顯示資訊）。

所有的自訂資料格都必須衍生自 [`ViewCell`](xref:Xamarin.Forms.ViewCell) ，這是所有內建資料格類型所使用的相同基類。

Xamarin.Forms提供控制項的快取[行為](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy)， `ListView` 可改善某些自訂資料格類型的滾動效能。

下列螢幕擷取畫面顯示自訂資料格的範例：

![「自訂資料格範例」](customizing-cell-appearance-images/custom-cell.png "自訂資料格範例")

### <a name="xaml"></a>XAML
先前的螢幕擷取畫面中所示的自訂資料格，可以使用下列 XAML 來建立：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="demoListView.ImageCellPage">
    <ContentPage.Content>
        <ListView  x:Name="listView">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout BackgroundColor="#eee"
                        Orientation="Vertical">
                            <StackLayout Orientation="Horizontal">
                                <Image Source="{Binding image}" />
                                <Label Text="{Binding title}"
                                TextColor="#f35e20" />
                                <Label Text="{Binding subtitle}"
                                HorizontalOptions="EndAndExpand"
                                TextColor="#503026" />
                            </StackLayout>
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

XAML 的運作方式如下：

- 自訂資料格會嵌套于內部的內 `DataTemplate` `ListView.ItemTemplate` 。 這與使用任何內建資料格的程式相同。
- `ViewCell`這是自訂資料格的類型。 元素的子系 `DataTemplate` 必須是或衍生自 `ViewCell` 類別。
- 在中 `ViewCell` ，版面配置可以由任何版面配置來管理 Xamarin.Forms 。 在此範例中，配置是由所管理 `StackLayout` ，允許自訂背景色彩。

> [!NOTE]
> 可系結的任何屬性都可以系結至 `StackLayout` 自訂資料格內。 不過，XAML 範例中不會顯示這項功能。

### <a name="code"></a>程式碼

您也可以在程式碼中建立自訂資料格。 首先，必須建立衍生自的自訂類別 `ViewCell` ：

```csharp
public class CustomCell : ViewCell
    {
        public CustomCell()
        {
            //instantiate each of our views
            var image = new Image ();
            StackLayout cellWrapper = new StackLayout ();
            StackLayout horizontalLayout = new StackLayout ();
            Label left = new Label ();
            Label right = new Label ();

            //set bindings
            left.SetBinding (Label.TextProperty, "title");
            right.SetBinding (Label.TextProperty, "subtitle");
            image.SetBinding (Image.SourceProperty, "image");

            //Set properties for desired design
            cellWrapper.BackgroundColor = Color.FromHex ("#eee");
            horizontalLayout.Orientation = StackOrientation.Horizontal;
            right.HorizontalOptions = LayoutOptions.EndAndExpand;
            left.TextColor = Color.FromHex ("#f35e20");
            right.TextColor = Color.FromHex ("503026");

            //add views to the view hierarchy
            horizontalLayout.Children.Add (image);
            horizontalLayout.Children.Add (left);
            horizontalLayout.Children.Add (right);
            cellWrapper.Children.Add (horizontalLayout);
            View = cellWrapper;
        }
    }
```

在頁面的函式中，ListView 的 `ItemTemplate` 屬性會設定為 `DataTemplate` 具有 `CustomCell` 指定類型的：

```csharp
public partial class ImageCellPage : ContentPage
    {
        public ImageCellPage ()
        {
            InitializeComponent ();
            listView.ItemTemplate = new DataTemplate (typeof(CustomCell));
        }
    }
```

### <a name="binding-context-changes"></a>系結內容變更

當系結至自訂資料格類型的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 實例時，顯示值的 UI 控制項 `BindableProperty` 應該使用覆 [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) 寫來設定要顯示在每個儲存格中的資料，而不是儲存格的函式，如下列程式碼範例所示：

```csharp
public class CustomCell : ViewCell
{
    Label nameLabel, ageLabel, locationLabel;

    public static readonly BindableProperty NameProperty =
        BindableProperty.Create ("Name", typeof(string), typeof(CustomCell), "Name");
    public static readonly BindableProperty AgeProperty =
        BindableProperty.Create ("Age", typeof(int), typeof(CustomCell), 0);
    public static readonly BindableProperty LocationProperty =
        BindableProperty.Create ("Location", typeof(string), typeof(CustomCell), "Location");

    public string Name
    {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age
    {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location
    {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null)
        {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

[`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged)當事件引發時，會呼叫覆寫 [`BindingContextChanged`](xref:Xamarin.Forms.BindableObject.BindingContextChanged) ，以回應 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 變更的屬性值。 因此，當 `BindingContext` 變更時，顯示值的 UI 控制項 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 應該設定其資料。 請注意，您 `BindingContext` 應該檢查是否有 `null` 值，因為這可由設定 Xamarin.Forms 以進行垃圾收集，而這會導致 `OnBindingContextChanged` 呼叫覆寫。

或者，UI 控制項可以系結至 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 實例以顯示其值，這會移除覆寫方法的需求 `OnBindingContextChanged` 。

> [!NOTE]
> 覆寫時 `OnBindingContextChanged` ，請確定 `OnBindingContextChanged` 已呼叫基類的方法，讓已註冊的委派能接收 `BindingContextChanged` 事件。

在 XAML 中，可以將自訂資料格類型系結至資料，如下列程式碼範例所示：

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

這會將 `Name` 實例中的、和可系結屬性系結 `Age` `Location` `CustomCell` 至 `Name` `Age` `Location` 基礎集合中每個物件的、和屬性。

下列程式碼範例顯示 c # 中的對等系結：

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView
{
    ItemsSource = people,
    ItemTemplate = customCell
};
```

在 iOS 和 Android 上，如果 [`ListView`](xref:Xamarin.Forms.ListView) 是回收元素，而自訂資料格使用自訂轉譯器，則自訂轉譯器必須正確地執行屬性變更通知。 當重複使用資料格時，其屬性值將會在系結內容更新為可用儲存格的時變更，並 `PropertyChanged` 引發事件。 如需詳細資訊，請參閱[自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。 如需有關資料格回收的詳細資訊，請參閱快取[策略](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy)。

## <a name="related-links"></a>相關連結

- [內建資料格（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [自訂資料格（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [系結內容已變更（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
