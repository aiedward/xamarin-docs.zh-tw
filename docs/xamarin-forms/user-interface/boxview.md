---
title: Xamarin.Forms BoxView
description: 這篇文章說明如何使用彩色的矩形裝飾、 圖形和 Xamarin.Forms 應用程式中的互動。
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2017
ms.openlocfilehash: 813a913c2c2fb27456c9a489c73b16d5892c4b8d
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997048"
---
# <a name="xamarinforms-boxview"></a>Xamarin.Forms BoxView

[`BoxView`](xref:Xamarin.Forms.BoxView) 呈現指定的寬度、 高度和色彩的簡單矩形。 您可以使用`BoxView`裝飾，基本圖形，以及透過觸控使用者互動。

Xamarin.Forms 沒有內建的向量圖形系統，因為`BoxView`補償的協助。 本文章使用中所述的範例程式的一些`BoxView`轉譯圖形。 `BoxView`可以調整大小，以類似於特定的寬度和粗細的線條和再利用任何角度旋轉`Rotation`屬性。

雖然`BoxView`可以模擬簡單的圖形，您可能想要調查[Xamarin.Forms 中的使用 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)更複雜的圖形需求。

這篇文章討論下列主題：

- **[設定 BoxView 色彩和大小](#colorandsize)** &ndash;設定`BoxView`屬性。
- **[轉譯文字裝飾](#textdecorations)** &ndash;使用`BoxView`轉譯線條。
- **[列出色彩 BoxView](#listingcolors)**  &ndash;顯示所有的系統色彩中`ListView`。
- **[玩遊戲生命週期由子類別化 BoxView](#subclassing)**  &ndash;實作著名的行動電話通訊自動化。
- **[建立數位時鐘](#digitalclock)** &ndash;模擬以點矩陣顯示。
- **[建立類比時鐘](#analogclock)** &ndash;轉換和製作動畫`BoxView`項目。

<a name="colorandsize" />

## <a name="setting-boxview-color-and-size"></a>設定 BoxView 色彩和大小

通常您會設定下列三個屬性`BoxView`:

- [`Color`](xref:Xamarin.Forms.BoxView.Color) 若要設定其色彩。
- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 若要設定的寬度`BoxView`以與裝置無關單位。
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 若要設定的高度`BoxView`。

`Color`屬性的類型是`Color`; 的屬性可以設定為任何`Color`的值，包括 141 靜態唯讀欄位的命名色彩範圍是依字母順序，從`AliceBlue`到`YellowGreen`。

`WidthRequest`並`HeightRequest`屬性只扮演的角色，如果`BoxView`是*未受限制*配置中。 這種狀況時，版面配置容器必須知道子系的大小，比方說，當`BoxView`子系中的自動調整大小的資料格`Grid`版面配置。 A`BoxView`時，也不受限制其`HorizontalOptions`並`VerticalOptions`以外的其他屬性設定為值`LayoutOptions.Fill`。 如果`BoxView`是 未受限制，但`WidthRequest`和`HeightRequest`沒有設定屬性，則寬度或高度設定為 40 的單位或大約在行動裝置上的 1/4 英吋的預設值。

`WidthRequest`並`HeightRequest`屬性會被忽略，如果`BoxView`是*限制*在配置中，在其中案例版面配置容器本身的大小會安排`BoxView`。

A`BoxView`可以限制在一個維度中，並在其他未受限制。 比方說，如果`BoxView`子系的垂直`StackLayout`，以垂直維度的`BoxView`是未受限制，而且其水平維度一般條件約束。 但有該水平維度的例外狀況： 如果`BoxView`有其`HorizontalOptions`屬性設定為項目以外的其他`LayoutOptions.Fill`，則水平維度也會不受限制。 此外，也可以針對`StackLayout`本身在此情況下有不受限制的水平維度，`BoxView`也會以水平方式不受限制。

[ **BasicBoxView** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)範例會顯示未受限制一英吋-正方形`BoxView`在其頁面的中心：

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

結果如下：

[![基本 BoxView](boxview-images/basicboxview-small.png "基本 BoxView")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

如果`VerticalOptions`並`HorizontalOptions`屬性會從`BoxView`標記，或者設定`Fill`，則`BoxView`變成受限於頁面的大小，並展開以填滿頁面。

A`BoxView`也可以是子系`AbsoluteLayout`。 在此情況下，位置和大小`BoxView`都是使用設定`LayoutBounds`附加可繫結的屬性。 `AbsoluteLayout`一文所述[ **AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md)。

您會看到範例程式，請依照下列中的所有這些案例的範例。

<a name="textdecorations" />

## <a name="rendering-text-decorations"></a>轉譯文字裝飾

您可以使用`BoxView`頁面的水平和垂直線條形式加入一些簡單的裝飾。 [ **TextDecoration** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)為範例。 中所定義的所有程式的視覺效果**MainPage.xaml**檔案，其中包含數個`Label`並`BoxView`中的項目`StackLayout`如下所示：

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

接下來的標記都的子系`StackLayout`。 此標記包含數種類型的裝飾`BoxView`搭配使用的項目`Label`項目：

[![文字裝飾](boxview-images/textdecoration-small.png "文字裝飾")](boxview-images/textdecoration-large.png#lightbox "文字裝飾")

達成流行的標頭，在頁面頂端`AbsoluteLayout`其子系有四個`BoxView`項目和`Label`，則所有的這是指派特定的位置和大小：

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

在 XAML 檔案中，`AbsoluteLayout`後面接著`Label`使用的格式描述文字`AbsoluteLayout`。

您可以藉由封閉兩者加底線的文字字串`Label`並`BoxView`中`StackLayout`具有其`HorizontalOptions`值以外的其他設定項目為`Fill`。 寬度`StackLayout`然後受到寬度`Label`，然後上套用該寬度`BoxView`。 `BoxView`指派只明確高度：

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

您無法使用這項技術，加上底線較長的文字字串或段落內的個別文字。

您也可使用`BoxView`類似於 HTML `hr` （水平尺規） 項目。 直接讓寬度`BoxView`取決於其父容器，在此情況下是`StackLayout`:

```xaml
<BoxView HeightRequest="3" />
```

最後，封入同時在一段文字的一邊繪製一條垂直線`BoxView`而`Label`水平`StackLayout`。 在此案例中的高度`BoxView`相同的高度`StackLayout`，其受到高度`Label`:

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
</StackLayout>
```
<a name="listingcolors" />

## <a name="listing-colors-with-boxview"></a>列出使用 BoxView 色彩

`BoxView`是方便顯示色彩。 此程式會使用`ListView`若要列出所有的公用靜態唯讀欄位的 Xamarin.Forms`Color`結構：

[![ListView 色彩](boxview-images/listviewcolors-small.png "ListView 色彩")](boxview-images/listviewcolors-large.png#lightbox "ListView 色彩")

[ **ListViewColors** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ListViewColors/)計畫包含類別，名為`NamedColor`。 靜態建構函式會使用反映來存取的所有欄位`Color`結構，並建立`NamedColor`針對每個物件。 這些儲存在靜態`All`屬性：

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

程式視覺效果所述的 XAML 檔案。 `ItemsSource`的屬性`ListView`設定為靜態`NamedColor.All`屬性，這表示`ListView`顯示所有個別`NamedColor`物件：

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

`NamedColor`來格式化的物件`ViewCell`做為資料範本的設定物件`ListView`。 此範本包括`BoxView`其`Color`屬性的繫結至`Color`屬性`NamedColor`物件。

<a name="subclassing" />

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>依子類別化 BoxView 生命遊戲玩法

遊戲生命週期是首創數學家 John Conway 和普及化的頁面中的行動電話通訊 automaton*科學 American*在 1970 年代。 維基百科文章提供了絕佳的簡介[生命週期 Conway 遊戲](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)。

Xamarin.Forms [ **GameOfLife** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/)程式還定義了一個名為類別`LifeCell`衍生自`BoxView`。 這個類別會封裝生命週期的遊戲中個別資料格的邏輯：

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

`LifeCell` 新增三個屬性，以`BoxView`:`Col`並`Row`屬性儲存的資料格在方格中，位置和`IsAlive`屬性會指出其狀態。 `IsAlive`屬性也會設定`Color`屬性`BoxView`設成黑色，如果儲存格已保持運作，而且白色如果儲存格不保持運作。

`LifeCell` 也會安裝`TapGestureRecognizer`以允許使用者藉由點選它們切換資料格的狀態。 類別會將轉譯`Tapped`筆勢辨識器到它自己的事件`Tapped`事件。

**GameOfLife**程式也包含`LifeGrid`類別，將封裝很多，遊戲邏輯和`MainPage`類別會處理程式的視覺效果。 這些包括描述的遊戲規則的覆疊。 以下是顯示幾個數百的作用中的 程式`LifeCell`頁面上的物件：

[![遊戲的生命循環](boxview-images/gameoflife-small.png "生命週期的遊戲")](boxview-images/gameoflife-large.png#lightbox "生命週期的遊戲")

<a name="digitalclock" />

## <a name="creating-a-digital-clock"></a>建立數位時鐘

[ **DotMatrixClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock/)程式會建立 210`BoxView`模擬的舊式的 5-7 點矩陣顯示點的項目。 您可以讀取的時間，以直向或橫向模式中，但較大型架構中：

[![點陣時鐘](boxview-images/dotmatrixclock-small.png "點陣時鐘")](boxview-images/dotmatrixclock-large.png#lightbox "點陣時鐘")

XAML 檔案未稍微超過執行個體化`AbsoluteLayout`用於時鐘：

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

所有其他項目就會發生在程式碼後置檔案中。 點陣顯示邏輯已大幅簡化數個陣列，描述對應至每 10 個數字和冒號的點定義：

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

這些欄位都以此的三維陣列`BoxView`儲存點的模式的六個數字的項目。

建構函式會建立所有`BoxView`數字和冒號，以及初始化的項目`Color`屬性`BoxView`冒號的項目：

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

此程式會使用的相對位置和大小功能`AbsoluteLayout`。 每個的高度與寬度`BoxView`設小數值，特別是 85%的 1 除以水平和垂直點的數目。 位置也都設為小數值。

因為所有的位置和大小是相對於總大小`AbsoluteLayout`，則`SizeChanged`只需要設定頁面的處理常式`HeightRequest`的`AbsoluteLayout`:

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

在最後的程式碼`MainPage`類別處理計時器回撥和色彩的每一個數字的點。 多維陣列，在程式碼後置檔案開頭的定義中，可協助進行此邏輯程式的最簡單的一部分：

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

點陣時鐘可能不按照的應用程式明顯`BoxView`，但`BoxView`項目也不能實現類比時鐘：

[![BoxView 時鐘](boxview-images/boxviewclock-small.png "BoxView 時鐘")](boxview-images/boxviewclock-large.png#lightbox "BoxView 時鐘")

中的所有視覺效果[ **BoxViewClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/)計劃是子系`AbsoluteLayout`。 這些項目會使用調整大小`LayoutBounds`附加屬性，而使用旋轉`Rotation`屬性。

三個`BoxView`時鐘指針的項目在 XAML 檔案中，具現化但未定位或調整大小：

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

程式碼後置檔案的建構函式具現化 60`BoxView`時鐘的圓周刻度標記的項目：

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

大小及位置的所有`BoxView`項目中，就會發生`SizeChanged`處理常式`AbsoluteLayout`。 內部類別的小型結構稱為`HandParams`描述每三個相對於總大小時鐘指針的大小：

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

`SizeChanged`處理常式可判斷的置中與半徑`AbsoluteLayout`，然後調整大小和位置 60`BoxView`做為刻度標記的項目。 `for`迴圈結束時，會設定`Rotation`屬性的每一種`BoxView`項目。 在結尾`SizeChanged`處理常式，`LayoutHand`大小和位置的三個時鐘指針呼叫方法：

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

`LayoutHand`方法調整大小，並將每一個指針點直接到 12:00 位置。 方法的結尾`AnchorY`屬性設定為對應至時鐘的中心位置。 這表示旋轉的中心點。

指針會循環使用中計時器回撥函式：

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

秒針視為有點不同： easing 函式的動畫套用至進行移動，看起來很機械化，而不是 smooth。 每個刻度，秒針會提取回有點，然後超過其目的地。 這一小段的程式碼將許多移動的真實性。

## <a name="conclusion"></a>結論

`BoxView`看起來可能會在簡單的第一次，但如您所見，它可以很多功能，而且可以幾乎重現的視覺效果通常可能只能使用向量圖形。 更複雜的圖形，請參閱[Xamarin.Forms 中的使用 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)。


## <a name="related-links"></a>相關連結

- [基本 BoxView （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)
- [文字裝飾 （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)
- [色彩清單方塊 （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox)
- [遊戲的生命週期 （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife)
- [點陣時鐘 （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock)
- [BoxView 時鐘 （範例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock)
- [BoxView](xref:Xamarin.Forms.BoxView)
