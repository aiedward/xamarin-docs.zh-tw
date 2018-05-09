---
title: 第 1 部分。 開始使用 XAML
description: Xamarin.Forms 應用程式中 XAML 大多用來定義頁面的視覺內容。 永遠提供標記的程式碼支援的 C# 程式碼檔案與相關聯的 XAML 檔案。 在一起，這兩個檔案會構成新的類別定義，其中包含子檢視和內容初始化。 在 XAML 檔案中，類別和屬性所參考 XML 項目和屬性，以及建立標記和程式碼之間的連結。
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 04/10/2018
ms.openlocfilehash: bc535160816ad186baaa5e802eceb6ae40347d75
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="part-1-getting-started-with-xaml"></a>第 1 部分。 開始使用 XAML

_Xamarin.Forms 應用程式中 XAML 大多用來定義頁面的視覺內容。永遠提供標記的程式碼支援的 C# 程式碼檔案與相關聯的 XAML 檔案。在一起，這兩個檔案會構成新的類別定義，其中包含子檢視和內容初始化。在 XAML 檔案中，類別和屬性所參考 XML 項目和屬性，以及建立標記和程式碼之間的連結。_

## <a name="creating-the-solution"></a>建立方案

若要開始編輯您的第一個 XAML 檔案，請使用 Visual Studio 或 Visual Studio for Mac 來建立新的 Xamarin.Forms 方案。 （選取對應至您的環境的這個頁面頂端的索引標籤）。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Windows 中，使用 Visual Studio 選取**檔案 > 新增 > 專案**從功能表。 在**新專案**對話方塊中，選取**Visual C# > 跨平台**左邊，然後**行動裝置應用程式 (Xamarin.Forms)** 從中心中的清單。 

![](get-started-with-xaml-images/win/newprojectdialog.w157.png "新增專案 對話方塊")

選取方案的位置，將名稱**XamlSamples** （或您偏好的任何內容），然後按**確定**。

在下一個畫面上，選取**空白應用程式**範本， **Xamarin.Forms** UI 技術和**可攜式類別程式庫 (PCL)** 程式碼共用策略：

![](get-started-with-xaml-images/win/newcrossplatformapp.png "新的應用程式 對話方塊")

Press **OK**. 

在方案中建立四個專案： **XamlSamples**可攜式類別庫 (PCL)、 **XamlSamples.Android**， **XamlSamples.iOS**，與 Windows 通用平台解決方案**XamlSamples.UWP**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在 Visual Studio for Mac 選取**檔案 > 新的方案**從功能表。 在**新專案**對話方塊中，選取**多平台 > 應用程式**左邊和**空白表單應用程式**(*不* **Form 應用程式**) 從範本清單：

![](get-started-with-xaml-images/mac/newprojectdialog1.png "新增專案 對話方塊 1")

按**下一步**。

在下一步 對話方塊中，提供給專案的名稱**XamlSamples** （或您偏好的任何內容）。 請確定**使用可攜式類別庫**選取選項按鈕，而且**使用 XAML 使用者介面檔案**已核取：

![](get-started-with-xaml-images/mac/newprojectdialog2.png "新增專案 對話方塊 2")

按**下一步**。 

在下列對話方塊中，您可以選取專案的位置：

![](get-started-with-xaml-images/mac/newprojectdialog3.png "新增專案 對話方塊 3")

按**建立**

在方案中建立三個專案： **XamlSamples**可攜式類別庫 (PCL)、 **XamlSamples.Android**，和**XamlSamples.iOS**。 

-----

在建立之後**XamlSamples**解決方案，您可能想要為方案的啟始專案，選取不同的平台專案來測試您的開發環境，以及所建立的建置和部署簡單的應用程式專案範本，在 phone 模擬器或實際裝置上。

除非您需要撰寫平台專屬程式碼中，共用**XamlSamples** PCL 專案，是在您將會花費幾乎所有的開發時間。 這些文件不會積極該專案之外。

### <a name="anatomy-of-a-xaml-file"></a>XAML 檔案的結構

