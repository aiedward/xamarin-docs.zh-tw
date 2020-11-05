---
title: 第 1 部份： 開始使用 XAML
description: 在 Xamarin.Forms 應用程式中，XAML 大部分用來定義頁面的視覺內容，並與程式碼後端檔案一起運作。
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8992f2a649b1d3998a24a11cdf7e51d768a3cd09
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371335"
---
# <a name="part-1-getting-started-with-xaml"></a>第 1 部份： 開始使用 XAML

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/xamlsamples)

_在 Xamarin.Forms 應用程式中，XAML 大部分用來定義頁面的視覺內容，並與 c # 程式碼後端檔案搭配運作。_

程式碼後端檔案會提供標記的程式碼支援。 這兩個檔案共同構成新的類別定義，其中包含子視圖和屬性初始化。 在 XAML 檔案中，會使用 XML 元素和屬性來參考類別和屬性，並建立標記和程式碼之間的連結。

## <a name="creating-the-solution"></a>建立方案

若要開始編輯第一個 XAML 檔案，請使用 Visual Studio 或 Visual Studio for Mac 來建立新的 Xamarin.Forms 方案。  (選取下方的索引標籤，以對應至您的環境。 ) 

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Windows 中，啟動 Visual Studio 2019，並在 [開始] 視窗中，按一下 [ **建立新專案** ] 以建立新專案：

![新增解決方案視窗](get-started-with-xaml-images/win/new-solution-2019.png)

在 [ **建立新專案** ] **視窗中，選取 [** **專案類型** ] 下拉式清單中的 [行動裝置]，選取 [行動 **應用程式 (Xamarin.Forms)** ] 範本，然後按 [ **下一步]** 按鈕：

![[新增專案] 視窗](get-started-with-xaml-images/win/new-project-2019.png)

在 [設定 **您的新專案** ] 視窗中，將 **專案名稱** 設定為 **XamlSamples** (或您偏好) 的任何專案，然後按一下 [ **建立** ] 按鈕。

在 [ **新的跨平臺應用程式** ] 對話方塊中，按一下 [ **空白** ]，然後按一下 [ **確定]** 按鈕：

![新增應用程式對話方塊](get-started-with-xaml-images/win/new-cross-platform-app.png)

方案中會建立四個專案： **XamlSamples** .NET Standard 程式庫、 **XamlSamples** 、 **XamlSamples** 和通用 Windows 平臺方案 **XamlSamples。**

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，從功能表中選取 [檔案] **> [新方案** ]。 在 [ **新增專案** ] 對話方塊中，選取左側的多 **平臺 > 應用程式** ，而 **空白的表單應用程式** ( *不會* 從範本清單中 **形成應用程式** ) ：

![新增專案對話方塊1](get-started-with-xaml-images/mac/newprojectdialog1.png)

按 **[下一步]** 。

在下一個對話方塊中，將專案命名為 **XamlSamples** (或您偏好) 的任何名稱。 確認已選取 [ **使用 .NET Standard** ] 選項按鈕：

![新增專案對話方塊2](get-started-with-xaml-images/mac/newprojectdialog2.png)

按 **[下一步]** 。

在下列對話方塊中，您可以選取專案的位置：

![新增專案對話方塊3](get-started-with-xaml-images/mac/newprojectdialog3.png)

按下 [ **建立** ]

方案中會建立三個專案： **XamlSamples** .NET Standard 程式庫、 **XamlSamples、Android** 和 **XamlSamples。**

-----

建立 **XamlSamples** 解決方案之後，您可能會想要測試開發環境，方法是選取各種平臺專案作為方案啟始專案，然後在電話模擬器或真實裝置上建立和部署專案範本所建立的簡單應用程式。

除非您需要撰寫平臺特定的程式碼，否則共用的 **XamlSamples** .NET Standard 程式庫專案，是您幾乎所有程式設計時間的費用。 這些文章不會在該專案之外的風險。

### <a name="anatomy-of-a-xaml-file"></a>XAML 檔案的剖析

**XamlSamples** .NET Standard 程式庫中有一組具有下列名稱的檔案：

- **App.xaml，xaml** 檔案;和
- **App.xaml.cs** ，這是與 xaml 檔案相關聯的 c # *程式碼後端* 檔案。

