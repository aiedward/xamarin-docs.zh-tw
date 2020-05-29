---
標題：「第1部分。 在應用程式中使用 XAML 「描述：」消費者入門 Xamarin.Forms ，xaml 主要是用來定義頁面的視覺內容，並與程式碼後置檔案一起運作。」
assetid： 9073FA0E-BD5A-4492-8A93-54C466F6EDB9 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：09/30/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="part-1-getting-started-with-xaml"></a>第 1 部份： 開始使用 XAML

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_在 Xamarin.Forms 應用程式中，XAML 主要是用來定義頁面的視覺內容，並與 c # 程式碼後置檔案搭配運作。_

程式碼後置檔案提供標記的程式碼支援。 這兩個檔案一起構成新的類別定義，其中包含子視圖和屬性初始化。 在 XAML 檔案中，會使用 XML 元素和屬性來參考類別和屬性，並建立標記和程式碼之間的連結。

## <a name="creating-the-solution"></a>建立方案

若要開始編輯您的第一個 XAML 檔案，請使用 Visual Studio 或 Visual Studio for Mac 來建立新的 Xamarin.Forms 方案。 （選取下方的索引標籤，以對應至您的環境。）

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Windows 中，啟動 Visual Studio 2019，然後在 [開始] 視窗中，按一下 [**建立新專案**] 來建立新的專案：

![新增解決方案視窗](get-started-with-xaml-images/win/new-solution-2019.png)

在 [**建立新專案**]**視窗中，選取 [** **專案類型**] 下拉式集中的 [行動]，選取 [行動**應用程式（ Xamarin.Forms ）** ] 範本，然後按 [**下一步]** 按鈕：

![[新增專案] 視窗](get-started-with-xaml-images/win/new-project-2019.png)

在 [設定**您的新專案**] 視窗中，將 [**專案名稱**] 設為 [ **XamlSamples** ] （或您偏好的任何一項），然後按一下 [**建立**] 按鈕。

在 [**新增跨平臺應用程式**] 對話方塊中，按一下 [**空白**]，然後按一下 [**確定]** 按鈕：

![[新增應用程式] 對話方塊](get-started-with-xaml-images/win/new-cross-platform-app.png)

解決方案中建立了四個專案： **XamlSamples** .NET Standard 程式庫、 **XamlSamples**、 **XamlSamples**和通用 Windows 平臺解決方案（ **XamlSamples**）。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，從功能表選取 [檔案] **> [新增方案**]。 在 [**新增專案**] 對話方塊中，選取左側的 [多**平臺 > 應用程式**]，並從範本清單中選取 [**空白表單應用程式**（*非***表單應用程式**）]：

![[新增專案] 對話方塊1](get-started-with-xaml-images/mac/newprojectdialog1.png)

按 **[下一步]**。

在下一個對話方塊中，為專案指定**XamlSamples**的名稱（或任何您偏好的方式）。 請確定已選取 [**使用 .NET Standard** ] 選項按鈕：

![[新增專案] 對話方塊2](get-started-with-xaml-images/mac/newprojectdialog2.png)

按 **[下一步]**。

在下列對話方塊中，您可以選取專案的位置：

![[新增專案] 對話方塊3](get-started-with-xaml-images/mac/newprojectdialog3.png)

按 [**建立**]

解決方案中會建立三個專案： **XamlSamples** .NET Standard 程式庫、 **XamlSamples**和**XamlSamples**。

-----

建立**XamlSamples**解決方案之後，您可能會想要測試開發環境，方法是選取各種平臺專案作為方案啟始專案，並在電話模擬器或實際裝置上建立及部署專案範本所建立的簡單應用程式。

除非您需要撰寫平臺特定的程式碼，否則共用的**XamlSamples** .NET Standard 程式庫專案，就是您在所有程式設計時間的實際支出。 這些文章不會在該專案之外的風險。

### <a name="anatomy-of-a-xaml-file"></a>XAML 檔案的剖析

在**XamlSamples** .NET Standard 程式庫中，有一對具有下列名稱的檔案：

- **App.xaml、xaml**檔案、和
- **App.xaml.cs**，這是與 xaml 檔案相關聯的 c #*程式碼後*置檔案。

您必須按一下 [app.xaml] 旁的箭號，才能看到**程式**代碼後置檔案。

