---
title: 自訂 ListView 儲存格的外觀
description: 本文章探討，同時利用方便的 ListView 控制項將資料呈現在 Xamarin.Forms 應用程式的選項。
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 7c19cc0f2d1f72706926d640bb7fad9323a9e17f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53049452"
---
# <a name="customizing-listview-cell-appearance"></a>自訂 ListView 儲存格的外觀

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)

ListView 顯示可捲動清單，您可以使用自訂`ViewCell`s。 `ViewCells` 可以用於顯示文字和影像，表示 true/false 狀態且接收使用者輸入。

有兩種方法來取得您想要從 ListView 儲存格的外觀：

- **[自訂內建的儲存格](#Built_in_Cells)** &ndash;更容易實作和更佳的效能，但會犧牲的自訂功能。
- **[建立自訂的資料格](#customcells)** &ndash;更充分掌控最後的結果，但有可能會有效能問題，如果未正確實作。

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>內建的儲存格
Xamarin.Forms 隨附的許多簡單的應用程式使用的內建資料格：

- **TextCell** &ndash;顯示文字
- **ImageCell** &ndash;顯示文字與影像。

兩個額外的儲存格[ `SwitchCell` ](~/xamarin-forms/user-interface/tableview.md#switchcell)並[ `EntryCell` ](~/xamarin-forms/user-interface/tableview.md#entrycell)可供使用，但它們通常不搭配`ListView`。 請參閱[ `TableView` ](~/xamarin-forms/user-interface/tableview.md)如需有關這些資料格。

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) 是顯示文字，並選擇性地使用以文字詳細資料的第二行的資料格。

TextCells 轉譯為原生控制項，在執行階段，因此效能非常好相較於自訂`ViewCell`。 TextCells 可自訂，可讓您設定：

- `Text` &ndash; 第一行中，使用大字型顯示文字。
- `Detail` &ndash; 下方第一行中，以較小的字型顯示文字。
- `TextColor` &ndash; 文字的色彩。
- `DetailColor` &ndash; 詳細資料的文字色彩

![](customizing-cell-appearance-images/text-cell-default.png "預設 TextCell 範例")

![](customizing-cell-appearance-images/text-cell-custom.png "自訂的 TextCell 範例")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell)例如`TextCell`，可用於顯示文字和次要的詳細資料的文字，和使用每個平台的原生控制項提供絕佳的效能。 `ImageCell` 不同於`TextCell`，因為它會映像顯示之文字的左邊。

`ImageCell` 您需要顯示一份資料的視覺外觀，例如連絡人或電影的清單時很有用。 ImageCells 可自訂，可讓您設定：

- `Text` &ndash; 第一行中，使用大字型顯示文字
- `Detail` &ndash; 下方第一行中，以較小的字型顯示文字
- `TextColor` &ndash; 文字的色彩
- `DetailColor` &ndash; 詳細資料的文字色彩
- `ImageSource` &ndash; 要顯示的文字旁邊的影像

![](customizing-cell-appearance-images/image-cell-default.png "預設 ImageCell 範例")

![](customizing-cell-appearance-images/image-cell-custom.png "自訂的 ImageCell 範例")

<a name="customcells" />

## <a name="custom-cells"></a>自訂資料格
當內建的儲存格未提供必要的配置時，自訂資料格會實作必要的配置。 例如，您可能要使用具有相同的權重的兩個標籤呈現儲存格。 A`TextCell`就是不足因為`TextCell`有一個較小的標籤。 大部分的資料格自訂新增額外的唯讀資料 （例如額外的標籤、 影像或其他顯示的資訊）。

所有自訂儲存格必須衍生自[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，所有的內建的儲存格類型使用的相同基底類別。

Xamarin.Forms 2 導入的新[快取行為](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy)上`ListView`可設定以改善捲動效能對於某些類型的自訂資料格的控制項。

這是自訂的資料格的範例：

![](customizing-cell-appearance-images/custom-cell.png "自訂儲存格範例")

### <a name="xaml"></a>XAML
若要建立上述的版面配置 XAML 如下：

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

上述 XAML 進行的很多。 我們細分：

- 自訂儲存格在巢狀`DataTemplate`，這是內部`ListView.ItemTemplate`。 這是相同的程序，與使用任何其他資料格。
- `ViewCell` 是自訂的資料格類型。 子系`DataTemplate`項目必須是或衍生自類型`ViewCell`。
- 請注意內`ViewCell`，版面配置由`StackLayout`。 此配置可讓我們自訂的背景色彩。 請注意，任何屬性`StackLayout`也就是可繫結可以雖然，未在此處顯示的自訂儲存格，內部繫結。

### <a name="cnum"></a>C&num;

在 C# 中指定自訂的資料格會更詳細的資訊比 XAML 對等項目。 讓我們來看一下：

首先，定義自訂的儲存格類別，`ViewCell`的基底類別：

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

在您建構函式，該網頁具有`ListView`，設定 ListView`ItemTemplate`屬性，以新`DataTemplate`:

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

請注意，建構函式`DataTemplate`採用型別。 Typeof 運算子取得的 CLR 型別`CustomCell`。

<a name="binding-context-changes" />

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

    public string Name {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null) {
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

var listView = new ListView {
    ItemsSource = people,
    ItemTemplate = customCell
};
```

IOS 和 Android 上，如果[ `ListView` ](xref:Xamarin.Forms.ListView)正在回收處理項目和自訂的儲存格使用的自訂轉譯器、 自訂轉譯器必須正確實作屬性變更通知。 重複使用的儲存格時其屬性值，將繫結內容更新時所使用的資料格，`PropertyChanged`所引發的事件。 如需詳細資訊，請參閱 <<c0> [ 自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。 如需有關儲存格所回收的詳細資訊，請參閱[快取策略](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy)。

## <a name="related-links"></a>相關連結

- [內建的儲存格 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [自訂資料格 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [繫結內容變更 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BindingContextChanged)
