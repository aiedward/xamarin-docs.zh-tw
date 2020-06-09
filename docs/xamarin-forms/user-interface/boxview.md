---
title： " Xamarin.Forms BoxView" 描述： "本文說明如何在應用程式中使用彩色矩形來裝飾、圖形和互動 Xamarin.Forms 。
assetid： 4CBF703D-84A0-4CDF-A433-5926B587782A ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：07/26/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-boxview"></a>Xamarin.FormsBoxView

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)

[`BoxView`](xref:Xamarin.Forms.BoxView)呈現指定寬度、高度和色彩的簡單矩形。 您可以使用做 `BoxView` 為裝飾、基本圖形，以及透過觸控與使用者互動。

因為沒有 Xamarin.Forms 內建向量圖形系統，所以 `BoxView` 有助於補償。 本文所述的部分範例程式會使用 `BoxView` 來轉譯圖形。 `BoxView`可以調整大小，以類似特定寬度和粗細的線條，然後使用屬性以任何角度旋轉 `Rotation` 。

雖然 `BoxView` 可以模擬簡單的圖形，但您可能會想要[使用 Xamarin.Forms 中的 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)進行調查，以取得更複雜的圖形需求。

## <a name="setting-boxview-color-and-size"></a>設定 BoxView 色彩和大小

通常您會設定下列屬性 `BoxView` ：

- [`Color`](xref:Xamarin.Forms.BoxView.Color)以設定其色彩。
- [`CornerRadius`](xref:Xamarin.Forms.BoxView.CornerRadius)設定其圓角半徑。
- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)設定的寬度（以與 `BoxView` 裝置無關的單位）。
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)設定的高度 `BoxView` 。

`Color`屬性的類型為 `Color` ，而屬性可以設定為任何 `Color` 值，包括命名色彩的141靜態唯讀欄位，範圍從 `AliceBlue` 到 `YellowGreen` 。

`CornerRadius`屬性的類型為， [`CornerRadius`](xref:Xamarin.Forms.CornerRadius) 屬性可以設定為單一的 `double` 統一圓角半徑值，或 `CornerRadius` 由四個值所定義的結構（套用 `double` 至左上方、右上方、左下方和右下方） `BoxView` 。

`WidthRequest`只有在 `HeightRequest` `BoxView` 不受*限制*的版面配置中，和屬性才會播放角色。 當版面配置容器需要知道子系的大小時（例如，當 `BoxView` 是版面配置中自動調整大小的儲存格的子系）時，就會發生這種情況 `Grid` 。 `BoxView`當其 `HorizontalOptions` 和 `VerticalOptions` 屬性設定為以外的值時，也不受限制 `LayoutOptions.Fill` 。 如果不 `BoxView` 受限制，但 `WidthRequest` `HeightRequest` 未設定和屬性，則寬度或高度會設定為預設值40單位，或在行動裝置上為大約1/4 英寸。

如果在配置 `WidthRequest` `HeightRequest` `BoxView` 中*受到限制*，則會忽略和屬性，在此情況下，版面配置容器會在上施加自己的大小 `BoxView` 。

`BoxView`可以在一個維度中受到限制，而不受限制。 例如，如果 `BoxView` 是垂直的子系 `StackLayout` ，的垂直維度 `BoxView` 就是不受限制的，而且其水準維度通常會受到限制。 但是，該水準維度有一些例外狀況：如果將 `BoxView` 其 `HorizontalOptions` 屬性設定為以外的某個專案 `LayoutOptions.Fill` ，則水準維度也不受限制。 本身也可能 `StackLayout` 有未受限制的水準維度，在此情況下也會以 `BoxView` 水準方式來限制。

[**BasicBoxView**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)範例會 `BoxView` 在其頁面的中央顯示一個不受限制的正方形：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             CornerRadius="10"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center"
             HorizontalOptions="Center" />