**App.xaml**和**App.xaml.cs**都會參與一個衍生自的類別，名為 `App` `Application` 。 大部分其他具有 XAML 檔案的類別都會參與衍生自的類別，這些檔案會 `ContentPage` 使用 xaml 來定義整個頁面的視覺內容。 這適用于**XamlSamples**專案中的其他兩個檔案：

- **MAINPAGE**xaml 檔案;和
- **MainPage.xaml.cs**，c # 程式碼後置檔案。

**MainPage**的樣子看起來像這樣（雖然格式可能有點不同）：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage">

    <StackLayout>
        <!-- Place new controls here -->
        <Label Text="Welcome to Xamarin Forms!"
               VerticalOptions="Center"
               HorizontalOptions="Center" />
    </StackLayout>

</ContentPage>
```

這兩個 XML 命名空間（ `xmlns` ）宣告參考 uri，第一個看似在 Xamarin 的網站上，第二個則是 Microsoft 的。 請不要檢查這些 Uri 所指向的內容。 這裡沒有任何東西。 它們只是 Xamarin 和 Microsoft 所擁有的 Uri，而且基本上會當做版本識別碼來運作。

第一個 XML 命名空間宣告表示在 XAML 檔案中，不含前置詞的標記會參考中的類別 Xamarin.Forms ，例如 `ContentPage` 。 第二個命名空間宣告會定義的前置詞 `x` 。 這適用于 XAML 本身內建的數個專案和屬性，以及其他 XAML 的執行支援。 不過，這些專案和屬性會略有不同，視 URI 中內嵌的年份而定。 Xamarin.Forms支援 2009 XAML 規格，但並非全部。

`local`命名空間宣告可讓您從 .NET Standard 程式庫專案存取其他類別。

在第一個標記的結尾， `x` 首碼會用於名為的屬性 `Class` 。 因為在 `x` xaml 命名空間中使用這個前置詞幾乎是通用的，所以 xaml 屬性（例如） `Class` 幾乎一律稱為 `x:Class` 。

`x:Class`屬性會指定完整的 .net 類別名稱： `MainPage` 命名空間中的類別 `XamlSamples` 。 這表示此 XAML 檔案 `MainPage` 會在衍生自的命名空間中定義名為的新類別 `XamlSamples` `ContentPage` ，也就是在其中 `x:Class` 顯示內容的標記。

`x:Class`屬性只能出現在 XAML 檔案的根項目中，以定義衍生的 c # 類別。 這是 XAML 檔案中唯一定義的新類別。 出現在 XAML 檔案中的其他專案，只會從現有類別具現化並初始化。

**MainPage.xaml.cs**檔案看起來像這樣（除了未使用的指示詞之外 `using` ）：

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
        }
    }
}
```

`MainPage`類別衍生自 `ContentPage` ，但請注意 `partial` 類別定義。 這表示應該有另一個部分類別定義 `MainPage` ，但是在哪裡？ 該方法是什麼 `InitializeComponent` ？

當 Visual Studio 建立專案時，它會剖析 XAML 檔案以產生 c # 程式碼檔案。 如果您查看**XamlSamples\XamlSamples\obj\Debug**目錄，您會發現名為**XamlSamples.MainPage.xaml.g.cs**的檔案。 ' G ' 代表產生的。 這是的另一個部分類別定義 `MainPage` ，其中包含 `InitializeComponent` 從函式呼叫的方法定義 `MainPage` 。 然後可以將這兩個部分 `MainPage` 類別定義編譯在一起。 視 XAML 是否已編譯而定，xaml 檔案或 XAML 檔案的二進位格式會內嵌在可執行檔中。

在執行時間，特定平臺專案中的程式碼 `LoadApplication` 會呼叫方法，並將它傳遞至 `App` .NET Standard 程式庫中類別的新實例。 類別的函式會具現 `App` 化 `MainPage` 。 該類別的函式會呼叫 `InitializeComponent` ，然後呼叫 `LoadFromXaml` 從 .NET Standard 程式庫抽取 XAML 檔案（或其編譯的二進位檔）的方法。 `LoadFromXaml`初始化 XAML 檔案中定義的所有物件，並將它們全部連接在父子式關聯性中，將程式碼中定義的事件處理常式附加至 XAML 檔案中設定的事件，並將物件的結果樹狀結構設定為頁面的內容。

雖然您通常不需要花太多時間來處理產生的程式碼檔案，但有時會在產生的檔案中的程式碼上引發執行時間例外狀況，因此您應該很熟悉它們。

