---
title: 自訂 ListView 儲存格外觀
description: 本文將探討在 Xamarin 中呈現資料的選項，同時利用 ListView 控制項的便利性。
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2019
ms.openlocfilehash: ab54b54c9f2f7d6d7748137ea079439b7c3ddfca
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78917260"
---
# <a name="customizing-listview-cell-appearance"></a>自訂 ListView 儲存格外觀

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

[Xamarin] [`ListView`](xref:Xamarin.Forms.ListView)類別是用來呈現可透過使用 `ViewCell` 元素來自訂的可滾動清單。 `ViewCell` 元素可以顯示文字和影像、表示 true/false 狀態，以及接收使用者輸入。

## <a name="built-in-cells"></a>內建資料格
Xamarin 會隨附適用于許多應用程式的內建資料格：

- [`TextCell`](#textcell)控制項用於以選擇性的第二行顯示詳細資料文字的文字。
- [`ImageCell`](#imagecell)控制項類似于 `TextCell`s，但在文字左側包含影像。
- `SwitchCell` 控制項可用來呈現和捕捉開啟/關閉或 true/false 狀態。
- `EntryCell` 控制項可用來呈現使用者可以編輯的文字資料。

[`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell)和[`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell)控制項在[`TableView`](~/xamarin-forms/user-interface/tableview.md)的內容中較常使用。

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell)是顯示文字的儲存格，選擇性地以第二行作為詳細資料文字。 下列螢幕擷取畫面顯示 iOS 和 Android 上的 `TextCell` 專案：

![](customizing-cell-appearance-images/text-cell-default.png "Default TextCell Example")

TextCells 在執行時間會轉譯為原生控制項，因此相較于自訂的 `ViewCell`，效能非常理想。 TextCells 是可自訂的，可讓您設定下列屬性：

- `Text` &ndash; 以大字型顯示在第一行的文字。
- `Detail` &ndash; 以較小的字型顯示在第一行底下的文字。
- `TextColor` &ndash; 文字的色彩。
- `DetailColor` &ndash; 詳細資料文字的色彩

下列螢幕擷取畫面顯示具有自訂色彩屬性 `TextCell` 專案：

![](customizing-cell-appearance-images/text-cell-custom.png "Custom TextCell Example")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell)（例如 `TextCell`）可用於顯示文字和次要詳細資料文字，並使用每個平臺的原生控制項提供絕佳的效能。 `ImageCell` 與 `TextCell` 不同之處在于，它會在文字左邊顯示一個影像。

下列螢幕擷取畫面顯示 iOS 和 Android 上的 `ImageCell` 專案：「![預設 ImageCell 範例](customizing-cell-appearance-images/image-cell-default.png "預設 ImageCell 範例")」

當您需要顯示具有視覺外觀的資料清單（例如連絡人或電影清單）時，`ImageCell` 非常有用。 `ImageCell`是可自訂的，可讓您設定：

- `Text` &ndash; 以大字型顯示在第一行的文字
- `Detail` &ndash; 第一行底下以較小的字型顯示的文字
- `TextColor` &ndash; 文字的色彩
- `DetailColor` &ndash; 詳細資料文字的色彩
- `ImageSource` &ndash; 要顯示在文字旁邊的影像

下列螢幕擷取畫面顯示具有自訂色彩屬性的 `ImageCell` 專案：「![自訂 ImageCell 範例](customizing-cell-appearance-images/image-cell-custom.png "自訂 ImageCell 範例")」

## <a name="custom-cells"></a>自訂資料格
自訂資料格可讓您建立內建資料格不支援的資料格版面配置。 例如，您可能會想要呈現具有相同權數之兩個標籤的資料格。 `TextCell` 會不夠，因為 `TextCell` 有一個較小的標籤。 大部分的資料格自訂會新增額外的唯讀資料（例如，其他標籤、影像或其他顯示資訊）。

所有自訂資料格都必須衍生自[`ViewCell`](xref:Xamarin.Forms.ViewCell)，這是所有內建資料格類型所使用的相同基類。

Xamarin 會在 `ListView` 控制項上提供快取[行為](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy)，可改善某些自訂資料格類型的滾動效能。

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

- 自訂資料格會嵌套在 `ListView.ItemTemplate`內的 `DataTemplate`內。 這與使用任何內建資料格的程式相同。
- `ViewCell` 是自訂資料格的類型。 `DataTemplate` 專案的子系必須是（或衍生自） `ViewCell` 類別。
- 在 `ViewCell`內，版面配置可以由任何 Xamarin 版面配置來管理。 在此範例中，配置是由 `StackLayout`所管理，允許自訂背景色彩。

> [!NOTE]
> 可系結之 `StackLayout` 的任何屬性，都可以系結至自訂資料格內。 不過，XAML 範例中不會顯示這項功能。

### <a name="code"></a>程式碼

您也可以在程式碼中建立自訂資料格。 首先，必須建立從 `ViewCell` 衍生的自訂類別：

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

在頁面的函式中，ListView 的 `ItemTemplate` 屬性會設定為指定 `CustomCell` 類型的 `DataTemplate`：

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

當系結至自訂資料格類型的[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)實例時，顯示 `BindableProperty` 值的 UI 控制項應該使用[`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged)覆寫來設定要顯示在每個資料格中的資料，而不是儲存格的函式，如下列程式碼範例所示：

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

當[`BindingContextChanged`](xref:Xamarin.Forms.BindableObject.BindingContextChanged)事件引發時，將會呼叫[`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged)覆寫，以回應變更[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)屬性的值。 因此，當 `BindingContext` 變更時，顯示[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)值的 UI 控制項會設定其資料。 請注意，`BindingContext` 應該檢查是否有 `null` 值，因為這可由 Xamarin 設定，以進行垃圾收集，而這會導致呼叫 `OnBindingContextChanged` 覆寫。

或者，UI 控制項可以系結至[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)實例以顯示其值，這會移除覆寫 `OnBindingContextChanged` 方法的需求。

> [!NOTE]
> 覆寫 `OnBindingContextChanged`時，請確定已呼叫基類的 `OnBindingContextChanged` 方法，讓已註冊的委派能接收 `BindingContextChanged` 事件。

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

這會將 `CustomCell` 實例中的 `Name`、`Age`和 `Location` 可系結屬性，系結至基礎集合中每個物件的 `Name`、`Age`和 `Location` 屬性。

下列程式碼範例C#顯示中的對等系結：

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

在 iOS 和 Android 上，如果[`ListView`](xref:Xamarin.Forms.ListView)是回收元素，而自訂資料格使用自訂轉譯器，則自訂轉譯器必須正確地執行屬性變更通知。 當儲存格重複使用時，其屬性值將會在系結內容更新為可用儲存格的時變更，並引發 `PropertyChanged` 事件。 如需詳細資訊，請參閱[自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。 如需有關資料格回收的詳細資訊，請參閱快取[策略](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy)。

## <a name="related-links"></a>相關連結

- [內建資料格（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [自訂資料格（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [系結內容已變更（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
