---
title: Xamarin.Forms Shell
description: 本文說明如何使用 Xamarin.Forms Shell，其可藉由提供大部分應用程式需要的基本 UI 功能，降低 Xamarin.Forms 應用程式的複雜性。
ms.prod: xamarin
ms.assetid: 1A674212-72DB-4AA4-B626-A4EC135AD1A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2018
ms.openlocfilehash: 933368f7ca1435ece4f20945b2f8e905f7584217
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899381"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

![預覽](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/Microsoft/TailwindTraders-Mobile)

Xamarin.Forms Shell 是應用程式的容器，可提供大部分應用程式需要的基本 UI 功能，讓您專注於應用程式的核心工作負載。 Shell 應用程式提供下列功能：

- 說明應用程式視覺結構的單一位置。
- 常見的導覽使用者介面。
- 含有深層連結的導覽服務。
- 整合式搜尋處理常式。

這項功能會降低應用程式的複雜性，同時提高開發人員的生產力。 此外，Shell 的撰寫還需考量轉譯速度和記憶體耗用量。

> [!IMPORTANT]
> 現有的 iOS 和 Android 應用程式可採用 Shell，立即受益於導覽、效能和擴充性等改進功能。

Shell 提供根據飛出視窗和索引標籤的固定導覽使用者介面。 Shell 應用程式中導覽的最上層是飛出視窗：

![飛出視窗](shell-images/flyout.png "飛出視窗")

導覽的下一個層級是底端索引標籤列：

![底端索引標籤](shell-images/bottom-tabs-full.png "底端索引標籤")

飛出視窗未開啟時，底端索引標籤列將視為導覽的最上層

在每個底端索引標籤內，接下來的導覽層級是頂端索引標籤列，其中每個索引標籤是 `ContentPage`：

![頂端索引標籤](shell-images/top-tabs-full.png "頂端索引標籤")

在每個 `ContentPage` 中，可以在導覽堆疊中新增及移除額外的 `ContentPage` 執行個體。

## <a name="bootstrapping-a-shell-application"></a>進行 Shell 應用程式的啟動程序

藉由將 `App` 類別的 `MainPage` 屬性設定為新的 `Shell` 執行個體，以進行 Shell 應用程式的啟動程序：

```csharp
namespace TailwindTraders.Mobile
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();

            Forms.SetFlags("Shell_Experimental");
            MainPage = new Shell();
        }
    }
}
```

`Shell` 類別會說明應用程式視覺結構的 XAML 檔案。

> [!IMPORTANT]
> Shell 目前為實驗性，只能在建立 `Shell` 執行個體之前藉由將 `Forms.SetFlags("Shell_Experimental");` 新增至您的 `App` 類別來使用，或是在叫用 `Forms.Init` 方法之前，新增至您的平台專案來使用。

## <a name="shell-file-hierarchy"></a>Shell 檔案階層

Shell 檔案由三個階層式元素所組成：

- `ShellItem`. 飛出視窗中的一或多個項目。 每個 `ShellItem` 是 `Shell` 的子系。
- `ShellSection`. 分組內容，可透過底端索引標籤導覽。 每個 `ShellSection` 是 `ShellItem` 的子系。
- `ShellContent`. 應用程式中的 `ContentPage` 執行個體，可透過頂端索引標籤導覽。 每個 `ShellContent` 是 `ShellSection` 的子系。

這些元素都不代表任何使用者介面，而是代表應用程式視覺結構的組織。 Shell 將會採用這些元素，並產生用於內容的導覽使用者介面。

下列 XAML 顯示簡單的 `Shell` 檔案範例：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders"
       x:Class=" TailwindTraders.Shell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    <ShellItem>
