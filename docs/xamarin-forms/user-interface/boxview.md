---
title: Xamarin.Forms BoxView
description: 本文說明如何使用彩色的矩形裝飾、 圖形和 Xamarin.Forms 應用程式中的互動。
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2017
ms.openlocfilehash: edb2785362f2cc7377d9adb0c1a89a6fa2b08111
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244311"
---
# <a name="xamarinforms-boxview"></a>Xamarin.Forms BoxView

[`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) 呈現指定的寬度、 高度和色彩的簡單矩形。 您可以使用`BoxView`裝飾，基本圖形，以及透過觸控使用者互動。

Xamarin.Forms 沒有內建向量圖形系統，因為`BoxView`補償的協助。 某些此發行項使用中所述的範例程式`BoxView`轉譯圖形。 `BoxView`可以調整大小以類似於特定的寬度和粗細的線條和旋轉角度使用任何`Rotation`屬性。

雖然`BoxView`可以模擬簡單的圖形，您可能想要調查[Xamarin.Forms 中使用的 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)更複雜的圖形需求。

本文將討論下列主題：

- **[設定 BoxView 色彩和大小](#colorandsize)** &ndash;設定`BoxView`屬性。
- **[呈現文字裝飾](#textdecorations)** &ndash;使用`BoxView`轉譯線條。
- **[列出使用 BoxView 色彩](#listingcolors)** &ndash;顯示所有系統色彩`ListView`。
- **[玩遊戲生命週期由子類別化 BoxView](#subclassing)**  &ndash;實作知名的行動電話通訊自動化。
- **[建立數位時鐘](#digitalclock)** &ndash;模擬點矩陣顯示。
- **[建立類比時鐘](#analogclock)** &ndash;轉換，並以動畫顯示`BoxView`項目。

<a name="colorandsize" />

## <a name="setting-boxview-color-and-size"></a>設定 BoxView 色彩和大小

通常您會設定下列三個屬性`BoxView`:

- [`Color`](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) 若要設定其色彩。
- [`WidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) 若要設定的寬度`BoxView`以與裝置無關的單位。
- [`HeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) 若要設定的高度`BoxView`。

`Color`屬性屬於型別`Color`; 這個屬性可以設定任何`Color`值，包括 141 靜態唯讀欄位的命名色彩範圍是依字母順序從`AliceBlue`至`YellowGreen`。

`WidthRequest`和`HeightRequest`屬性只能播放一個角色，如果`BoxView`是*受條件約束*配置中。 時，這種情況，版面配置容器需要知道子較大者，例如，當`BoxView`子系中的自動調整大小的資料格的`Grid`版面配置。 A`BoxView`時，也不受限制其`HorizontalOptions`和`VerticalOptions`以外屬性設定為值`LayoutOptions.Fill`。 如果`BoxView`不受限制，但`WidthRequest`和`HeightRequest`沒有設定屬性，則寬度或高度會設為預設值 40 個單位或大約在行動裝置上的 1/4 英吋。

`WidthRequest`和`HeightRequest`屬性會被忽略，如果`BoxView`是*限制*在配置中，案例版面配置容器中套用自己大小`BoxView`。

A`BoxView`可以限制一個維度中，並在其他未受限。 例如，如果`BoxView`子系的垂直`StackLayout`，垂直維度的`BoxView`是未受限和其中的水平維度通常會受到限制。 但有例外狀況，該水平維度： 如果`BoxView`具有其`HorizontalOptions`屬性設定為其他項目以外`LayoutOptions.Fill`，然後水平維度也不受限制。 也可能會`StackLayout`本身有未受限制的水平維度，在此情況下`BoxView`也會以水平方式不受限制。

[ **BasicBoxView** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)範例會顯示未受限一英吋的方形`BoxView`其頁面中心：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center"
             HorizontalOptions="Center" />

</ContentPage>
```

以下是結果：

[![基本 BoxView](boxview-images/basicboxview-small.png "基本 BoxView")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

如果`VerticalOptions`和`HorizontalOptions`屬性會從`BoxView`標記，或設為`Fill`，然後在`BoxView`變成的頁面上，大小限制，並展開以填滿頁面。

A`BoxView`也可以是子系`AbsoluteLayout`。 在此情況下，位置和大小`BoxView`都使用設定`LayoutBounds`附加可繫結的屬性。 `AbsoluteLayout`文章中有討論[ **AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md)。

您會看到上述所有情況，請依照下列範例程式中的範例。

<a name="textdecorations" />

## <a name="rendering-text-decorations"></a>呈現文字裝飾

您可以使用`BoxView`水平和垂直線條的形式在頁面上加入一些簡單的裝飾。 [ **[Textdecoration]** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)範例為其示範。 所有程式的視覺效果都定義在**MainPage.xaml**檔案，其中包含數個`Label`和`BoxView`中的項目`StackLayout`如下所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:TextDecoration"
             x:Class="TextDecoration.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Black" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ScrollView Margin="15">
        <StackLayout>

            ···

        </StackLayout>
    </ScrollView>
</ContentPage>
```

所有後面的標記都是子系`StackLayout`。 這個標記包含數種類型的裝飾`BoxView`搭配使用的項目`Label`項目：

[![文字裝飾](boxview-images/textdecoration-small.png "文字裝飾")](boxview-images/textdecoration-large.png#lightbox "文字裝飾")

在頁面頂端的風格的標頭達成與`AbsoluteLayout`，其子系有四個`BoxView`項目和`Label`，則所有的可指派的特定位置和大小：

```xaml
<AbsoluteLayout>
    <BoxView AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
    <BoxView AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
    <Label Text="Stylish Header"
           FontSize="24"
           AbsoluteLayout.LayoutBounds="30, 25, AutoSize, AutoSize"/>
</AbsoluteLayout>
```

在 XAML 檔案中，`AbsoluteLayout`後面`Label`使用格式描述文字`AbsoluteLayout`。

您可以藉由封閉兩者加底線的文字字串`Label`和`BoxView`中`StackLayout`具有其`HorizontalOptions`值設定為其他項目以外`Fill`。 寬度`StackLayout`然後受到寬度`Label`，再加上該寬度`BoxView`。 `BoxView`指派只明確高度：

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

您無法使用這項技術，較長的文字字串或段落內的個別文字加上底線。

它也可使用`BoxView`類似於 HTML `hr` （水平規則） 項目。 直接讓寬度`BoxView`由其父容器，在此情況下是`StackLayout`:

```xaml
<BoxView HeightRequest="3" />
```

最後，封入同時在一段文字的一邊繪製垂直線條`BoxView`和`Label`中在水平`StackLayout`。 在此情況下的高度`BoxView`的高度相同`StackLayout`，這由的高度`Label`:

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
/StackLayout>
```
<a name="listingcolors" />

## <a name="listing-colors-with-boxview"></a>列出 BoxView 的色彩

`BoxView`是方便顯示色彩。 此程式會使用`ListView`列出所有的公用靜態唯讀欄位 Xamarin.Forms`Color`結構：

[![ListView 色彩](boxview-images/listviewcolors-small.png "ListView 色彩")](boxview-images/listviewcolors-large.png#lightbox "ListView 色彩")

[ **ListViewColors** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ListViewColors/)程式包含類別，名為`NamedColor`。 靜態建構函式會使用反映來存取所有的欄位`Color`結構，並建立`NamedColor`針對每個物件。 這些儲存在靜態`All`屬性：

```csharp
public class NamedColor
{
    // Instance members.
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    // Static members.
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields ())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof (Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                               (int)(255 * color.R),
                                               (int)(255 * color.G),
                                               (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }
}
```

程式視覺效果所述的 XAML 檔案。 `ItemsSource`屬性`ListView`設為靜態`NamedColor.All`屬性，這表示`ListView`會顯示所有個人`NamedColor`物件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ListViewColors"
             x:Class="ListViewColors.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="10, 20, 10, 0" />
            <On Platform="Android, UWP" Value="10, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ListView SeparatorVisibility="None"
              ItemsSource="{x:Static local:NamedColor.All}">
        <ListView.RowHeight>
            <OnPlatform x:TypeArguments="x:Int32">
                <On Platform="iOS, Android" Value="80" />
                <On Platform="UWP" Value="90" />
            </OnPlatform>
        </ListView.RowHeight>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ContentView Padding="5">
                        <Frame OutlineColor="Accent"
                               Padding="10">
                            <StackLayout Orientation="Horizontal">
                                <BoxView Color="{Binding Color}"
                                         WidthRequest="50"
                                         HeightRequest="50" />
                                <StackLayout>
                                    <Label Text="{Binding FriendlyName}"
                                           FontSize="22"
                                           VerticalOptions="StartAndExpand" />
                                    <Label Text="{Binding RgbDisplay, StringFormat='RGB = {0}'}"
                                           FontSize="16"
                                           VerticalOptions="CenterAndExpand" />
                                </StackLayout>
                            </StackLayout>
                        </Frame>
                    </ContentView>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

`NamedColor`來格式化的物件`ViewCell`物件所設的資料範本`ListView`。 此範本包括`BoxView`其`Color`屬性繫結至`Color`屬性`NamedColor`物件。

<a name="subclassing" />

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>依子類別化 BoxView 生命遊戲玩法

遊戲的生命週期是行動電話通訊的自動化的數學家 John Conway 和 popularized 的頁面中*科學 American* 1970年中。 維基百科文章提供了絕佳的簡介[生命週期 Conway 遊戲](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)。

Xamarin.Forms [ **GameOfLife** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/)程式定義的類別，名為`LifeCell`衍生自`BoxView`。 這個類別會封裝生命週期遊戲中的個別資料格的邏輯：

```csharp
class LifeCell : BoxView
{
    bool isAlive;

    public event EventHandler Tapped;

    public LifeCell()
    {
        BackgroundColor = Color.White;

        TapGestureRecognizer tapGesture = new TapGestureRecognizer();
        tapGesture.Tapped += (sender, args) =>
        {
            Tapped?.Invoke(this, EventArgs.Empty);
        };
        GestureRecognizers.Add(tapGesture);
    }

    public int Col { set; get; }

    public int Row { set; get; }

    public bool IsAlive
    {
        set
        {
            if (isAlive != value)
            {
                isAlive = value;
                BackgroundColor = isAlive ? Color.Black : Color.White;
            }
        }
        get
        {
            return isAlive;
        }
    }
}
```

`LifeCell` 將三個屬性至`BoxView`:`Col`和`Row`屬性儲存的資料格在方格中，位置和`IsAlive`屬性會指出其狀態。 `IsAlive`屬性也會設定`Color`屬性`BoxView`為黑色，如果儲存格，和白色如果儲存格不在作用。

`LifeCell` 也會安裝`TapGestureRecognizer`可讓使用者藉由點選它們切換資料格的狀態。 類別會將轉譯`Tapped`事件到它自己的筆勢辨識器從`Tapped`事件。

**GameOfLife**程式也包含`LifeGrid`類別，將封裝很多，遊戲邏輯和`MainPage`處理程式的視覺效果的類別。 這些包括描述遊戲的規則覆疊。 以下是程式中顯示幾個數百動作`LifeCell`頁面上的物件：

[![遊戲的使用壽命到期](boxview-images/gameoflife-small.png "遊戲的使用壽命到期")](boxview-images/gameoflife-large.png#lightbox "生命週期的遊戲")

<a name="digitalclock" />

## <a name="creating-a-digital-clock"></a>建立數位時鐘

[ **DotMatrixClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock/)程式建立 210`BoxView`模擬的舊式的 5-7 點矩陣顯示點的項目。 您可以讀取的時間，以直向或橫向模式，但是它是大橫印：

[![點陣時鐘](boxview-images/dotmatrixclock-small.png "點陣時鐘")](boxview-images/dotmatrixclock-large.png#lightbox "點陣時鐘")

XAML 檔案稍微大於未執行個體化`AbsoluteLayout`用於時鐘：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DotMatrixClock"
             x:Class="DotMatrixClock.MainPage"
             Padding="10"
             SizeChanged="OnPageSizeChanged">

    <AbsoluteLayout x:Name="absoluteLayout"
                    VerticalOptions="Center" />
</ContentPage>
```

所有其他項目就會發生在程式碼後置檔案中。 點陣顯示邏輯大幅簡化了描述對應到 10 的數字和冒號的每個點的數個陣列的定義：

```csharp
public partial class MainPage : ContentPage
{
    // Total dots horizontally and vertically.
    const int horzDots = 41;
    const int vertDots = 7;

    // 5 x 7 dot matrix patterns for 0 through 9.
    static readonly int[, ,] numberPatterns = new int[10, 7, 5]
    {
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 1, 1}, { 1, 0, 1, 0, 1},
            { 1, 1, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 0, 0}, { 0, 1, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0},
            { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0},
            { 0, 0, 1, 0, 0}, { 0, 1, 0, 0, 0}, { 1, 1, 1, 1, 1}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 0, 1, 0},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 0, 1, 0}, { 0, 0, 1, 1, 0}, { 0, 1, 0, 1, 0}, { 1, 0, 0, 1, 0},
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 0, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0}, { 0, 0, 0, 0, 1},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 1, 0}, { 0, 1, 0, 0, 0}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0},
            { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 1},
            { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 1, 1, 0, 0}
        },
    };

    // Dot matrix pattern for a colon.
    static readonly int[,] colonPattern = new int[7, 2]
    {
        { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }
    };

    // BoxView colors for on and off.
    static readonly Color colorOn = Color.Red;
    static readonly Color colorOff = new Color(0.5, 0.5, 0.5, 0.25);

    // Box views for 6 digits, 7 rows, 5 columns.
    BoxView[, ,] digitBoxViews = new BoxView[6, 7, 5];

    ···

}
```

這些欄位都以此的三維陣列`BoxView`儲存六個位數的點模式的項目。

建構函式建立所有`BoxView`元素的數字和冒號，以及初始化`Color`屬性`BoxView`冒號的項目：

```csharp
public partial class MainPage : ContentPage
{