當您編譯和執行此程式時， `Label` 元素會出現在頁面的中央，如同 XAML 的建議：

[![預設 Xamarin.Forms 顯示](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

針對更有趣的視覺效果，您只需要更有趣的 XAML。

## <a name="adding-new-xaml-pages"></a>加入新的 XAML 頁面

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要將其他 XAML 型 `ContentPage` 類別加入至您的專案，請選取 [ **XamlSamples** .NET Standard 程式庫] 專案，並按一下滑鼠右鍵，然後選取 [**加入 > 新增專案**...]。在 [**加入新專案**] 對話方塊中，選取 [ **Visual c # 專案] > Xamarin.Forms > 內容] 頁面**（不是 [**內容] 頁面（c #）**，這會建立僅限程式字碼頁面，或 [**內容視圖**]，而不是頁面）。 提供頁面的名稱，例如**HelloXamlPage**：

![[加入新專案] 對話方塊](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要將其他 XAML 型 `ContentPage` 類別加入至您的專案，請選取 [ **XamlSamples** .NET Standard 程式庫] 專案，並叫用檔案 **> [新增**檔案] 功能表項目。 在 [**新增**檔案] 對話方塊的左側，選取左側的 [**表單**]，然後選取 [**表單 ContentPage Xaml** ] （不是 [**表單 ContentPage**]，它會建立僅限程式字碼頁面，或不是頁面的 [**內容視圖**]）。 提供頁面的名稱，例如**HelloXamlPage**：

![[新增檔案] 對話方塊](get-started-with-xaml-images/mac/newfiledialog.png)

-----

系統會將兩個檔案加入至專案**HelloXamlPage**和程式碼後置檔案**HelloXamlPage.xaml.cs**。

## <a name="setting-page-content"></a>設定頁面內容

編輯**HelloXamlPage** ，使唯一的標記為和的標籤 `ContentPage` `ContentPage.Content` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

`ContentPage.Content`標記是 XAML 的唯一語法的一部分。 一開始，它們可能會是不正確 XML，但卻是合法的。 句點不是 XML 中的特殊字元。

`ContentPage.Content`標記稱為*屬性元素*標記。 `Content`是的屬性 `ContentPage` ，而且通常會設定為單一視圖或具有子視圖的版面配置。 一般來說，屬性會變成 XAML 中的屬性，但很難將 `Content` 屬性設定為複雜的物件。 基於這個理由，屬性會以 XML 元素表示，其中包含類別名稱和屬性名稱，並以句號分隔。 現在，您 `Content` 可以在標記之間設定屬性 `ContentPage.Content` ，如下所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage"
             Title="Hello XAML Page">
    <ContentPage.Content>

        <Label Text="Hello, XAML!"
               VerticalOptions="Center"
               HorizontalTextAlignment="Center"
               Rotation="-15"
               IsVisible="true"
               FontSize="Large"
               FontAttributes="Bold"
               TextColor="Blue" />

    </ContentPage.Content>
</ContentPage>
```

另請注意，已在 `Title` 根標記上設定屬性。

此時，類別、屬性和 XML 之間的關聯性應該很明顯： Xamarin.Forms 類別（例如 `ContentPage` 或 `Label` ）會在 XAML 檔案中顯示為 XML 元素。 該類別的屬性（包括 `Title` `ContentPage` 和的七個屬性 `Label` ）通常會顯示為 XML 屬性。

有許多快捷方式都是用來設定這些屬性的值。 某些屬性為基本資料類型：例如， `Title` 和 `Text` 屬性的類型為，且的類型 `String` `Rotation` 為 `Double` ，而且 `IsVisible` （ `true` 根據預設，只有在此處設定）是類型 `Boolean` 。

`HorizontalTextAlignment`屬性的類型為 `TextAlignment` ，也就是列舉。 針對任何列舉類型的屬性，您只需要提供成員名稱。

不過，對於更複雜類型的屬性而言，轉換器是用來剖析 XAML。 這些是 Xamarin.Forms 衍生自的類別 `TypeConverter` 。 許多是公用類別，但有些則不是。 針對這個特定的 XAML 檔案，其中有幾個類別會在幕後扮演角色：

- `LayoutOptionsConverter`屬性的 `VerticalOptions`
- `FontSizeConverter`屬性的 `FontSize`
- `ColorTypeConverter`屬性的 `TextColor`

這些轉換器會管理屬性設定的允許語法。

`ThicknessTypeConverter`可以處理一個、兩個或四個以逗號分隔的數位。 如果提供一個數位，則會套用到所有四個邊。 有兩個數字，第一個是靠左和向右填補，而第二個是頂端和底部。 順序為左、上、右和下的四個數字。

`LayoutOptionsConverter`可以將結構的公用靜態欄位名稱轉換 `LayoutOptions` 為類型的值 `LayoutOptions` 。

`FontSizeConverter`可以處理 `NamedSize` 成員或數位字型大小。

`ColorTypeConverter`會接受結構的公用靜態欄位名稱 `Color` 或十六進位 RGB 值（包含或不含 Alpha 色板），前面加上數位記號（#）。 以下是不含 Alpha 色板的語法：

 `TextColor="#rrggbb"`

其中的每個字母都是十六進位數位。 以下是 Alpha 色板的包含方式：

 `TextColor="#aarrggbb">`

針對 Alpha 色板，請記住，FF 完全不透明，而00則完全透明。

另外兩種格式可讓您針對每個通道僅指定一個十六進位數位：

 `TextColor="#rgb"` `TextColor="#argb"`

在這些情況下，數位會重複形成值。 例如，#CF3 是 RGB 色彩 CC-FF-33。

## <a name="page-navigation"></a>頁面導覽

當您執行**XamlSamples**程式時， `MainPage` 會顯示。 若要查看新的， `HelloXamlPage` 您可以將其設定為**App.xaml.cs**檔案中的新啟動頁面，或流覽至中的新頁面 `MainPage` 。

若要執行導覽，請先變更**App.xaml.cs**函式中的程式碼，以便 `NavigationPage` 建立物件：

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

在**MainPage.xaml.cs**的函式中，您可以建立簡單的， `Button` 並使用事件處理常式來流覽至 `HelloXamlPage` ：

```csharp
public MainPage()
{
    InitializeComponent();

    Button button = new Button
    {
        Text = "Navigate!",
        HorizontalOptions = LayoutOptions.Center,
        VerticalOptions = LayoutOptions.Center
    };

    button.Clicked += async (sender, args) =>
    {
        await Navigation.PushAsync(new HelloXamlPage());
    };

    Content = button;
}
```

設定 `Content` 頁面的屬性會取代 XAML 檔案中的 `Content` 屬性設定。 當您編譯並部署此程式的新版本時，畫面上會出現一個按鈕。 按下它會流覽至 `HelloXamlPage` 。 以下是 iPhone、Android 和 UWP 的結果頁面：

[![旋轉的標籤文字](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

您可以使用 `MainPage` 頁面頂端的向左箭號或 Android 上電話的底部，或使用 Windows 10 頁面頂端的向左箭號，流覽回到使用 iOS 上的 [ **< 上一頁**] 按鈕。

請隨意試驗 XAML，以取得不同的轉譯方式 `Label` 。 如果您需要將任何 Unicode 字元內嵌到文字中，您可以使用標準 XML 語法。 例如，若要將問候語放在智慧引號中，請使用：

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

如下所示：

[![具有 Unicode 字元的旋轉標籤文字](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>XAML 和程式碼互動

**HelloXamlPage**範例只包含 `Label` 頁面上的單一，但這非常不尋常。 大部分 `ContentPage` 的衍生都會將 `Content` 屬性設定為某種類型的配置，例如 `StackLayout` 。 的 `Children` 屬性 `StackLayout` 定義為類型 `IList<View>` ，但實際上是類型的物件 `ElementCollection<View>` ，而且該集合可以填入多個 views 或其他版面配置。 在 XAML 中，這些父子式關聯性是使用一般 XML 階層建立的。 以下是名為**XamlPlusCodePage**的新頁面的 XAML 檔案：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

此 XAML 檔案的語法完成，如下所示：

[![頁面上的多個控制項](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

不過，您可能會考慮此程式的功能或許也裨益。 可能會 `Slider` 導致 `Label` 顯示目前的值，而且 `Button` 可能要在程式內執行某個動作。

如您在[第4部分中所見。資料系結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)：使用來顯示值的工作，可以使用資料系結 `Slider` `Label` 完全在 XAML 中處理。 但先查看程式碼解決方案會很有用。 就算如此，處理 `Button` 按一下也一定需要程式碼。 這表示的程式碼後置檔案 `XamlPlusCodePage` 必須包含的事件處理常式 `ValueChanged` `Slider` ，以及的事件 `Clicked` `Button` 。 讓我們新增它們：

```csharp
namespace XamlSamples
{
    public partial class XamlPlusCodePage
    {
        public XamlPlusCodePage()
        {
            InitializeComponent();
        }

        void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
        {

        }

        void OnButtonClicked(object sender, EventArgs args)
        {

        }
    }
}
```

這些事件處理常式不需要是公用的。

回到 XAML 檔案， `Slider` 和 `Button` 標記必須包含 `ValueChanged` `Clicked` 參考這些處理常式之和事件的屬性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Clicked="OnButtonClicked" />
    </StackLayout>
</ContentPage>
```

請注意，將處理常式指派給事件與指派屬性值的語法相同。

如果的事件處理常式 `ValueChanged` `Slider` 將會使用 `Label` 來顯示目前的值，則處理常式需要從程式碼參考該物件。 `Label`需要使用屬性指定的名稱 `x:Name` 。

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

`x`屬性的前置詞 `x:Name` 表示此屬性是 XAML 的內建函式。

您指派給屬性的名稱與 `x:Name` c # 變數名稱具有相同的規則。 例如，它的開頭必須是字母或底線，而且不能包含內嵌空格。

現在， `ValueChanged` 事件處理常式可以設定， `Label` 以顯示新的 `Slider` 值。 新值可從事件引數取得：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

或者，處理常式可能會 `Slider` 從引數中取得產生此事件的物件 `sender` ，並 `Value` 從中取得屬性：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

當您第一次執行程式時， `Label` `Slider` 由於 `ValueChanged` 事件尚未引發，因此不會顯示值。 但是的任何操作 `Slider` 都會導致顯示值：

[![顯示的滑杆值](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

現在為 `Button` 。 讓我們藉由使用按鈕的來顯示警示，來模擬事件的回應 `Clicked` `Text` 。 事件處理常式可以安全地將 `sender` 引數轉換為 `Button` ，然後存取其屬性：

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

方法定義為， `async` 因為 `DisplayAlert` 方法是非同步，且前面應該加 `await` 上運算子，當方法完成時，會傳回。 因為這個方法 `Button` `sender` 會從引數中取得引發事件，所以相同的處理常式可以用於多個按鈕。

您已瞭解 XAML 中定義的物件可以引發在程式碼後置檔案中處理的事件，而且程式碼後置檔案可以使用以屬性指派給它的名稱，存取在 XAML 中定義的物件 `x:Name` 。 這些是程式碼和 XAML 互動的兩種基本方式。

您可以藉由檢查新產生的**XamlPlusCode.xaml.g.cs**檔案來以往累積所有 XAML 運作方式的一些其他深入解析，此檔案現在包含任何指派給任何 `x:Name` 屬性的名稱做為私用欄位。 以下是該檔案的簡化版本：

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

此欄位的宣告可讓您在管轄區下的部分類別檔案內，自由地使用變數 `XamlPlusCodePage` 。 在執行時間，會在剖析 XAML 之後指派欄位。 這表示 `valueLabel` 當此函式開始時，此欄位會是， `null` 但是在呼叫之後才會 `XamlPlusCodePage` 生效 `InitializeComponent` 。

將 `InitializeComponent` 控制權傳回給此函式之後，頁面的視覺效果就如同已在程式碼中具現化並初始化一樣。 XAML 檔案不再扮演類別中的任何角色。 您可以透過任何想要的方式在頁面上操作這些物件，例如，將視圖加入至 `StackLayout` ，或將 `Content` 頁面的屬性設定為其他專案。 您可以藉由檢查 `Content` 頁面的屬性和版面組態集合中的專案，來「流覽樹狀結構」 `Children` 。 您可以設定以這種方式存取之 views 的屬性，或動態指派事件處理常式給它們。

歡迎免費。 這是您的頁面，而 XAML 只是用來建立其內容的工具。

## <a name="summary"></a>摘要

在此簡介中，您已瞭解 XAML 檔案和程式碼檔案如何提供類別定義，以及 XAML 和程式碼檔案如何互動。 但是 XAML 也有自己獨特的語法功能，可讓它以非常有彈性的方式使用。 您可以在第2部分開始探索這些功能[。基本的 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)。

## <a name="related-links"></a>相關連結

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第2部分。基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第3部分。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第4部分。資料系結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第5部分：從資料系結到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