</Shell>
```

> [!NOTE]
> 每個 `ShellItem` 也可以將 `FlyoutIcon` 屬性設定為 `ImageSource`，這會顯示在標題的左側。

此 XAML 會顯示 `HomePage`，因為它是 Shell 檔案中宣告內容的第一個項目：

![首頁](shell-images/homepage.png "首頁")

按下漢堡按鈕會顯示飛出視窗：

![開啟的飛出視窗](shell-images/flyout-initial.png "開啟的飛出視窗")

在 Shell 檔案中新增更多的 `ShellItem` 執行個體，可以增加飛出視窗中的項目數。 但請注意，`HomePage` 是在應用程式啟動期間建立的，使用這種方法新增其他的 `ShellItem` 執行個體將導致這些頁面也會在應用程式啟動期間建立。 透過將 `ShellContent.ContentTemplate` 屬性為 `DataTemplate` 可避免這種狀況：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders"
       x:Class=" TailwindTraders.Shell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    <ShellItem>
    <ShellContent Title="Profile"
                  ContentTemplate="{DataTemplate local:ProfilePage}" />
</Shell>
```

使用此方法，`ProfilePage` 只會在使用者巡覽到它時建立，而不是在應用程式啟動時建立。 此外，還請注意，針對 `ProfilePage` 已省略 `ShellItem` 和 `ShellSection` 物件，並在 `ShellContent` 執行個體上定義了 `Title` 屬性。 這種精簡的方法需要較少的標記，Shell 會提供必要的邏輯包裝函式 (不會在視覺化樹狀結構中引入其他的檢視)。

此外，透過將 `Shell.ItemTemplate` 屬性設定為 `DataTemplate`，即可自訂每個 `ShellItem` 外觀：

```xaml
<Shell.ItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Title}" />
        </ContentView>
    </DataTemplate>
</Shell.ItemTemplate>          
```

此程式碼只會重新置放飛出視窗內每個 `ShellItem` 的文字。 請注意，Shell 會提供 `Title` (與 `Icon`) 屬性給 `DataTemplate` 的 `BindingContext`。

## <a name="flyout"></a>Flyout

飛出視窗是應用程式的根功能表，由飛出視窗標題和功能表項目所組成：

![標註的飛出視窗](shell-images/flyout-annotated.png "標註的飛出視窗")

### <a name="flyout-behavior"></a>飛出視窗行為

飛出視窗可透過漢堡按鈕或從螢幕側邊撥動來進行存取。 不過，透過將 `Shell.FlyoutBehavior` 屬性設定為其中一個 `FlyoutBehavior` 列舉成員，即可變更此行為：

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

將 `FlyoutBehavior` 屬性設定為 `Disabled` 會隱藏飛出視窗，當您只有一個 `ShellItem` 時，這非常有用。 其他有效的 `FlyoutBehavior` 值為 `Flyout`(預設) 和 `Locked`。

### <a name="flyout-header"></a>飛出視窗標題

飛出視窗標題是在飛出視窗頂端選擇性顯示的內容，其外觀是由可透過 `Shell.FlyoutHeader` 屬性值設定的 `View` 來定義：

```xaml
<Shell.FlyoutHeader>
    <local:FlyoutHeader />
</Shell.FlyoutHeader>
```

或者，透過將 `Shell.FlyoutHeaderTemplate` 屬性設定為 `DataTemplate`，即可定義飛出視窗標題的外觀：

```xaml
<Shell.FlyoutHeaderTemplate>
    <DataTemplate>
        <StackLayout HorizontalOptions="Fill"
                     VerticalOptions="Fill"
                     BackgroundColor="White"
                     Padding="16">
            <Label FontSize="Medium"
                   Text="Smart Shopping">
                <Label.Margin>
                    <Thickness Left="8" />
                </Label.Margin>
            </Label>
            <Button Image="photo"
                    Text="By taking a photo">
                <Button.Margin>
                    <Thickness Top="16" />
                </Button.Margin>
            </Button>
            <Button Image="ia"
                    Text="By using AR">
                <Button.Margin>
                    <Thickness Top="8" />
                </Button.Margin>
            </Button>
        </StackLayout>
    </DataTemplate>
</Shell.FlyoutHeaderTemplate>
```

此 XAML 會導致下列的飛出視窗標題：

![飛出視窗標題](shell-images/flyout-header.png "飛出視窗標題")

根據預設，飛出視窗標題將會固定在飛出視窗中，而如果有足夠的項目，以下內容就會捲動。 不過，透過將 `Shell.FlyoutHeaderBehavior` 屬性設定為其中一個 `FlyoutHeaderBehavior` 列舉成員，即可變更此行為：

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