    ···

    public MainPage()
    {
        InitializeComponent();

        // BoxView dot dimensions.
        double height = 0.85 / vertDots;
        double width = 0.85 / horzDots;

        // Create and assemble the BoxViews.
        double xIncrement = 1.0 / (horzDots - 1);
        double yIncrement = 1.0 / (vertDots - 1);
        double x = 0;

        for (int digit = 0; digit < 6; digit++)
        {
            for (int col = 0; col < 5; col++)
            {
                double y = 0;

                for (int row = 0; row < 7; row++)
                {
                    // Create the digit BoxView and add to layout.
                    BoxView boxView = new BoxView();
                    digitBoxViews[digit, row, col] = boxView;
                    absoluteLayout.Children.Add(boxView,
                                                new Rectangle(x, y, width, height),
                                                AbsoluteLayoutFlags.All);
                    y += yIncrement;
                }
                x += xIncrement;
            }
            x += xIncrement;

            // Colons between the hours, minutes, and seconds.
            if (digit == 1 || digit == 3)
            {
                int colon = digit / 2;

                for (int col = 0; col < 2; col++)
                {
                    double y = 0;

                    for (int row = 0; row < 7; row++)
                    {
                        // Create the BoxView and set the color.
                        BoxView boxView = new BoxView
                            {
                                Color = colonPattern[row, col] == 1 ?
                                            colorOn : colorOff
                            };
                        absoluteLayout.Children.Add(boxView,
                                                    new Rectangle(x, y, width, height),
                                                    AbsoluteLayoutFlags.All);
                        y += yIncrement;
                    }
                    x += xIncrement;
                }
                x += xIncrement;
            }
        }

        // Set the timer and initialize with a manual call.
        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimer);
        OnTimer();
    }

    ···

}
```

此程式會使用的相對位置和調整大小功能`AbsoluteLayout`。 每個的高度與寬度`BoxView`設定為小數的值，特別是 85 %1 除以水平和垂直點的數目。 位置也會設定為小數值。

因為所有的位置和大小的大小總計的相對`AbsoluteLayout`、`SizeChanged`只需要設定網頁的處理常式`HeightRequest`的`AbsoluteLayout`:

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnPageSizeChanged(object sender, EventArgs args)
    {
        // No chance a display will have an aspect ratio > 41:7
        absoluteLayout.HeightRequest = vertDots * Width / horzDots;
    }

    ···

}
```

