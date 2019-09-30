---
title: 第 1 部分。 開始使用 XAML
description: 在 Xamarin.Forms 應用程式中，XAML 是大部分會用來定義頁面和程式碼後置檔案搭配運作的視覺化內容。
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
ms.openlocfilehash: a35a3750957073168bca4903e1cef79ed53a3de9
ms.sourcegitcommit: bb6a103de620386187a08151a4cdc29a4dc53a10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679967"
---
# <a name="part-1-getting-started-with-xaml"></a>第 1 部分。 開始使用 XAML

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_在 Xamarin.Forms 應用程式中，XAML 大多用來定義頁面的視覺內容及一起運作C#程式碼後置檔案。_

程式碼後置檔案提供的標記程式碼支援。 在一起，這兩個檔案會導致新的類別定義，其中包含子檢視和屬性初始設定。 在 XAML 檔案中，類別和屬性會參考 XML 項目和屬性，而且建立標記和程式碼之間的連結。

## <a name="creating-the-solution"></a>建立方案

若要開始編輯您的第一個 XAML 檔案，請使用 Visual Studio 或 Visual Studio for Mac 建立新的 Xamarin.Forms 方案。 （選取下方為您環境的對應的索引標籤）。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在 Windows 中，啟動 Visual Studio 2019，然後在 [開始] 視窗中，按一下 [**建立新專案**] 來建立新的專案：

![新增解決方案視窗](get-started-with-xaml-images/win/new-solution-2019.png)

在 [**建立新專案**] 視窗的 [**專案類型**]**下拉式選單中，** 選取 [行動**應用程式（Xamarin）** ] 範本，然後按 [**下一步]** 按鈕：

![[新增專案] 視窗](get-started-with-xaml-images/win/new-project-2019.png)

在 [設定**您的新專案**] 視窗中，將 [**專案名稱**] 設為 [ **XamlSamples** ] （或您偏好的任何一項），然後按一下 [**建立**] 按鈕。

在 [**新增跨平臺應用程式**] 對話方塊中，按一下 [**空白**]，然後按一下 [**確定]** 按鈕：

![[新增應用程式] 對話方塊](get-started-with-xaml-images/win/new-cross-platform-app.png)

在方案中建立四個專案： **XamlSamples** .NET 標準程式庫**XamlSamples.Android**， **XamlSamples.iOS**，與通用 Windows 平台解決方案中， **XamlSamples.UWP**。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，選取**檔案 > 新的方案**從功能表。 在 **新的專案**對話方塊中，選取**多平台 > 應用程式**左邊，和**空白的 Forms App** (*不* **Forms 應用程式**) 從範本清單：

![[新增專案] 對話方塊1](get-started-with-xaml-images/mac/newprojectdialog1.png)

按下**下一步**。

在下一步 對話方塊中，提供專案名稱**XamlSamples** （或您偏好的任何內容）。 請確定**使用.NET Standard**選取選項按鈕：

![[新增專案] 對話方塊2](get-started-with-xaml-images/mac/newprojectdialog2.png)

按下**下一步**。

在下列對話方塊中，您可以選取專案的位置：

![[新增專案] 對話方塊3](get-started-with-xaml-images/mac/newprojectdialog3.png)

按下**建立**

在方案中建立三個專案： **XamlSamples** .NET 標準程式庫**XamlSamples.Android**，並**XamlSamples.iOS**。

-----

在建立後**XamlSamples**解決方案，您可能想要為方案的啟始專案，選取不同的平台專案來測試您的開發環境，以及建置和部署簡單的應用程式所建立專案範本，在 phone 模擬器或實際裝置上。

除非您要撰寫平台特定程式碼，共用**XamlSamples** .NET Standard 程式庫專案時，您將會花費幾乎所有的程式設計的時間。 這些文件不會積極該專案之外。

### <a name="anatomy-of-a-xaml-file"></a>XAML 檔案的結構

內**XamlSamples** .NET Standard 程式庫是一組具有下列名稱的檔案：

