---
title: 資料格的外觀
description: 瀏覽同時利用 ListView 的方便性，不論呈現資料的選項。
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: df0e113f0c76ea9bde58da7a7ceccd50edd5b227
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="cell-appearance"></a>資料格的外觀

清單檢視會顯示可捲動清單，您可以透過使用的自訂`ViewCell`s。 `ViewCells` 可以用於顯示文字和影像，表示 true/false 狀態和接收使用者輸入。

有兩種方法來取得想要從 ListView 資料格的外觀：

- **[自訂內建的儲存格](#Built_in_Cells)** &ndash;更容易實作和更佳的效能，但會犧牲自訂。
- **[建立自訂的資料格](#customcells)** &ndash;更多控制的最終結果，但有潛在的效能問題，如果未正確實作。

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>內建資料格
Xamarin.Forms 隨附內建的儲存格可用於許多簡單的應用程式：

- **TextCell** &ndash;顯示文字
- **ImageCell** &ndash;來顯示文字的影像。

兩個額外的儲存格[ `SwitchCell` ](~/xamarin-forms/user-interface/tableview.md#switchcell)和[ `EntryCell` ](~/xamarin-forms/user-interface/tableview.md#entrycell)可供使用，但是它們通常不搭配`ListView`。 請參閱[ `TableView` ](~/xamarin-forms/user-interface/tableview.md)如需有關這些資料格。

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](http://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) 是資料格的顯示文字，並選擇性地使用第二行以詳細資料的文字。

TextCells 轉譯為原生控制項在執行階段，因此效能是很好相較於自訂`ViewCell`。 TextCells 是可自訂，可讓您設定：

- `Text` &ndash; 在第一行，大字型顯示文字。
- `Detail` &ndash; 底下的第一行，以較小的字型顯示文字。
- `TextColor` &ndash; 文字的色彩。
- `DetailColor` &ndash; 詳細資訊文字的色彩

![](customizing-cell-appearance-images/text-cell-default.png "預設 TextCell 範例")

![](customizing-cell-appearance-images/text-cell-custom.png "自訂的 TextCell 範例")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](http://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/)例如`TextCell`、 可以用於顯示文字與次要的詳細資料，並使用每個平台的原生控制項提供絕佳的效能。 `ImageCell` 不同於`TextCell`，它會映像顯示之文字的左邊。

`ImageCell` 您需要顯示一份的視覺外觀，例如連絡人的電影清單的資料時很有用。 ImageCells 是可自訂，可讓您設定：

- `Text` &ndash; 在第一行，大字型顯示文字
- `Detail` &ndash; 較小的字型中的第一行底下顯示的文字
- `TextColor` &ndash; 文字的色彩
- `DetailColor` &ndash; 詳細資訊文字的色彩
- `ImageSource` &ndash; 要顯示的文字旁邊的影像

請注意，當目標為 Windows Phone 8.1、`ImageCell`預設不會縮放影像。 另外請注意，Windows Phone 8.1 是唯一的平台的詳細資料所呈現文字相同的色彩和字型為預設的主要文字。 Windows Phone 8.0 呈現`ImageCell`如下所示：

![](customizing-cell-appearance-images/image-cell-default.png "預設 ImageCell 範例")

![](customizing-cell-appearance-images/image-cell-custom.png "自訂的 ImageCell 範例")

<a name="customcells" />

## <a name="custom-cells"></a>自訂儲存格
當內建資料格不提供必要的配置時，自訂資料格會實作必要的配置。 比方說，您可能要呈現的資料格具有兩個具有相同的權重的標籤。 A`LabelCell`會不足因為`LabelCell`具有較小的一個標籤。 大多數的儲存格自訂新增額外的唯讀資料 （例如額外的標籤、 影像或其他顯示資訊）。

所有自訂儲存格必須衍生自[ `ViewCell` ](http://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)，類型之所有內建的儲存格使用相同的基底類別。

Xamarin.Forms 2 導入新[快取行為](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy)上`ListView`控制項可改善某些類型的自訂儲存格的捲動效能設定。

這是自訂的儲存格的範例：

![](customizing-cell-appearance-images/custom-cell.png "自訂儲存格範例")

### <a name="xaml"></a>XAML
若要建立上述配置 XAML 如下：

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

正在經常進行上面的 XAML。 讓我們將它分解：

- 自訂儲存格是否位於`DataTemplate`，這是內部`ListView.ItemTemplate`。 這是相同的程序使用任何其他資料格。
- `ViewCell` 是自訂的儲存格類型。 子系`DataTemplate`元素必須屬於或衍生自型別`ViewCell`。
- 請注意該內部`ViewCell`，版面配置由管理`StackLayout`。 此配置可讓我們以自訂的背景色彩。 請注意，任何屬性`StackLayout`也就是可繫結可以雖然未在此顯示內自訂儲存格時，繫結。

### <a name="cnum"></a>C&num;

在 C# 中指定的自訂儲存格是更詳細的資訊比 XAML 對等項目。 讓我們來看一下：

首先，來定義自訂儲存格類別，`ViewCell`的基底類別：

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

在您的網頁的建構函式`ListView`，設定 ListView 的`ItemTemplate`屬性至新`DataTemplate`:

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

請注意，建構函式`DataTemplate`採用型別。 Typeof 運算子取得的 CLR 類型`CustomCell`。

<a name="binding-context-changes" />

### <a name="binding-context-changes"></a>繫結內容變更

繫結至自訂儲存格類型時[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)例項，UI 控制項會顯示`BindableProperty`值應該使用[ `OnBindingContextChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.OnBindingContextChanged()/)覆寫設定中顯示資料每個資料格，而不是資料格建構函式，如下列程式碼範例所示：

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

[ `OnBindingContextChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.OnBindingContextChanged()/)覆寫時將會呼叫[ `BindingContextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.BindingContextChanged/)事件引發，以回應的值[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)屬性變更。 因此，當`BindingContext`變更時，UI 控制項會顯示[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)值應該設定其資料。 請注意，`BindingContext`應檢查`null`值，這可以進行記憶體回收，又會導致設定由 Xamarin.Forms`OnBindingContextChanged`覆寫所呼叫。

此外，UI 控制項可以繫結至[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)顯示其值，便不需要覆寫的執行個體`OnBindingContextChanged`方法。

> [!NOTE]
> 在覆寫`OnBindingContextChanged`，請確定基底類別的`OnBindingContextChanged`呼叫方法，使已註冊的委派能接收`BindingContextChanged`事件。

在 XAML 中，繫結至資料的自訂儲存格類型可以達到下列程式碼範例所示：

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

這會將繫結`Name`， `Age`，和`Location`可繫結屬性`CustomCell`執行個體，為`Name`， `Age`，和`Location`基礎的集合中每個物件的屬性。

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

IOS 和 Android 上如果[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)循環項目和自訂資料格會使用自訂轉譯器、 自訂轉譯器必須正確實作屬性變更告知。 重複使用的資料格時繫結內容所使用的資料格，以更新時，會變更其屬性值`PropertyChanged`所引發的事件。 如需詳細資訊，請參閱[自訂 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。 如需儲存格回收的詳細資訊，請參閱[快取策略](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy)。

## <a name="related-links"></a>相關連結

- [內建的儲存格 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [自訂儲存格 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [繫結內容變更 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BindingContextChanged)