寬度`AbsoluteLayout`會自動設定，因為它會自動縮放以完整頁面的寬度。

中的最後一個程式碼`MainPage`類別處理計時器回呼和色彩的每一個數字的點。 多維陣列的程式碼後置檔案開頭的定義有助於讓這個邏輯程式的最簡單的一部分：

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimer()
    {
        DateTime dateTime = DateTime.Now;

        // Convert 24-hour clock to 12-hour clock.
        int hour = (dateTime.Hour + 11) % 12 + 1;

        // Set the dot colors for each digit separately.
        SetDotMatrix(0, hour / 10);
        SetDotMatrix(1, hour % 10);
        SetDotMatrix(2, dateTime.Minute / 10);
        SetDotMatrix(3, dateTime.Minute % 10);
        SetDotMatrix(4, dateTime.Second / 10);
        SetDotMatrix(5, dateTime.Second % 10);
        return true;
    }

    void SetDotMatrix(int index, int digit)
    {
        for (int row = 0; row < 7; row++)
            for (int col = 0; col < 5; col++)
            {
                bool isOn = numberPatterns[digit, row, col] == 1;
                Color color = isOn ? colorOn : colorOff;
                digitBoxViews[index, row, col].Color = color;
            }
    }
}
```
<a name="analogclock" />

## <a name="creating-an-analog-clock"></a>建立類比時鐘

點陣時鐘可能看似明顯應用程式的`BoxView`，但`BoxView`項目也都能實現類比時鐘：

[![BoxView 時鐘](boxview-images/boxviewclock-small.png "BoxView 時鐘")](boxview-images/boxviewclock-large.png#lightbox "BoxView 時鐘")

中的所有視覺效果[ **BoxViewClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/)程式是子系`AbsoluteLayout`。 這些項目調整大小使用`LayoutBounds`附加屬性，並使用`Rotation`屬性。

三個`BoxView`時鐘指針的項目在 XAML 檔案中，具現化，但未定位或大小：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BoxViewClock"
             x:Class="BoxViewClock.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <AbsoluteLayout x:Name="absoluteLayout"
                    SizeChanged="OnAbsoluteLayoutSizeChanged">

        <BoxView x:Name="hourHand"
                 Color="Black" />

        <BoxView x:Name="minuteHand"
                 Color="Black" />

        <BoxView x:Name="secondHand"
                 Color="Black" />
    </AbsoluteLayout>
</ContentPage>
```