將 `FlyoutHeaderBehavior` 設定為 `CollapseOnScroll` 就會在進行捲動時摺疊飛出視窗。 其他有效的 `FlyoutHeaderBehavior` 值為 `Default`、`Fixed` 和 `Scroll` (隨著功能表項目捲動)。

## <a name="menu-items"></a>功能表項目

新增更多的 `ShellItem` 執行個體，可以增加飛出視窗中的項目數。 不過，也可以將 `MenuItem` 執行個體新增至飛出視窗。 這允許一些案例，例如巡覽至相同的頁面，同時透過 `MenuItem.CommandParameter` 屬性傳遞資料。

`MenuItem` 執行個體應新增至 `Shell.MenuItems` 集合：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Views"
       x:Class="TailwindTraders.Shell"
       FlyoutHeaderBehavior="Fixed"
       Title="Tailwind Traders"
       ...>
    <Shell.MenuItems>
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="1"
                  Text="Holiday decorations" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="2"
                  Text="Appliances" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="3"
                  Text="Bathrooms" />
        ...
    </Shell.MenuItems>
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    </ShellItem>    
</Shell>
```

> [!NOTE]
> 每個 `MenuItem` 也可以將 `Icon` 屬性設定為 `FileImageSource`，這會顯示在文字的左側。

此 XAML 會導致下列的飛出視窗：

![完整的飛出視窗](shell-images/flyout-full.png "完整的飛出視窗")

此外，透過將 `Shell.MenuItemTemplate` 屬性設定為 `DataTemplate`，即可自訂 `MenuItem` 外觀：

```xaml
<Shell.MenuItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Text}"
                   FontAttributes="Bold" />
        </ContentView>
    </DataTemplate>
</Shell.MenuItemTemplate>
```

這會導致 `MenuItem` 執行個體的文字以粗體呈現：

![粗體的功能表項目](shell-images/menuitems-bold.png "粗體的功能表項目")

## <a name="tabs"></a>定位點

`ShellSection` 執行個體將會呈現為底端索引標籤，前提是在單一 `ShellItem` 中有多個 `ShellSection` 執行個體：

```xaml
<ShellItem Title="Bottom Tab Sample"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="AR" Icon="ia.png">
        <ShellContent ContentTemplate="{DataTemplate local:ARPage}"/>
    </ShellSection>
    <ShellSection Title="Photo" Icon="photo.png">
        <ShellContent ContentTemplate="{DataTemplate local:PhotoPage}"/>
    </ShellSection>
</ShellItem>
```

在此範例中，`ShellSection` 執行個體將會呈現為底端索引標籤：

![底端索引標籤](shell-images/tabs-bottom.png "底端索引標籤")

`ShellContent` 項目將會呈現為頂端索引標籤，前提是在單一 `ShellSection` 內有多個 `ShellContent` 執行個體：

```xaml
<ShellItem Title="Store Home"
           Shell.TitleView="Store Home"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="Browse Product">
        <ShellContent Title="Featured"
                      ContentTemplate="{DataTemplate local:FeaturedPage}" />
        <ShellContent Title="On Sale"
                      ContentTemplate="{DataTemplate local:SalePage}" />
    </ShellSection>
</ShellItem>
```

在此範例中，`ShellContent` 執行個體將會呈現為頂端索引標籤：

![頂端索引標籤](shell-images/tabs-top.png "頂端索引標籤")

您可以使用 XAML 樣式或藉由提供自訂轉譯器，來設定索引標籤的樣式。 例如，下列範例顯示設定索引標籤色彩的 XAML 樣式：

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.ShellTabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.ShellTabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.ShellTabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

## <a name="navigation"></a>巡覽

Shell 包含以 URI 為基礎的導覽體驗。 URI 提供已改進的導覽體驗，允許導覽至應用程式中的任何頁面，而不需要遵循設定的導覽階層。 此外，它還提供向後巡覽的能力，而不需瀏覽導覽堆疊上的所有頁面。

這種以 URI 為基礎的導覽是利用路由來完成的，而路由是用來在應用程式內進行巡覽的 URI 區段。 `Shell` 檔案必須宣告路由配置、路由主機和路由：

```xaml
<Shell ...
       Route="tailwindtraders"
       RouteHost="www.microsoft.com"
       RouteScheme="app">
    ...