- **App.xaml**，XAML 檔案; 以及
- **App.xaml.cs**、 C# *程式碼後置*XAML 檔案相關聯的檔案。

您必須按一下箭號旁**App.xaml**若要查看程式碼後置檔案。

兩者**App.xaml**並**App.xaml.cs**類別，名為參與`App`衍生自`Application`。 使用 XAML 檔案的大部分其他類別衍生自的類別參與`ContentPage`; 這些檔案會使用 XAML 來定義整個頁面的視覺內容。 這是中的其他兩個檔案，則為 true **XamlSamples**專案：

- **MainPage.xaml**，XAML 檔案; 以及
- **MainPage.xaml.cs**、C#程式碼後置檔案。

**MainPage.xaml** （雖然格式可能會稍有不同） 檔案看起來像這樣：

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

這兩個 XML 命名`xmlns`空間（）宣告參考 uri，第一個看似在 Xamarin 的網站上，第二個則是 Microsoft 的。 不需要檢查哪些這些 Uri 指向。 裡面沒有東西。 他們只是由 Xamarin 與 Microsoft 所擁有的 Uri，它們基本上函式做為版本識別碼。

第一個 XML 命名空間宣告表示，沒有前置詞在 XAML 檔中定義的標記是指類別在 Xamarin.Forms 中，例如`ContentPage`。 第二個命名空間宣告中定義的前置詞`x`。 這用數個項目和屬性的 XAML 內建函式本身的支援和 XAML 的其他實作。 不過，這些項目和屬性會內嵌在 URI 中的年份而略有不同。 Xamarin.Forms 可支援在 2009 XAML 規格中，但不是它全部。

`local`命名空間宣告可讓您存取.NET Standard 程式庫專案中的其他類別。

在該第一個標記，結尾處`x`前置詞用於名為屬性`Class`。 因為使用 this`x`前置詞是這類的 XAML 命名空間，而 XAML 屬性幾乎通用`Class`幾乎都稱為`x:Class`。

`x:Class`屬性會指定完整的.NET 類別名稱：`MainPage`類別中`XamlSamples`命名空間。 這表示，此 XAML 檔案會定義名為的新類別`MainPage`中`XamlSamples`命名空間衍生自`ContentPage`— 在其中標記`x:Class`屬性會出現。

`x:Class`屬性只可以出現在 XAML 檔案，以定義 衍生的根項目C#類別。 這是唯一的新 XAML 檔案中定義的類別。 所有其他項目出現在 XAML 檔案中是直接從現有類別具現化而初始化。

**MainPage.xaml.cs**檔案看起來像這樣 (除了未使用`using`指示詞):

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

`MainPage`類別衍生自`ContentPage`，但請注意`partial`類別定義。 這可能表示，應該是另一個部分類別定義`MainPage`，但在哪裡可以找到？ 為何，`InitializeComponent`方法？

當 Visual Studio 建置專案時，它會剖析 XAML 檔案來產生C#程式碼檔案。 如果您查看**XamlSamples\XamlSamples\obj\Debug**目錄中，您可以找到名為的檔案**XamlSamples.MainPage.xaml.g.cs**。 產生代表 'g'。 這是其他部分類別定義`MainPage`，其中包含定義`InitializeComponent`方法呼叫從`MainPage`建構函式。 這些兩個部分`MainPage`類別定義然後一起編譯。 根據 XAML 是否經過編譯後，XAML 檔案或二進位格式的 XAML 檔案內嵌在可執行檔。

在執行階段，程式碼中的特定平台專案呼叫`LoadApplication`方法，傳遞給它的新執行個體`App`.NET Standard 程式庫中的類別。 `App`類別建構函式具現化`MainPage`。 該類別的建構函式呼叫`InitializeComponent`，接著呼叫`LoadFromXaml`擷取.NET Standard 程式庫中的 XAML 檔案 （或其已編譯的二進位檔） 的方法。 `LoadFromXaml` 初始化 XAML 檔案中定義的所有物件、 所有連線中父子式關聯性、 附加在 XAML 檔案中，設定事件的程式碼中定義的事件處理常式，並設定物件的結果樹狀結構頁面的內容。