建構函式的程式碼後置檔案會具現化 60`BoxView`周圍的時鐘刻度的項目：

```csharp
public partial class MainPage : ContentPage
{

    ···

    BoxView[] tickMarks = new BoxView[60];

    public MainPage()
    {
        InitializeComponent();

        // Create the tick marks (to be sized and positioned later).
        for (int i = 0; i < tickMarks.Length; i++)
        {
            tickMarks[i] = new BoxView { Color = Color.Black };
            absoluteLayout.Children.Add(tickMarks[i]);
        }

        Device.StartTimer(TimeSpan.FromSeconds(1.0 / 60), OnTimerTick);
    }

    ···

}
```

大小及位置的所有`BoxView`項目，就會發生在`SizeChanged`處理常式`AbsoluteLayout`。 內部類別的一些結構稱為`HandParams`描述三個相對於總大小的時鐘指針的每個的大小：

```csharp
public partial class MainPage : ContentPage
{
    // Structure for storing information about the three hands.
    struct HandParams
    {
        public HandParams(double width, double height, double offset) : this()
        {
            Width = width;
            Height = height;
            Offset = offset;
        }

        public double Width { private set; get; }   // fraction of radius
        public double Height { private set; get; }  // ditto
        public double Offset { private set; get; }  // relative to center pivot
    }

    static readonly HandParams secondParams = new HandParams(0.02, 1.1, 0.85);
    static readonly HandParams minuteParams = new HandParams(0.05, 0.8, 0.9);
    static readonly HandParams hourParams = new HandParams(0.125, 0.65, 0.9);

    ···

 }
```