內**XamlSamples**可攜式類別庫是一組具有下列名稱的檔案：

- **App.xaml**，XAML 檔案; 和
- **App.xaml.cs**，C#*程式碼後置*相關聯的 XAML 檔案的檔案。

您必須按一下箭號旁**App.xaml**若要查看程式碼後置檔案。 

同時**App.xaml**和**App.xaml.cs**參與類別，名為`App`衍生自`Application`。 XAML 檔案與大多數其他類別從中衍生的類別參與`ContentPage`; 這些檔案使用 XAML 來定義整個頁面的視覺內容。 這是中的其他兩個檔案，則為 true **XamlSamples**專案：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

- **MainPage.xaml**，XAML 檔案; 和
- **MainPage.xaml.cs**，C# 程式碼後置檔案。

**MainPage.xaml**檔案看起來像這樣：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage">

    <Label Text="Welcome to Xamarin Forms!" 
           VerticalOptions="Center" 
           HorizontalOptions="Center" />

</ContentPage>
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

- **XamlSamplesPage.xaml**，XAML 檔案; 和
- **XamlSamplesPage.xaml.cs**，C# 程式碼後置檔案。

**XamlSamplesPage.xaml**檔案看起來像這樣：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" 
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" 
             xmlns:local="clr-namespace:XamlSamples" 
             x:Class="XamlSamples.XamlSamplesPage">

    <Label Text="Welcome to Xamarin Forms!" 
           VerticalOptions="Center" 
           HorizontalOptions="Center" />

</ContentPage>
```

-----

兩個 XML 命名空間 ( `xmlns`) 宣告的 Uri，看似在 Xamarin 的網站上的第一個和 on Microsoft 的第二個會參考。 不要檢查哪些這些 Uri 指向。 沒有有項目。 他們只需 Xamarin 和 Microsoft 所擁有的 Uri，它們基本上作為版本識別碼。

第一個 XML 命名空間宣告表示，沒有前置詞在 XAML 檔中定義的標籤是指類別中透過 Xamarin.Forms，例如`ContentPage`。 第二個命名空間宣告定義的前置詞`x`。 這用數個項目和屬性都內建到 XAML 本身的支援和 XAML 的其他實作。 不過，這些項目和屬性會根據內嵌在 URI 中的年份而稍有不同的。 Xamarin.Forms 支援 XAML 2009 的規格，但不是它全部。

`local`命名空間宣告可讓您從 PCL 專案中存取其他類別。

在第一個標籤，結尾`x`前置詞則用於名為屬性`Class`。 因為這個使用`x`前置詞是這類幾乎通用 XAML 命名空間，而 XAML 屬性`Class`幾乎都稱為`x:Class`。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

`x:Class`屬性會指定完整的.NET 類別名稱：`MainPage`類別`XamlSamples`命名空間。 這表示此 XAML 檔案定義新的類別，名為`MainPage`中`XamlSamples`衍生自命名空間`ContentPage`— 所在標記`x:Class`屬性隨即顯示。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

`x:Class`屬性會指定完整的.NET 類別名稱：`XamlSamplesPage`類別`XamlSamples`命名空間。 這表示此 XAML 檔案定義新的類別，名為`XamlSamplesPage`中`XamlSamples`衍生自命名空間`ContentPage`— 所在標記`x:Class`屬性隨即顯示。

-----

`x:Class`屬性只能出現在要衍生的 C# 類別定義的 XAML 檔案的根項目。 這是唯一的 XAML 檔案中定義的類別。 所有其他項目出現在 XAML 檔案中是只要從現有類別具現化而初始化。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

**MainPage.xaml.cs**檔案看起來像這樣 (除了用未使用`using`指示詞):

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

`MainPage`類別衍生自`ContentPage`，但請注意`partial`類別定義。 這可能表示應該會有另一個部分類別定義`MainPage`，但它嗎？ 為何，`InitializeComponent`方法？ 

當 Visual Studio 建置專案時，它會剖析 XAML 檔案來產生 C# 程式碼檔案。 若您查看**XamlSamples\XamlSamples\obj\Debug**目錄中，您可以找到名為**XamlSamples.MainPage.xaml.g.cs**。 產生代表 'g'。 這是其他部分類別定義`MainPage`，其中包含定義`InitializeComponent`方法呼叫從`MainPage`建構函式。 這些兩個部分`MainPage`類別定義然後一起編譯。 根據是否或不可以編譯 XAML，XAML 檔案或 XAML 檔案以二進位格式會內嵌在可執行檔。

在執行階段程式碼在特定平台的專案呼叫`LoadApplication`方法，傳遞給它的新執行個體`App`PCL 中的類別。 `App`類別建構函式具現化`MainPage`。 該類別的建構函式呼叫`InitializeComponent`，然後呼叫`LoadFromXaml`PCL 從擷取的 XAML 檔案 （或其已編譯的二進位檔） 的方法。 `LoadFromXaml` 初始化 XAML 檔案中定義的所有物件、 一起連接中父子式關聯性、 附加在 XAML 檔案中，設定事件的程式碼中定義的事件處理常式以及設定物件的結果樹狀結構頁面的內容。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

**XamlSamplesPage.xaml.cs**檔案看起來像這樣：

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class XamlSamplesPage : ContentPage
    {
        public XamlSamplesPage()
        {
            InitializeComponent();
        }
    }
}
```