您必須按一下 [app.xaml] 旁邊的箭號，以查看 **程式** 代碼後端檔案。

**App.xaml** 和 **App.xaml.cs** 都參與名為 `App` 的類別，該類別衍生自 `Application` 。 大部分其他具有 XAML 檔案的類別都有提供衍生自的類別，而這些檔案會 `ContentPage` 使用 XAML 來定義整個頁面的視覺內容。 這適用于 **XamlSamples** 專案中的其他兩個檔案：

- **MainPage** ，xaml 檔案;和
- **MainPage.xaml.cs** ，c # 程式碼後端檔案。

**MainPage .xaml** 檔案看起來像這樣 (雖然格式可能有點不同) ：

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

這兩個 XML 命名空間 () 宣告會 `xmlns` 參考 uri、第一個看似在 Xamarin 網站上的，第二個則是 Microsoft 的。 請勿檢查這些 Uri 所指向的內容。 沒有任何內容。 它們只是 Xamarin 和 Microsoft 所擁有的 Uri，而且基本上會以版本識別碼的形式運作。

第一個 XML 命名空間宣告表示，在沒有前置詞的 XAML 檔案中定義的標記會參考中的類別 Xamarin.Forms ，例如 `ContentPage` 。 第二個命名空間宣告會定義的前置詞 `x` 。 這可用於 XAML 本身內建的數個專案和屬性，以及其他 XAML 的執行所支援的元素和屬性。 不過，根據在 URI 中內嵌的年份，這些專案和屬性會略有不同。 Xamarin.Forms 支援 2009 XAML 規格，但並非全部。

`local`命名空間宣告可讓您從 .NET Standard 程式庫專案存取其他類別。

在第一個標記的結尾，前置詞 `x` 會用於名為的屬性 `Class` 。 由於使用此前置詞 `x` 是 xaml 命名空間的虛擬通用，因此 xaml 屬性（例如） `Class` 幾乎一律稱為 `x:Class` 。

`x:Class`屬性會指定完整的 .net 類別名稱： `MainPage` 命名空間中的類別 `XamlSamples` 。 這表示這個 XAML 檔案 `MainPage` 會在衍生自的命名空間中定義名為的新類別 `XamlSamples` ，這 `ContentPage` 是 `x:Class` 屬性出現的標記。

`x:Class`屬性只可出現在 XAML 檔案的根項目中，以定義衍生的 c # 類別。 這是在 XAML 檔案中唯一定義的新類別。 出現在 XAML 檔案中的其他專案，則只是從現有類別具現化並初始化。