</ContentPage>
```

結果如下︰

[![基本 BoxView](boxview-images/basicboxview-small.png "基本 BoxView")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

如果 `VerticalOptions` 和 `HorizontalOptions` 屬性已從標記中移除， `BoxView` 或設定為 `Fill` ，則會受到 `BoxView` 頁面大小的限制，並展開以填滿頁面。

`BoxView`也可以是的子系 `AbsoluteLayout` 。 在這種情況下，的位置和大小 `BoxView` 都是使用附加的可系結屬性來設定 `LayoutBounds` 。 `AbsoluteLayout` [**AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md)一文中會討論。

您會在接下來的範例程式中，看到所有這些案例的範例。

## <a name="rendering-text-decorations"></a>轉譯文字裝飾

您可以使用， `BoxView` 以水準和垂直線的形式，在您的頁面上新增一些簡單的裝飾。 [**TextDecoration**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)範例會示範這種情況。 所有程式的視覺效果都是在**MainPage**檔案中定義，其中包含 `Label` 中的數個和元素， `BoxView` `StackLayout` 如下所示：

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

後面的所有標記都是的子系 `StackLayout` 。 此標記是由與專案搭配使用的數種裝飾元素所組成 `BoxView` `Label` ：

[![文字裝飾](boxview-images/textdecoration-small.png "文字裝飾")](boxview-images/textdecoration-large.png#lightbox "文字裝飾")

頁面頂端的「時尚」標頭是使用的子系 `AbsoluteLayout` 是四個元素，而「全部」則 `BoxView` `Label` 是指派特定位置和大小：

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

在 XAML 檔案中， `AbsoluteLayout` 後面接著， `Label` 其中包含描述的格式化文字 `AbsoluteLayout` 。

您可以 `Label` `BoxView` 在將 `StackLayout` 其 `HorizontalOptions` 值設定為以外之 `Fill` 專案的中，將和都括住，以加上文字字串的底線。 的寬度 `StackLayout` 接著會受到的寬度控管 `Label` ，然後將該寬度強加于上 `BoxView` 。 `BoxView`只會指派一個明確的高度：

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

您不能使用這項技術，在較長的文字字串或段落中為個別文字加上底線。

也可以使用 `BoxView` 來類似 HTML `hr` （水準規則）元素。 只要讓的寬度由 `BoxView` 其父容器決定，在此案例中為 `StackLayout` ：

```xaml
<BoxView HeightRequest="3" />
```

最後，您可以在文欄位落的某一端繪製垂直線，方法是 `BoxView` 以水準方式將和括住 `Label` `StackLayout` 。 在此情況下，的高度與的高度 `BoxView` 相同 `StackLayout` ，其受高度的規範 `Label` ：

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
</StackLayout>
```

## <a name="listing-colors-with-boxview"></a>使用 BoxView 來列出色彩

`BoxView`方便顯示色彩。 此程式會使用 `ListView` 來列出結構的所有公用靜態唯讀欄位 Xamarin.Forms `Color` ：

