---
title: 自訂 ListView 儲存格的外觀
description: 本文章探討，同時利用方便的 ListView 控制項將資料呈現在 Xamarin.Forms 應用程式的選項。
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2019
ms.openlocfilehash: ab54b54c9f2f7d6d7748137ea079439b7c3ddfca
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998155"
---
# <a name="customizing-listview-cell-appearance"></a>自訂 ListView 儲存格的外觀

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

[Xamarin [`ListView`](xref:Xamarin.Forms.ListView) ] 類別是用來呈現可以透過`ViewCell`使用專案自訂的可滾動清單。 `ViewCell`元素可以顯示文字和影像、表示 true/false 狀態，以及接收使用者輸入。

## <a name="built-in-cells"></a>內建的儲存格
Xamarin 會隨附適用于許多應用程式的內建資料格：

- [`TextCell`](#textcell)控制項用於以選擇性的第二行顯示詳細資料文字的文字。
- [`ImageCell`](#imagecell)控制項類似`TextCell`于，但包含文字左邊的影像。
- `SwitchCell`控制項可用來呈現和捕捉開啟/關閉或 true/false 狀態。
- `EntryCell`控制項可用來呈現使用者可以編輯的文字資料。

和控制項在的內容[`TableView`](~/xamarin-forms/user-interface/tableview.md)中較為常用。 [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell)

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) 是顯示文字，並選擇性地使用以文字詳細資料的第二行的資料格。 下列螢幕擷取畫面顯示`TextCell` iOS 和 Android 上的專案：

![](customizing-cell-appearance-images/text-cell-default.png "預設 TextCell 範例")

TextCells 轉譯為原生控制項，在執行階段，因此效能非常好相較於自訂`ViewCell`。 TextCells 是可自訂的，可讓您設定下列屬性：

- `Text` &ndash; 第一行中，使用大字型顯示文字。
- `Detail` &ndash; 下方第一行中，以較小的字型顯示文字。
- `TextColor` &ndash; 文字的色彩。
- `DetailColor` &ndash; 詳細資料的文字色彩

下列螢幕擷取畫面顯示`TextCell`具有自訂色彩屬性的專案：

![](customizing-cell-appearance-images/text-cell-custom.png "自訂 TextCell 範例")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell)例如`TextCell`，可用於顯示文字和次要的詳細資料的文字，和使用每個平台的原生控制項提供絕佳的效能。 `ImageCell` 不同於`TextCell`，因為它會映像顯示之文字的左邊。

下列螢幕擷取畫面顯示`ImageCell` iOS 和 Android 上的專案：「![預設 ImageCell 範例]」(customizing-cell-appearance-images/image-cell-default.png "預設 ImageCell 範例")

`ImageCell` 您需要顯示一份資料的視覺外觀，例如連絡人或電影的清單時很有用。 `ImageCell`可自訂，可讓您設定：

- `Text` &ndash; 第一行中，使用大字型顯示文字
- `Detail` &ndash; 下方第一行中，以較小的字型顯示文字
- `TextColor` &ndash; 文字的色彩
- `DetailColor` &ndash; 詳細資料的文字色彩
- `ImageSource` &ndash; 要顯示的文字旁邊的影像

下列螢幕擷取畫面顯示`ImageCell`具有自訂色彩屬性的專案：「![自訂 ImageCell 範例]」(customizing-cell-appearance-images/image-cell-custom.png "自訂 ImageCell 範例")

## <a name="custom-cells"></a>自訂資料格
自訂資料格可讓您建立內建資料格不支援的資料格版面配置。 例如，您可能要使用具有相同的權重的兩個標籤呈現儲存格。 A`TextCell`就是不足因為`TextCell`有一個較小的標籤。 大部分的資料格自訂新增額外的唯讀資料 （例如額外的標籤、 影像或其他顯示的資訊）。

所有自訂儲存格必須衍生自[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，所有的內建的儲存格類型使用的相同基底類別。

Xamarin 會在`ListView`控制項上提供快取[行為](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy)，可改善某些自訂資料格類型的滾動效能。

下列螢幕擷取畫面顯示自訂資料格的範例：

「![自訂資料格範例]」(customizing-cell-appearance-images/custom-cell.png "自訂資料格範例")

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

- 自訂儲存格在巢狀`DataTemplate`，這是內部`ListView.ItemTemplate`。 這與使用任何內建資料格的程式相同。
- `ViewCell` 是自訂的資料格類型。 `DataTemplate`元素的子系必須是或衍生自`ViewCell`類別。
- 在中`ViewCell`，版面配置可以由任何 Xamarin 版面配置來管理。 在此範例中，配置是由`StackLayout`所管理，允許自訂背景色彩。

> [!NOTE]
> 可系結`StackLayout`的任何屬性都可以系結至自訂資料格內。 不過，XAML 範例中不會顯示這項功能。

### <a name="code"></a>程式碼

您也可以在程式碼中建立自訂資料格。 首先， `ViewCell`必須建立衍生自的自訂類別：

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

在頁面的函式中，ListView `ItemTemplate`的屬性會設定`DataTemplate`為具有`CustomCell`指定類型的：

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

### <a name="binding-context-changes"></a>繫結內容變更

繫結至自訂的資料格型別時[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)執行個體，顯示的 UI 控制項`BindableProperty`的值應該使用[ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged)設定中顯示的資料覆寫每個資料格，而不是資料格建構函式，如下列程式碼範例所示：

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

[ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged)覆寫時將會呼叫[ `BindingContextChanged` ](xref:Xamarin.Forms.BindableObject.BindingContextChanged)事件引發時，以回應的值[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)屬性變更。 因此，當`BindingContext`變更時，顯示的 UI 控制項[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)值應該設定其資料。 請注意，`BindingContext`應檢查`null`值，因為這可以進行記憶體回收，這接著會設定由 Xamarin.Forms`OnBindingContextChanged`覆寫呼叫。

或者，UI 控制項可以繫結至[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)執行個體，以顯示其值，這樣就不需要覆寫`OnBindingContextChanged`方法。

> [!NOTE]
> 在覆寫`OnBindingContextChanged`，請確認基底類別的`OnBindingContextChanged`方法呼叫，讓已註冊的委派收到`BindingContextChanged`事件。

在 XAML 中，繫結至資料的自訂資料格類型可以達成下列程式碼範例所示：

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

這會將繫結`Name`， `Age`，並`Location`中的可繫結屬性`CustomCell`執行個體，為`Name`， `Age`，和`Location`基礎集合中每個物件的屬性。

在 C# 中相等的繫結是以下列程式碼範例所示：

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

IOS 和 Android 上，如果[ `ListView` ](xref:Xamarin.Forms.ListView)正在回收處理項目和自訂的儲存格使用的自訂轉譯器、 自訂轉譯器必須正確實作屬性變更通知。 重複使用的儲存格時其屬性值，將繫結內容更新時所使用的資料格，`PropertyChanged`所引發的事件。 如需詳細資訊，請參閱 <<c0> [ 自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。 如需有關儲存格所回收的詳細資訊，請參閱[快取策略](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy)。

## <a name="related-links"></a>相關連結

- [內建的儲存格 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [自訂資料格 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [繫結內容變更 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