除了未使用的指示詞) 之外， **MainPage.xaml.cs** 檔案看起來像這樣 (`using` ：

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

`MainPage`類別衍生自 `ContentPage` ，但請注意 `partial` 類別定義。 這表示應該有另一個部分類別定義 `MainPage` ，但在哪裡？ 那麼該方法是什麼 `InitializeComponent` ？

當 Visual Studio 建立專案時，它會剖析 XAML 檔案以產生 c # 程式碼檔案。 如果您查看 **XamlSamples\XamlSamples\obj\Debug** 目錄，就會找到名為 **XamlSamples.MainPage.xaml.g.cs** 的檔案。 ' G ' 代表已產生。 這是的其他部分類別定義 `MainPage` ，其中包含從函式 `InitializeComponent` 呼叫之方法的定義 `MainPage` 。 然後，您可以將這兩個部分 `MainPage` 類別定義一起編譯。 根據是否編譯 XAML 而定，xaml 檔案或 XAML 檔案的二進位格式會內嵌在可執行檔中。

在執行時間，特定平臺專案中的程式碼 `LoadApplication` 會呼叫方法，並將 `App` .NET Standard 程式庫中類別的新實例傳遞給它。 類別的函式具現 `App` 化 `MainPage` 。 該類別的函式會呼叫 `InitializeComponent` ，然後呼叫 `LoadFromXaml` 從 .NET Standard 程式庫中解壓縮 XAML 檔案 (或其已編譯二進位) 的方法。 `LoadFromXaml` 初始化 XAML 檔案中定義的所有物件、將它們全部連接在父子式關聯性中、將程式碼中定義的事件處理常式附加至 XAML 檔案中設定的事件，並將物件的結果樹狀結構設定為頁面的內容。

雖然您通常不需要花太多時間來處理產生的程式碼檔案，有時執行時間例外狀況會在產生的檔案中的程式碼上引發，因此您應該熟悉它們。

當您編譯和執行此程式時，該專案 `Label` 會顯示在頁面中央，如同 XAML 所建議：

[![Default：：：非 loc (Xamarin. Forms) ：：： display](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

針對更有趣的視覺效果，您只需要更有趣的 XAML。

## <a name="adding-new-xaml-pages"></a>加入新的 XAML 頁面

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要將其他以 XAML 為基礎的 `ContentPage` 類別加入至您的專案，請選取 [ **XamlSamples** .NET Standard 程式庫] 專案，按一下滑鼠右鍵，然後選取 [ **加入 > 新專案**...]。在 [ **加入新專案** ] 對話方塊中，選取 [ **Visual c # 專案] > Xamarin.Forms > 內容頁面** ([ **內容] 頁面 (c # )** ，這會建立僅限程式碼的頁面或 **內容視圖** ，這不是頁面) 。 提供頁面名稱，例如， **HelloXamlPage** ：

![[加入新專案] 對話方塊](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要將其他以 XAML 為基礎的 `ContentPage` 類別加入至您的專案，請選取 [ **XamlSamples** .NET Standard 程式庫] 專案，並叫用檔案 **> [新增** 檔案] 功能表項目。 在 [ **新增** 檔案] 對話方塊的左側，選取左邊的 **表單** ， **表單 ContentPage Xaml** (不是 **表單 ContentPage** ，它會建立僅限程式碼的頁面或 **內容視圖** ，這不是頁面) 。 提供頁面名稱，例如， **HelloXamlPage** ：

![[新增檔案] 對話方塊](get-started-with-xaml-images/mac/newfiledialog.png)

-----

系統會將兩個檔案新增至專案、 **HelloXamlPage** 和程式碼後端檔案 **HelloXamlPage.xaml.cs** 。

## <a name="setting-page-content"></a>設定頁面內容

編輯 **HelloXamlPage .xaml** 檔案，讓唯一的標記是和的標籤 `ContentPage` `ContentPage.Content` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

`ContentPage.Content`標記是 XAML 的唯一語法的一部分。 一開始，它們可能會是不正確 XML，但卻是合法的。 句點不是 XML 中的特殊字元。

`ContentPage.Content`標記稱為 *屬性元素* 標記。 `Content` 是的屬性 `ContentPage` ，而且通常會設定為單一視圖或具有子視圖的版面配置。 一般來說，屬性會變成 XAML 中的屬性，但很難將 `Content` 屬性設為複雜的物件。 基於這個理由，屬性會以 XML 專案表示，此專案是由類別名稱和以句號分隔的屬性名稱所組成。 現在 `Content` 可在標記之間設定屬性 `ContentPage.Content` ，如下所示：

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

另請注意，已 `Title` 在根標記上設定屬性。

此時，類別、屬性和 XML 之間的關聯性應該很明顯： Xamarin.Forms 類別 (例如 `ContentPage` 或 `Label`) 會以 XML 專案的形式出現在 XAML 檔案中。 該類別的屬性（包括 `Title` `ContentPage` 和的七個屬性 `Label` ）通常會顯示為 XML 屬性。

有許多快速鍵可以設定這些屬性的值。 某些屬性是基本的資料類型：例如， `Title` 和 `Text` 屬性的類型為 `String` 、 `Rotation` 類型為 `Double` ，而 (預設為，而且只有在此為 `IsVisible` `true` 圖例) 的型別時才會設定 `Boolean` 。

`HorizontalTextAlignment`屬性的型別為 `TextAlignment` ，其為列舉型別。 針對任何列舉型別的屬性，您只需要提供一個成員名稱。

但是，如果是更複雜類型的屬性，則會使用轉換器來剖析 XAML。 這些是 Xamarin.Forms 衍生自的類別 `TypeConverter` 。 許多都是公用類別，但有些不是。 針對這個特定的 XAML 檔案，其中有幾個類別在幕後扮演著角色：

- `LayoutOptionsConverter` 針對  `VerticalOptions` 屬性
- `FontSizeConverter` 針對  `FontSize` 屬性
- `ColorTypeConverter` 針對  `TextColor` 屬性

這些轉換器會管理屬性設定的允許語法。

`ThicknessTypeConverter`可以處理一個、兩個或四個以逗號分隔的數位。 如果有提供一個數位，則會套用至所有四方。 如果有兩個數字，則第一個是左方和右邊填補，而第二個是頂端和底部。 四個數字以左、上、右和下的順序排列。

`LayoutOptionsConverter`可以將結構的公用靜態欄位名稱轉換 `LayoutOptions` 成類型的值 `LayoutOptions` 。

`FontSizeConverter`可以處理 `NamedSize` 成員或數位字型大小。

`ColorTypeConverter` `Color` 會接受結構或十六進位 RGB 值的公用靜態欄位名稱（不論是否使用 Alpha 色板），並在前面加上數位記號 ( # ) 。 以下是沒有 Alpha 通道的語法：

 `TextColor="#rrggbb"`

每個小數位數都是十六進位數位。 以下是 Alpha 通道的包含方式：

 `TextColor="#aarrggbb">`

針對 Alpha 色板，請記住，FF 完全不透明，而00是完全透明的。

另外兩個格式可讓您針對每個通道只指定一個十六進位數位：

 `TextColor="#rgb"` `TextColor="#argb"`

在這些情況下，數位會重複形成值。 例如，#CF3 是 RGB 色彩 CC-FF-33。

## <a name="page-navigation"></a>頁面流覽

當您執行 **XamlSamples** 程式時， `MainPage` 會顯示。 若要查看新的， `HelloXamlPage` 您可以將它設定為 **App.xaml.cs** 檔中的新啟動頁面，或流覽至新的頁面 `MainPage` 。

若要執行導覽，請先變更 **App.xaml.cs** 函式中的程式碼，以便 `NavigationPage` 建立物件：

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

在 **MainPage.xaml.cs** 的函式中，您可以建立簡單的， `Button` 並使用事件處理常式來流覽至 `HelloXamlPage` ：

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

設定 `Content` 頁面的屬性會取代 XAML 檔案中的 `Content` 屬性設定。 當您編譯和部署此程式的新版本時，畫面上會出現一個按鈕。 按下它會流覽至 `HelloXamlPage` 。 以下是 iPhone、Android 和 UWP 的結果頁面：

[![旋轉的標籤文字](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

您可以使用 `MainPage` iOS 上的 [上一頁] 按鈕、在 Android 上的電話頂端或在 Android 上的手機底部向左箭號，或在 Windows 10 上使用頁面頂端的向左箭號，以流覽回使用 iOS 上的 **< 上一頁** 按鈕。

您可以針對不同的轉譯方式，使用 XAML 來進行實驗 `Label` 。 如果您需要將任何 Unicode 字元內嵌到文字中，您可以使用標準的 XML 語法。 例如，若要將問候語放在彎引號中，請使用：

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

如下所示：

[![具有 Unicode 字元的旋轉標籤文字](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>XAML 和程式碼互動

**HelloXamlPage** 範例只 `Label` 會在頁面上包含單一頁面，但這非常不尋常。 大部分 `ContentPage` 的衍生會將 `Content` 屬性設定為某些排序的版面配置，例如 `StackLayout` 。 的 `Children` 屬性 `StackLayout` 定義為類型 `IList<View>` ，但實際上是類型的物件 `ElementCollection<View>` ，而且可以使用多個視圖或其他配置來填入集合。 在 XAML 中，這些父子式關聯性會與一般 XML 階層建立。 以下是新頁面的 XAML 檔案，名為 **XamlPlusCodePage** ：

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

這個 XAML 檔案的語法完整，如下所示：

[![頁面上的多個控制項](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

不過，您可能會將此程式視為功能或許也裨益。 或許 `Slider` 應該會導致 `Label` 顯示目前的值，而且 `Button` 可能是要在程式內進行某些動作。

您將在 [第4部分中看到。資料](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)系結的基本概念，使用來顯示值的工作， `Slider` `Label` 可以在具有資料系結的 XAML 中完整處理。 但是，先查看程式碼解決方案會很有用。 儘管如此，處理 `Button` click 的確需要程式碼。 這表示的程式碼後端檔案 `XamlPlusCodePage` 必須包含事件的處理常式 `ValueChanged` ，以及的事件的處理常式 `Slider` `Clicked` `Button` 。 讓我們新增它們：

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

請注意，將處理常式指派給事件的語法，與指派屬性值的語法相同。

如果的事件處理常式 `ValueChanged` `Slider` 將使用 `Label` 顯示目前的值，則處理常式必須從程式碼參考該物件。 `Label`需要以屬性指定的名稱 `x:Name` 。

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

`x`屬性的前置詞 `x:Name` 表示這個屬性是 XAML 內建的。

您指派給屬性的名稱與 `x:Name` c # 變數名稱具有相同的規則。 例如，它必須以字母或底線開頭，且不能包含內嵌空格。

現在 `ValueChanged` 事件處理常式可以設定， `Label` 以顯示新的 `Slider` 值。 您可以從事件引數取得新的值：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

或者，處理常式可以 `Slider` 從引數取得產生此事件的物件 `sender` ，並從該引數取得 `Value` 屬性：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

當您第一次執行程式時， `Label` 因為尚未 `Slider` 引發事件，所以不會顯示值 `ValueChanged` 。 但是，的任何操作 `Slider` 都會使值顯示：

[![顯示滑杆值](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

現在適用于 `Button` 。 讓我們以按鈕的顯示警示，以模擬事件的回應 `Clicked` `Text` 。 事件處理常式可以安全地將 `sender` 引數轉換成 `Button` ，然後存取其屬性：

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

方法定義為， `async` 因為 `DisplayAlert` 方法是非同步，而且開頭必須是 `await` 運算子，這會在方法完成時傳回。 因為這個方法會 `Button` 從 `sender` 引數中取得引發事件，所以相同的處理常式可用於多個按鈕。

您已瞭解在 XAML 中定義的物件可能引發在程式碼後端檔案中處理的事件，而且程式碼後端檔案可以使用以屬性指派給它的名稱，存取在 XAML 中定義的物件 `x:Name` 。 這兩種程式碼和 XAML 互動的基本方式。

您可以藉由檢查新產生的 **XamlPlusCode.xaml.g.cs** 檔案，來以往累積 XAML 運作方式的一些詳細資訊，現在會包含指派給任何 `x:Name` 屬性作為私用欄位的任何名稱。 以下是該檔案的簡化版本：

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

此欄位的宣告可讓您自由地在您的管轄區下，于部分類別檔案內的任何地方使用該變數 `XamlPlusCodePage` 。 在執行時間，在剖析 XAML 之後，會指派此欄位。 這表示該欄位是在函式 `valueLabel` `null` 開始時， `XamlPlusCodePage` 但在呼叫之後有效的 `InitializeComponent` 。

將 `InitializeComponent` 控制權傳回給函式之後，頁面的視覺效果就像是在程式碼中具現化並初始化一樣。 XAML 檔案不再扮演類別中的任何角色。 您可以透過任何想要的方式，在頁面上操作這些物件，例如，藉由將視圖新增至 `StackLayout` ，或將 `Content` 頁面的屬性設定為其他部分。 您可以藉由檢查 `Content` 頁面的屬性和版面組態集合中的專案，來「流覽樹狀結構」 `Children` 。 您可以在以這種方式存取的視圖上設定屬性，或以動態方式將事件處理常式指派給它們。

請放心。 這是您的頁面，而 XAML 則是建立其內容的工具。

## <a name="summary"></a>總結

在此簡介中，您已瞭解 XAML 檔案和程式碼檔如何構成類別定義，以及 XAML 和程式碼檔案如何互動。 但是 XAML 也有自己獨特的語法功能，可讓您以極具彈性的方式使用。 您可以在 [第2部分開始探索。基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)。

## <a name="related-links"></a>相關連結

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第2部分。基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第3部分。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第4部分。資料系結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第5部分。從資料系結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)