[![ListView 色彩](boxview-images/listviewcolors-small.png "ListView 色彩")](boxview-images/listviewcolors-large.png#lightbox "ListView 色彩")

[**ListViewColors**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors)套裝程式含名為的類別 `NamedColor` 。 靜態的函式會使用反映來存取結構的所有欄位 `Color` ，並 `NamedColor` 為每個欄位建立一個物件。 這些會儲存在靜態 `All` 屬性中：

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

程式視覺效果如 XAML 檔案中所述。 的 `ItemsSource` 屬性 `ListView` 會設定為靜態 `NamedColor.All` 屬性，這表示會 `ListView` 顯示所有個別 `NamedColor` 物件：

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

物件 `NamedColor` 會由 `ViewCell` 設定為資料範本的物件進行格式化 `ListView` 。 此範本包含， `BoxView` 其 `Color` 屬性系結至 `Color` 物件的屬性 `NamedColor` 。

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>藉由子類別化 BoxView 來玩生活遊戲

生活的遊戲是依照數學家 John Conway 定律和普及化在1970年代的*科學美國*的頁面中所創造的行動電話自動化。 這是維琪百科文章[conway 定律的生活遊戲](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)所提供的絕佳簡介。

Xamarin.Forms [**GameOfLife**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)程式 `LifeCell` 會定義衍生自的類別，名為 `BoxView` 。 這個類別會將個別資料格的邏輯封裝在生活的遊戲中：

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

`LifeCell`將其他三個屬性加入至 `BoxView` ： `Col` 和 `Row` 屬性會儲存資料格在方格中的位置，而 `IsAlive` 屬性則表示其狀態。 如果資料格在運作 `IsAlive` 中，屬性也會將的 `Color` 屬性設定 `BoxView` 為黑色，如果資料格不在運作中，則將設為白色。

`LifeCell`也會安裝 `TapGestureRecognizer` ，以允許使用者藉由使用來切換資料格的狀態。 類別會將 `Tapped` 筆勢辨識器的事件轉譯為它自己的 `Tapped` 事件。

**GameOfLife**程式也包含類別， `LifeGrid` 它會封裝許多遊戲邏輯，以及 `MainPage` 處理常式視覺效果的類別。 其中包括描述遊戲規則的重迭。 此處的程式會在 `LifeCell` 頁面上顯示數百個物件：

[![生活遊戲](boxview-images/gameoflife-small.png "生活遊戲")](boxview-images/gameoflife-large.png#lightbox "生活遊戲")

## <a name="creating-a-digital-clock"></a>建立數位時鐘

[**DotMatrixClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)程式會建立 210 `BoxView` 元素，以模擬舊式的 5 x 7 點矩陣顯示的點。 您可以閱讀直向或橫向模式的時間，但它的橫向範圍較大：

[![點矩陣時鐘](boxview-images/dotmatrixclock-small.png "點矩陣時鐘")](boxview-images/dotmatrixclock-large.png#lightbox "點矩陣時鐘")

XAML 檔案除了具現化 `AbsoluteLayout` 用於時鐘的：

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

所有其他專案都會出現在程式碼後置檔案中。 點矩陣顯示邏輯已由數個數組的定義大幅簡化，其中描述每個10位數和冒號的對應點：

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

這些欄位會以三維陣列的元素結束， `BoxView` 用於儲存六位數的點模式。

此函式會建立 `BoxView` 數位和冒號的所有元素，同時初始化冒號的 `Color` `BoxView` 元素屬性：

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

此程式會使用的相對定位和調整大小功能 `AbsoluteLayout` 。 每個的寬度和高度 `BoxView` 都會設定為小數值，特別是85% 的1除以水準和垂直點的數目。 這些位置也會設定為「小數值」。

由於所有位置和大小都是相對於的總大小，因此 `AbsoluteLayout` 頁面的 `SizeChanged` 處理常式只需要設定的 `HeightRequest` `AbsoluteLayout` ：

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

的寬度會 `AbsoluteLayout` 自動設定，因為它會延伸到頁面的完整寬度。

類別中的最後一個程式碼會 `MainPage` 處理計時器回呼，並將每個數位的點色彩。 在程式碼後置檔案的開頭，多維陣列的定義有助於讓這個邏輯成為程式最簡單的部分：

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

## <a name="creating-an-analog-clock"></a>建立類比時鐘

點矩陣時鐘似乎是的明顯應用 `BoxView` ，但 `BoxView` 元素也能夠實現類比時鐘：

[![BoxView 時鐘](boxview-images/boxviewclock-small.png "BoxView 時鐘")](boxview-images/boxviewclock-large.png#lightbox "BoxView 時鐘")

[**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)程式中的所有視覺效果都是的子系 `AbsoluteLayout` 。 這些元素會使用 `LayoutBounds` 附加屬性進行調整大小，並使用 `Rotation` 屬性來旋轉。

這三個 `BoxView` 用於時鐘的元素會在 XAML 檔案中具現化，但不會定位或調整大小：

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

程式碼後置檔案的函式會將60元素具現化， `BoxView` 以用於時鐘圓周周圍的刻度：

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

所有元素的大小和位置 `BoxView` 都會出現在的 `SizeChanged` 處理常式中 `AbsoluteLayout` 。 類別內部的小型結構， `HandParams` 描述與時鐘總大小相關的三個手中每一個的大小：

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

此 `SizeChanged` 處理常式會決定的中心和半徑 `AbsoluteLayout` ，然後再將 `BoxView` 用來做為刻度的60元素大小和位置放在一起。 迴圈會藉 `for` 由設定每個專案的屬性來結束 `Rotation` `BoxView` 。 在處理常式的結尾 `SizeChanged` ， `LayoutHand` 會呼叫方法來調整時鐘的大小和位置：

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

`LayoutHand`方法的大小和位置分別指向12:00 位置。 在方法的結尾， `AnchorY` 屬性會設定為相對於時鐘中心的位置。 這表示旋轉的中心。

手會在計時器回呼函式中旋轉：

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

第二個動作的處理方式稍有不同：會套用動畫緩動函式，讓移動看起來像是機械，而不是平滑。 在每個刻度上，第二部會取回一小部分，然後超出情形及其目的地。 這小段的程式碼增加了許多動作的真實性。

## <a name="related-links"></a>相關連結

- [基本 BoxView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)
- [文字裝飾（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)
- [ListView 色彩（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/)
- [生活遊戲（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)
- [點矩陣時鐘（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)
- [BoxView 時鐘（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)
- [BoxView](xref:Xamarin.Forms.BoxView)