`XamlSamplesPage`類別衍生自`ContentPage`，但請注意`partial`類別定義。 那里建議應該會有另一個 C# 檔案的另一個部分類別定義`XamlSamplesPage`，但它嗎？ 為何，`InitializeComponent`方法？

當 Visual Studio for Mac 建置專案時，它會剖析 XAML 檔案來產生 C# 程式碼檔案。 若您查看**XamlSamples\XamlSamples\obj\Debug**目錄中，您可以找到名為**XamlSamples.XamlSamplesPage.xaml.g.cs**。 產生代表 'g'。 這是其他部分類別定義`XamlSamplesPage`，其中包含定義`InitializeComponent`方法呼叫從`XamlSamplesPage`建構函式。  這些兩個部分`XamlSamplesPage`類別定義然後一起編譯。 根據是否或不可以編譯 XAML，XAML 檔案或 XAML 檔案以二進位格式會內嵌在可執行檔。

在執行階段程式碼在特定平台的專案呼叫`LoadApplication`方法，傳遞給它的新執行個體`App`PCL 中的類別。 `App`類別建構函式具現化`XamlSamplesPage`。 該類別的建構函式呼叫`InitializeComponent`，然後呼叫`LoadFromXaml`PCL 從擷取的 XAML 檔案 （或其已編譯的二進位檔） 的方法。 `LoadFromXaml` 初始化 XAML 檔案中定義的所有物件、 一起連接中父子式關聯性、 附加在 XAML 檔案中，設定事件的程式碼中定義的事件處理常式以及設定物件的結果樹狀結構頁面的內容。

-----

雖然您通常不需要花費的時間產生的程式碼檔案，有時執行階段引發例外狀況是程式碼在產生的檔案，因此您應該熟悉這些。

當您編譯和執行此程式，`Label`如所示的 XAML 項目會出現在頁面的中央。 從左到右的三個平台為 iOS、 Android 和 UWP:

[![](get-started-with-xaml-images/xamlsamples.png "預設 Xamarin.Forms 顯示")](get-started-with-xaml-images/xamlsamples-large.png#lightbox "預設 Xamarin.Forms 顯示")

更有趣的視覺效果，您只需要為更多有趣的 XAML。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="preliminaries"></a>準備工作

若要讓檔案名稱在 Visual Studio for Mac 與在 Windows 下執行的 Visual Studio 所建立的檔案一致，重新命名**XamlSamplesPage.xaml**至**MainPage.xaml**，和**XamlSamplesPage.xaml.cs**至**MainPage.xaml.cs**。 內**XamlSamplesPage.xaml**檔案中，變更`XamlSamplesPage`至`MainPage`。 內**XamlSamplesPage.xaml.cs**檔案中，變更出現兩次的`XamlSamplesPage`至`MainPage`。 內**App.xaml.cs**檔案中，變更陳述式

```csharp
MainPage = new XamlSamplesPage();
```

變更為：

```csharp
MainPage = new MainPage();
```

-----

測試的程式仍會編譯並部署然後再繼續。

## <a name="adding-new-xaml-pages"></a>加入新的 XAML 頁面

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要加入其他以 XAML 為基礎`ContentPage`類別加入您專案中，選取**XamlSamples** PCL 專案，並叫用**專案 > 加入新項目**功能表項目。 左邊的**加入新項目**對話方塊中，選取**Visual C#** 和**Xamarin.Forms**。 從清單中選取**內容頁面**(不**內容頁面 (C#)**，它會建立僅限程式碼頁面上，或**內容檢視**，不是頁面)。 指定頁面名稱，例如**HelloXamlPage.xaml**:

![](get-started-with-xaml-images/win/addnewitemdialog.w157.png "加入新項目 對話方塊")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要加入其他以 XAML 為基礎`ContentPage`類別加入您專案中，選取**XamlSamples** PCL 專案，並叫用**檔案 > 新的檔案**功能表項目。 左邊的**新檔案**對話方塊中，選取**Form**左邊和**Form ContentPage Xaml** (不**Form ContentPage**，建立僅限程式碼頁面上，或**內容檢視**，不是頁面)。 指定頁面名稱，例如**HelloXamlPage**:

![](get-started-with-xaml-images/mac/newfiledialog.png "新的檔案對話方塊")

-----

兩個檔案加入至專案， **HelloXamlPage.xaml**和程式碼後置檔案**HelloXamlPage.xaml.cs**。 

## <a name="setting-page-content"></a>設定頁面內容

編輯**HelloXamlPage.xaml**檔案，所以只有標記，這些會用於`ContentPage`和`ContentPage.Content`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>
        
    </ContentPage.Content>
</ContentPage>
```

`ContentPage.Content`標籤是唯一的 XAML 語法的一部分。 首先，它們可能會顯示無效的 XML，但是可以合法。 在期限不在 XML 中的特殊字元。

`ContentPage.Content`標記稱為*屬性項目*標記。 `Content` 是的屬性`ContentPage`，而且通常設定為單一檢視] 或 [子檢視的版面配置。 屬性通常會變成屬性在 XAML 中，但很難設定`Content`屬性設定為複雜物件。 基於這個原因，屬性會表示為類別名稱和以句號分隔的屬性名稱所組成的 XML 項目。 現在`Content`屬性可以設定成介於`ContentPage.Content`標記，像這樣：

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

也請注意，`Title`根標記上設定屬性。

在這個階段中，類別、 屬性和 XML 之間的關聯性應該能看出： Xamarin.Forms 類別 (例如`ContentPage`或`Label`) 會出現在 XAML 檔案儲存為 XML 項目。 該類別的屬性，包括`Title`上`ContentPage`和七項屬性的`Label`— 通常會顯示為 XML 屬性。

若要設定這些屬性的值存在許多的快速鍵。 有些屬性是基本資料型別:，例如`Title`和`Text`屬性屬於類型`String`，`Rotation`的型別`Double`，和`IsVisible`(也就是`true`依預設，而且僅適用於此處設定圖例所示） 是型別`Boolean`。

`HorizontalTextAlignment`屬性屬於型別`TextAlignment`，這是列舉型別。 對於任何列舉類型的屬性，您只需要提供是成員名稱。

對於更複雜型別的屬性，不過，轉換器會用於剖析 XAML。 這些是衍生自 Xamarin.Forms 中的類別`TypeConverter`。 許多都是公用類別，但某些則不會。 針對這個特定的 XAML 檔案，這些類別的數個播放在幕後的角色：

-  `LayoutOptionsConverter` 如`VerticalOptions`屬性
-  `FontSizeConverter` 如`FontSize`屬性
-  `ColorTypeConverter` 如`TextColor`屬性

這些轉換會控管的屬性設定可允許的語法。

`ThicknessTypeConverter`可以處理其中一個、 兩個或四個以逗號分隔的數字。 如果提供一個數字，它適用於所有的四個邊。 兩個數字，與第一個是 left 和 right 填補，而第二個上方和下方。 四個號碼都順序左邊、 上方、 右側，與下方。

`LayoutOptionsConverter`可轉換的公用靜態欄位的名稱`LayoutOptions`結構類型的值以`LayoutOptions`。

`FontSizeConverter`可以處理`NamedSize`成員或數值的字型大小。

`ColorTypeConverter`可接受的公用靜態欄位的名稱`Color`結構或十六進位 RGB 值，不論 alpha 色板，前面加上數字符號 （#）。 以下是沒有 alpha 色板語法：

 `TextColor="#rrggbb"`

每個小的字母是十六進位數字。 以下是如何 alpha 色板就會包含：

 `TextColor="#aarrggbb">`

Alpha 色板，請記住，FF 會完全不透明，和 00 是完全透明。

其他兩種格式可讓您指定只是單一十六進位數字的每個通道：

 `TextColor="#rgb"` `TextColor="#argb"`

在這些情況下，該數字會形成值重複。 例如，#CF3 是 RGB 色彩副本-F-33。

## <a name="page-navigation"></a>頁面巡覽

當您執行**XamlSamples**程式`MainPage`隨即出現。 若要查看新`HelloXamlPage`可以設定它作為新的啟動頁面**App.xaml.cs**檔案，或瀏覽至新的頁面從`MainPage`。

若要實作導覽，請先變更中的程式碼**App.xaml.cs**建構函式，讓`NavigationPage`建立物件：

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

在**MainPage.xaml.cs**建構函式，您可以建立簡單`Button`並瀏覽至使用此事件處理常式`HelloXamlPage`:

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

設定`Content`頁的屬性會取代的設定`Content`XAML 檔案中的屬性。 當您編譯和部署此程式的新版本時，按鈕會出現在螢幕上。 按下它瀏覽至`HelloXamlPage`。 以下是 [結果] 頁面上 iPhone、 Android 和 UWP:

[![](get-started-with-xaml-images/helloxaml1.png "旋轉標籤文字")](get-started-with-xaml-images/helloxaml1-large.png#lightbox "旋轉標籤文字")

您可以瀏覽回到`MainPage`使用 **< 回**在 iOS 上，使用向左箭號，在頁面頂端或底部的電話，在 Android 上，或在頁面頂端的 Windows 10 上使用向左箭號按鈕。

請放心試驗不同的方式來呈現的 XAML `Label`。 如果您要內嵌在文字中的任何 Unicode 字元，您可以使用標準的 XML 語法。 比方說，若要將問候語智慧引號中，使用：

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

看起來如下：

[![](get-started-with-xaml-images/helloxaml2.png "使用 Unicode 字元的標籤文字的旋轉")](get-started-with-xaml-images/helloxaml2-large.png#lightbox "旋轉標籤文字使用 Unicode 字元")

## <a name="xaml-and-code-interactions"></a>XAML 和程式碼互動

**HelloXamlPage**範例包含只有一個`Label`在頁面上，但這是很常見的事。 大部分`ContentPage`衍生項目集`Content`的部分的配置屬性排序，例如`StackLayout`。 `Children`屬性`StackLayout`定義型別`IList<View>`但實際類型的物件，則`ElementCollection<View>`，和集合可以填入多個檢視或其他鍵盤配置。 在 XAML 中，這些父子式關聯性會建立與一般 XML 階層。 以下是名為的新頁面的 XAML 檔案**XamlPlusCodePage**:

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

此 XAML 檔案語法完成，且它起來：

[![](get-started-with-xaml-images/xamlpluscode1.png "在頁面上的多個控制項")](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox "頁面上的多個控制項")

不過，您可能會考慮此程式會在功能上缺點。 可能是`Slider`應該會造成`Label`來顯示目前的值，而`Button`可能會執行在程式內的項目。

您會發現在[第 4 部分。資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)，顯示的工作`Slider`值使用`Label`可以處理在 XAML 中，資料繫結。 但很有用，可以先查看程式碼方案。 即便如此，處理`Button`按一下絕對需要的程式碼。 這表示的程式碼後置檔案`XamlPlusCodePage`必須包含的處理常式`ValueChanged`事件`Slider`和`Clicked`事件`Button`。 讓我們加入它們：

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

傳回在 XAML 檔案中，`Slider`和`Button`標記要包含的屬性`ValueChanged`和`Clicked`參考這些處理常式的事件：

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

請注意，指派的事件處理常式有與指派給屬性的值相同的語法。

如果處理常式`ValueChanged`事件`Slider`將使用`Label`若要顯示的目前值，這個處理常式需要從程式碼中參考該物件。 `Label`需要指定名稱`x:Name`屬性。

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

`x`的前置詞`x:Name`屬性會指出這個屬性是為 XAML 內建函式。

指派給`x:Name`屬性具有相同的規則，做為 C# 變數名稱。 例如，它必須以字母或底線開頭，而且不得包含內嵌的空格。

現在`ValueChanged`事件處理常式可以設定`Label`以顯示新`Slider`值。 新的值是可從事件引數：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

或處理常式無法取得`Slider`物件會將產生此事件從`sender`引數，並取得`Value`不同的屬性：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

當您第一次執行程式時，`Label`不會顯示`Slider`值，因為`ValueChanged`尚未還會引發事件。 但操作`Slider`會使顯示值：

[![](get-started-with-xaml-images/xamlpluscode2.png "顯示的滑桿值")](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox "滑桿顯示的值")

現在針對`Button`。 讓我們來模擬的回應`Clicked`所顯示之警示的事件`Text` 按鈕。 此事件處理常式可以安全地轉型`sender`引數`Button`，然後存取其屬性：

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

這個方法定義為`async`因為`DisplayAlert`方法為非同步且應該前面加上`await`運算子，就會傳回此方法完成時。 因為這個方法會取得`Button`引發的事件`sender`引數，相同的處理常式可用於多個按鈕。

在 XAML 中定義的物件可以引發事件會在程式碼後置檔案中，處理和程式碼後置檔案都可以存取的物件定義在 XAML 中使用的名稱指派給它與您已看過`x:Name`屬性。 這些是互動的程式碼和 XAML 的兩種基本方式。

某些其他深入了解如何非法 XAML 運作方式，藉由檢查新產生**XamlPlusCode.xaml.g.cs 檔案**，現在包含指派給任何任何名稱`x:Name`的私用欄位的屬性。 以下是該檔案的簡化的版本：

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

這個欄位的宣告，可讓 「 自由內任何位置使用的變數`XamlPlusCodePage`您管轄區下的部分類別檔案。 在執行階段，在已剖析 XAML，會被指派的欄位。 這表示`valueLabel`欄位是`null`時`XamlPlusCodePage`建構函式開始之後但有效`InitializeComponent`呼叫。

之後`InitializeComponent`傳回控制項回到建構函式，就如同必須已具現化，而且在程式碼中初始化已經建構頁面的視覺效果。 XAML 檔案不在類別中扮演任何角色。 您可以使用這些物件在任何您想要的方式，比方說，藉由新增檢視 頁面上操作`StackLayout`，或設定`Content`屬性時，為其他項目頁面完全。 您可以 「 查核樹狀結構 」 藉由檢查`Content`屬性頁面中的項目`Children`的版面配置的集合。 您可以設定這種方式，存取檢視的屬性，或動態指派給它們的事件處理常式。

您可以任意去。 它是您的網頁，且 XAML 建置其內容的工具。

## <a name="summary"></a>總結

使用本簡介，您已經看到如何 XAML 檔案和程式碼檔案構成類別定義，以及 XAML 和程式碼檔案的互動方式。 但是 XAML 也有自己唯一的語法功能，讓它使用非常大的彈性的方式。 您可以開始瀏覽這些[第 2 部分。基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)。



## <a name="related-links"></a>相關連結

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 2 部分：基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