雖然您通常不需要花太多時間使用產生的程式碼檔案，有時執行階段會引發例外狀況程式碼在產生的檔案，因此您應該先熟悉這些。

當您編譯和執行此程式，`Label`元素會出現在頁面中央，XAML 所示：

[![預設的 Xamarin。表單顯示](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

針對更有趣的視覺效果，您只需要更多有趣的 XAML。

## <a name="adding-new-xaml-pages"></a>加入新的 XAML 頁面

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要將其他以 XAML 為基礎的 `ContentPage` 類別加入至您的專案，請選取 [ **XamlSamples** .NET Standard 程式庫] 專案，按一下滑鼠右鍵，然後選取 [新增 **> 新專案**...]。在 [**加入新專案**] 對話方塊中，選取 [**視覺C#專案] > [Xamarin. 表單 > 內容] 頁面**（不是 [**內容] 頁面（C#）** ，它會建立僅限程式字碼頁面，或不是頁面的**內容視圖**）。 指定頁面名稱，例如**HelloXamlPage**:

![[加入新專案] 對話方塊](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要新增其他 XAML 為基礎`ContentPage`類別，以您的專案中，選取**XamlSamples** .NET Standard 程式庫專案，然後叫用**檔案 > 新的檔案**功能表項目。 左邊的**新的檔案**對話方塊中，選取**Form**左邊，並**Forms ContentPage Xaml** (不**Forms ContentPage**，建立僅限程式碼的頁面上，或是**內容檢視**，這不是頁面)。 指定頁面名稱，例如**HelloXamlPage**:

![[新增檔案] 對話方塊](get-started-with-xaml-images/mac/newfiledialog.png)

-----

兩個檔案新增至專案中， **HelloXamlPage.xaml**和 程式碼後置檔案**HelloXamlPage.xaml.cs**。

## <a name="setting-page-content"></a>設定頁面內容

編輯**HelloXamlPage.xaml**檔案，以便唯一的標記是用來`ContentPage`和`ContentPage.Content`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

`ContentPage.Content`標籤是唯一的 XAML 語法的一部分。 首先，它們看起來好像是無效的 XML，但是而言是合法的。 句號不是 XML 中的特殊字元。

`ContentPage.Content`標記稱為*property 項目*標記。 `Content` 是的屬性`ContentPage`，且通常設定為單一檢視或子檢視版面配置。 屬性通常會變成 XAML 中的屬性，但它可能難以設定`Content`屬性的複雜物件。 基於這個理由，屬性會表示為類別名稱和以句號分隔屬性名稱所組成的 XML 項目。 現在`Content`屬性可以設定之間`ContentPage.Content`標記，像這樣：

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

也請注意，`Title`屬性已設定根標記上。

此時，類別、屬性和 XML 之間的關聯性應該很明顯：在 XAML 檔案中，會以 XML `ContentPage`元素`Label`的形式顯示 Xamarin 類別（例如或）。 該類別的屬性，包括`Title`上`ContentPage`和七個屬性`Label`— 通常會顯示為 XML 屬性。

若要設定這些屬性的值存在許多捷徑。 某些屬性為基本資料類型：例如`Title` ，和`IsVisible` `Double` `true`屬性的`String`類型為，且的類型為，而（根據預設，只有在此處設定）為類型`Rotation` `Text` `Boolean`.

`HorizontalTextAlignment`屬性的類型是`TextAlignment`，這是列舉型別。 對於任何列舉類型的屬性，您只需要提供是成員名稱。

針對更複雜類型的屬性，不過，轉換器會用於剖析的 XAML。 這些是衍生自的 Xamarin.Forms 中的類別`TypeConverter`。 有許多是公用類別，但有些不是。 這個特定的 XAML 檔案，這些類別的數個播放在幕後的角色：

- `LayoutOptionsConverter` 針對`VerticalOptions`屬性
- `FontSizeConverter` 針對`FontSize`屬性
- `ColorTypeConverter` 針對`TextColor`屬性

這些轉換子控管的屬性設定可允許的語法。

`ThicknessTypeConverter`可以處理其中一個、 兩個或四個以逗號分隔的數字。 如果提供一個數字，它適用於所有四個邊。 有兩個數字，第一個左側和右側填補，而第二個頂端和底部。 四個數字位於順序左側、 上方、 右側，與下方。

`LayoutOptionsConverter`可轉換的公用靜態欄位的名稱`LayoutOptions`類型的值結構`LayoutOptions`。

`FontSizeConverter`可以處理`NamedSize`成員或數值的字型大小。

`ColorTypeConverter`可接受的公用靜態欄位的名稱`Color`結構或十六進位的 RGB 值，包含或不含 alpha 色板，前面加上數字符號 （#）。 以下是不含 alpha 色板的語法：

 `TextColor="#rrggbb"`

每個小小的字母是十六進位數字。 以下是包含 alpha 色頻的方式：

 `TextColor="#aarrggbb">`

Alpha 通道，請記住，FF 完全不透明且 00 是完全透明。

其他兩種格式可讓您指定只有單一十六進位數字每個通道：

 `TextColor="#rgb"` `TextColor="#argb"`

在這些情況下，會重複以表單值的數字。 例如，#CF3 是 RGB 色彩 CC-FF-33。

## <a name="page-navigation"></a>頁面巡覽

當您執行**XamlSamples**計劃，`MainPage`隨即出現。 若要查看新`HelloXamlPage`您可以設定它作為新的啟動頁面**App.xaml.cs**檔案，或瀏覽至新的頁面從`MainPage`。

若要實作導覽，第一次變更中的程式碼**App.xaml.cs**建構函式，讓`NavigationPage`建立物件：

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

在  **MainPage.xaml.cs**建構函式，您可以建立簡單`Button`，並使用 瀏覽至 事件處理常式`HelloXamlPage`:

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

設定`Content`頁的屬性會取代設定`Content`XAML 檔案中的屬性。 當您編譯和部署新的版本，此程式時，按鈕會出現在螢幕上。 按下它瀏覽至`HelloXamlPage`。 以下是 iPhone、 Android 及 UWP 上的 [結果] 頁面：

[![旋轉的標籤文字](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

您可以瀏覽回到`MainPage`使用 **< 上一步**在 iOS 上，使用向左箭號，在頁面頂端或底部的 行動電話在 Android 上，或使用向左箭號，在 Windows 10 上的頁面頂端的按鈕。

歡迎您試驗不同的方式來呈現的 XAML `Label`。 如果您需要在文字中內嵌任何 Unicode 字元，您可以使用標準的 XML 語法。 比方說，若要將招呼語設智慧引號，請使用：

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

看起來如下：

[![具有 Unicode 字元的旋轉標籤文字](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>XAML 和程式碼互動

**HelloXamlPage**範例只包含一個單一`Label`在頁面上，但這是極不尋常。 大部分`ContentPage`衍生項目組`Content`部分的版面配置的屬性排序，例如`StackLayout`。 `Children`屬性`StackLayout`定義為類型`IList<View>`但就實際型別的物件`ElementCollection<View>`，和集合可以填入多個檢視或其他版面配置。 在 XAML 中，這些父子式關聯性被建立使用一般的 XML 階層。 以下是名為的新頁面的 XAML 檔案**XamlPlusCodePage**:

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

此 XAML 檔案語法完成，且它如下：

[![頁面上的多個控制項](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

不過，您可能會考慮此程式是功能不足。 或許`Slider`應該會導致`Label`來顯示目前的值，而`Button`可能預期的作業在程式內的項目。

如您所見中[第 4 部分。資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)，顯示的工作`Slider`值使用`Label`可以完全在 XAML 中處理資料繫結。 但是，您最好先查看程式碼解決方案。 即便如此，處理`Button`按一下絕對需要的程式碼。 這表示的程式碼後置檔案`XamlPlusCodePage`必須包含的處理常式`ValueChanged`事件`Slider`並`Clicked`事件`Button`。 讓我們來新增它們：

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

這些事件處理常式不必是公用的。

回在 XAML 檔案中，`Slider`並`Button`標記要包含的屬性`ValueChanged`和`Clicked`參考這些處理常式的事件：

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

請注意，將處理常式指派給事件有與指派給屬性的值相同的語法。

如果處理常式`ValueChanged`事件的`Slider`將使用`Label`若要顯示目前的值，這個處理常式需要從程式碼中參考該物件。 `Label`需要使用指定名稱`x:Name`屬性。

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

`x`前置詞`x:Name`屬性會指出這個屬性是 XAML 內建函式。

您指派給`x:Name`屬性具有相同的規則C#變數的名稱。 比方說，它必須以字母或底線開頭，並不包含內嵌的空格。

現在`ValueChanged`事件處理常式可以設定`Label`以顯示新`Slider`值。 新的值是可從事件引數：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

或無法取得處理常式`Slider`會產生此事件的物件`sender`引數，並取得`Value`與屬性：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

當您第一次執行程式時，`Label`不會顯示`Slider`值，因為`ValueChanged`還尚未引發事件。 但操作`Slider`會導致要顯示的值：

[![顯示的滑杆值](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

現在`Button`。 讓我們模擬的回應`Clicked`所顯示的警示事件`Text`的按鈕。 可以安全地將事件處理常式`sender`引數`Button`，然後存取其屬性：

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

方法定義成`async`因為`DisplayAlert`方法是非同步，而且應該前面加上`await`運算子，就會傳回方法完成時。 因為這個方法會取得`Button`引發事件`sender`引數，相同的處理常式可用於多個按鈕。

您已了解在 XAML 中定義的物件可以引發事件處理在程式碼後置檔案中，而且程式碼後置檔案可以存取使用指派給它的名稱的 XAML 中定義的物件`x:Name`屬性。 這些是程式碼與 XAML 互動兩種基本方法。

某些額外的深入了解如何收集 XAML 的運作方式，藉由檢查新產生**XamlPlusCode.xaml.g.cs 檔案**，它現在包含任何名稱指派給任何`x:Name`的私用欄位的屬性。 以下是該檔案的簡化的版本：

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

此欄位的宣告讓變數可自由地在任何地方使用`XamlPlusCodePage`您管轄權下的部分類別檔案。 在執行階段，在已剖析的 XAML，會被指派的欄位。 這表示`valueLabel`欄位是`null`當`XamlPlusCodePage`建構函式開始之後但有效`InitializeComponent`呼叫。

之後`InitializeComponent`回到建構函式的傳回控制項、 頁面的視覺效果就如同它們具有已具現化並初始化程式碼中建構。 XAML 檔案不再在類別中扮演任何角色。 您可以在任何您想要的方式，比方說，藉由新增至檢視的頁面上的這些物件來處理`StackLayout`，或設定`Content`為其他項目頁面的屬性完全。 您可以 「 查核樹狀結構 」 藉由檢查`Content`屬性頁面中的項目`Children`的版面配置的集合。 您可以設定這種方式，存取檢視的屬性，或以動態方式將事件處理常式指派給他們。

請放心。 它是您的頁面，而且 XAML 來建置其內容的工具。

## <a name="summary"></a>總結

與此簡介中，您已了解如何 XAML 檔案和程式碼檔案構成類別定義，以及的 XAML 和程式碼檔案互動的方式。 但是 XAML 也有自己獨特的語法功能，以便用於極具彈性的方式。 您可以開始探索這些功能[第 2 部分。基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)。

## <a name="related-links"></a>相關連結

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第 2 部分：基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