</Shell>
```

合併後，`RouteScheme`、`RouteHost` 和 `Route` 屬性值會構成 `app://www.microsoft.com/tailwindtraders` 根 URI。

`Shell` 類別中的每個項目，也可以定義可用於以程式設計方式巡覽的路由屬性。

在 `Shell` 建構函式或在叫用路由之前執行的任何其他位置中，可以針對 Shell 元素 (例如 `MenuItem` 執行個體) 未代表的任何頁面，明確註冊其他路由：

```csharp
Routing.RegisterRoute("productcategory", typeof(ProductCategoryPage));
```

### <a name="implementing-navigation"></a>實作導覽

功能表項目會公開可用來實作導覽的 `Command` 屬性：

```csharp
public ICommand ProductTypeCommand { get; } = new Command<string>(NavigateToProductType);

static void NavigateToProductType(string typeId)
{
  (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"app:///tailwindtraders/productcategory?id={typeId}", true);
}
```

若要叫用導覽，必須透過 `Application` 類別的 `MainPage` 屬性取得應用程式 `Shell` 的參考。 然後，呼叫 `GoToAsync` 方法，並將有效的 URI 作為引數傳遞，即可叫用導覽。 `GoToAsync` 方法使用 `ShellNavigationState` 物件來巡覽，而該物件是從 `string` 或 `Uri` 建構而成。

### <a name="passing-data"></a>傳遞資料

您可以透過查詢字串參數在頁面間傳遞資料。 當您將查詢屬性 (property) 的屬性 (attribute) 新增至類別時，Shell 會在 `ContentPage` 或 ViewModel 上設定查詢字串參數值：

```csharp
[QueryProperty("TypeID", "id")]
public partial class ProductCategoryPage : ContentPage
{
    private string _typeId;

    public ProductCategoryPage()
    {
        InitializeComponent();

        BindingContext = new ProductCategoryViewModel();
    }

    public string TypeID
    {
        get => _typeId;
        set => MyLabel.Text = value;
    }
}
```

`QueryProperty` 屬性會指定 `TypeID` 將從 `GoToAsync` 方法呼叫中的 URI，接收 `id` 查詢字串參數中所傳入的資料。

### <a name="intercepting-navigation"></a>攔截導覽

Shell 提供可在導覽路由完成之前或之後連結到其中的能力。 分別登錄 `Navigating` 和 `Navigated` 事件的事件處理常式，即可達成此目的：

```xaml
<Shell ...
       Navigating="OnShellNavigating">
    ...
</Shell>
```

```csharp
void OnShellNavigating(object sender, ShellNavigatingEventArgs e)
{
  if (...)
  {
    e.Cancel(); // Cancel the navigation
  }
}
```

`ShellNavigatingEventArgs` 類別提供下列屬性：

| 屬性 | 類型 | 描述 |
|---|---|---|
| 目前 | `ShellNavigationState` | 目前頁面的 URI。 |
| 原始程式檔 | `ShellNavigationState` | 代表導覽起源的 URI。 |
| 目標 | `ShellNavigationSource`  | 代表導覽目的地的 URI。 |
| CanCancel  | `bool` | 指出它是否可以取消導覽的值。 |
| 已取消  | `bool` | 指出是否已取消導覽的值。 |

此外，`ShellNavigatingEventArgs` 類別還提供 `Cancel` 方法。

`ShellNavigatedEventArgs` 類別提供下列屬性：

| 屬性 | 類型 | 描述 |
|---|---|---|
| 目前 | `ShellNavigationState` | 目前頁面的 URI。 |
| 上一步| `ShellNavigationState` | 上一頁的 URI。 |
| 原始程式檔  | `ShellNavigationSource` | 代表導覽起源的 URI。 |

此外，Shell 提供可覆寫的 `OnBackButtonPressed` 方法，此方法可用來攔截上一頁按鈕的點選。

## <a name="related-links"></a>相關連結

- [Tailwind Traders (範例)](https://github.com/Microsoft/TailwindTraders-Mobile)