`SizeChanged`處理常式可判斷置中與半徑`AbsoluteLayout`，，然後調整大小，並置於 60`BoxView`做為刻度標記的項目。 `for`迴圈結束時，會設定`Rotation`每種屬性`BoxView`項目。 在結尾`SizeChanged`處理常式，`LayoutHand`大小和位置的三個時鐘指針來呼叫方法：

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnAbsoluteLayoutSizeChanged(object sender, EventArgs args)
    {
        // Get the center and radius of the AbsoluteLayout.
        Point center = new Point(absoluteLayout.Width / 2, absoluteLayout.Height / 2);
        double radius = 0.45 * Math.Min(absoluteLayout.Width, absoluteLayout.Height);

        // Position, size, and rotate the 60 tick marks.
        for (int index = 0; index < tickMarks.Length; index++)
        {
            double size = radius / (index % 5 == 0 ? 15 : 30);
            double radians = index * 2 * Math.PI / tickMarks.Length;
            double x = center.X + radius * Math.Sin(radians) - size / 2;
            double y = center.Y - radius * Math.Cos(radians) - size / 2;
            AbsoluteLayout.SetLayoutBounds(tickMarks[index], new Rectangle(x, y, size, size));
            tickMarks[index].Rotation = 180 * radians / Math.PI;
        }

        // Position and size the three hands.
        LayoutHand(secondHand, secondParams, center, radius);
        LayoutHand(minuteHand, minuteParams, center, radius);
        LayoutHand(hourHand, hourParams, center, radius);
    }

    void LayoutHand(BoxView boxView, HandParams handParams, Point center, double radius)
    {
        double width = handParams.Width * radius;
        double height = handParams.Height * radius;
        double offset = handParams.Offset;

        AbsoluteLayout.SetLayoutBounds(boxView,
            new Rectangle(center.X - 0.5 * width,
                          center.Y - offset * height,
                          width, height));

        // Set the AnchorY property for rotations.
        boxView.AnchorY = handParams.Offset;
    }

    ···

}
```

`LayoutHand`方法調整大小，並指向直接到 12:00 位置的每一個指針。 方法的結尾`AnchorY`屬性設定為對應至時鐘的中心位置。 這表示旋轉的中心點。

指針會循環使用中計時器回呼函式：

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimerTick()
    {
        // Set rotation angles for hour and minute hands.
        DateTime dateTime = DateTime.Now;
        hourHand.Rotation = 30 * (dateTime.Hour % 12) + 0.5 * dateTime.Minute;
        minuteHand.Rotation = 6 * dateTime.Minute + 0.1 * dateTime.Second;

        // Do an animation for the second hand.
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        secondHand.Rotation = 6 * (dateTime.Second + t);
        return true;
    }
}
```

第二個手動的方式稍有不同的方式處理： 動畫的 easing 函式套用至機械，而不是 smooth 看起來的移動。 上的每個刻度，秒針提取回有點，並再超過其目的地。 這一小段程式碼加入許多移動的真實性。

## <a name="conclusion"></a>結論

`BoxView`看起來可能會在簡單的第一次，而是做為您所見，它可以是相當靈活，並可以幾乎通常只能使用可能會有重現視覺效果向量圖形。 更複雜的圖形，請參閱[Xamarin.Forms 中使用的 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)。


## <a name="related-links"></a>相關連結

- [基本 BoxView （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)
- [文字裝飾 （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)
- [色彩清單方塊 （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox)
- [遊戲的生命週期 （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife)
- [點陣時鐘 （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock)
- [BoxView 時鐘 （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock)
- [BoxView](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)
