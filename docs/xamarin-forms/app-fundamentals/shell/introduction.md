---
title: Xamarin.Forms Shell 簡介
description: Xamarin.Forms Shell 提供大部分應用程式需要的基本功能，包括常見的導覽使用者體驗、URI 式導覽配置，以及整合式搜尋處理常式。
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 20d9fb79d03990824dd884b62138a3e29b3ee04f
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65054478"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

![](~/media/shared/preview.png "此 API 目前是預先發行版本")

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/Xaminals/)

Xamarin.Forms Shell 會提供大部分行動應用程式需要的基本功能，藉此降低行動應用程式開發的複雜度，這些基本功能包括：

- 說明應用程式視覺階層的單一位置。
- 常見的導覽使用者體驗。
- 允許導覽至應用程式中任何頁面的 URI 式導覽配置。
- 整合式搜尋處理常式。

此外，Shell 應用程式的優點是轉譯速度更快，且記憶體使用量更少。

> [!IMPORTANT]
> 現有的 iOS 和 Android 應用程式可採用 Shell，立即受益於導覽、效能和擴充性等改進功能。

Shell 目前為實驗性，只能在叫用 `Forms.Init` 方法之前，新增 `Forms.SetFlags("Shell_Experimental");` 至您的平台專案來使用。

# <a name="androidtabandroid"></a>[Android](#tab/android)

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        global::Xamarin.Forms.Forms.SetFlags("Shell_Experimental");

        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());
    }
}
```

# <a name="iostabios"></a>[iOS](#tab/ios)

```csharp
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.SetFlags("Shell_Experimental");

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new App());

        return base.FinishedLaunching(app, options);
    }
}
```

----

## <a name="shell-navigation-experience"></a>Shell 導覽體驗

Shell 會根據飛出視窗和索引標籤，提供固定的導覽體驗。 Shell 應用程式中導覽的最上層是飛出視窗：

[![Shell 飛出視窗在 iOS 和 Android 上的螢幕擷取畫面](introduction-images/flyout.png "Shell 飛出視窗")](introduction-images/flyout-large.png#lightbox "Shell 飛出視窗")

選取飛出視窗項目會產生代表要選取並顯示之項目的底部索引標籤：

[![Shell 底部索引標籤在 iOS 和 Android 上的螢幕擷取畫面](introduction-images/monkeys.png "Shell 底部索引標籤")](introduction-images/monkeys-large.png#lightbox "Shell 底部索引標籤")

> [!NOTE]
> 未開啟飛出視窗時，會將底部的索引標籤列視為應用程式中導覽的最上層。

每個索引標籤都會顯示一個 [`ContentPage`](xref:Xamarin.Forms.ContentPage)。 不過，如果底部索引標籤包含多個頁面，則可透過頂端索引標籤列導覽頁面：

[![Shell 頂端索引標籤在 iOS 和 Android 上的螢幕擷取畫面](introduction-images/cats.png "Shell 頂端索引標籤")](introduction-images/cats-large.png#lightbox "Shell 頂端索引標籤")

在每個索引標籤中，都可以導覽至其他 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件：

[![Shell 頁面導覽在 iOS 和 Android 上的螢幕擷取畫面](introduction-images/cat-details.png "Shell 應用程式導覽")](introduction-images/cat-details-large.png#lightbox "Shell 應用程式導覽")

## <a name="subclassing-the-shell-class"></a>將 Shell 類別子類別化

子類別化的 `Shell` 物件描述 Shell 應用程式的視覺階層，並由三個主要的階層式物件所組成：

- `FlyoutItem`，代表飛出視窗中的一或多個項目。 每個 `FlyoutItem` 物件都是 `Shell` 物件的子系。
- `Tab`，代表分組內容，可透過底部索引標籤導覽。 每個 `Tab` 物件都是一個 `FlyoutItem` 物件的子系。
- `ShellContent`，代表應用程式中的 `ContentPage` 物件。 每個 `ShellContent` 物件都是一個 `Tab` 物件的子系。 當多個 `ShellContent` 物件出現在 `Tab` 中時，物件將可透過頂端索引標籤導覽。

這些物件都不代表任何使用者介面，而是代表應用程式視覺階層的組織。 Shell 將會採用這些物件，並產生用於內容的導覽使用者介面。

> [!NOTE]
> `FlyoutItem` 類別為 `ShellItem` 類別的別名，而 `Tab` 類別則為 `ShellSection` 類別的別名。 這些別名的設計可讓 API 更容易取用。

下列 XAML 會顯示子類別化 `Shell` 物件的範例：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    ...
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png">
                <views:CatsPage />
            </ShellContent>
            <ShellContent Title="Dogs"
                          Icon="dog.png">
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png">
            <views:MonkeysPage />
        </ShellContent>
        <ShellContent Title="Elephants"
                      Icon="elephant.png">  
            <views:ElephantsPage />
        </ShellContent>
        <ShellContent Title="Bears"
                      Icon="bear.png">
            <views:BearsPage />
        </ShellContent>
    </FlyoutItem>
    ...
</Shell>
```

執行時，此 XAML 會顯示 `CatsPage`，因為它是子類別化 `Shell` 物件中宣告之內容的第一個項目：

[![Shell 應用程式在 iOS 和 Android 上的螢幕擷取畫面](introduction-images/cats.png "Shell 應用程式")](introduction-images/cats-large.png#lightbox "Shell 應用程式")

按下漢堡圖示，或從左邊撥動可顯示飛出視窗：

[![Shell 飛出視窗在 iOS 和 Android 上的螢幕擷取畫面](introduction-images/flyout-reduced.png "Shell 飛出視窗")](introduction-images/flyout-reduced-large.png#lightbox "Shell 飛出視窗")

> [!IMPORTANT]
> 在 Shell 應用程式中，屬於 `ShellContent` 物件子系的每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 都是在應用程式啟動期間建立的。 使用此方法新增其他 `ShellContent` 物件將會導致在應用程式啟動期間建立其他頁面，進而可能導致啟動經驗不佳。 不過，Shell 也能夠依需求建立頁面，以回應導覽。 如需詳細資訊，請參閱 [Xamarin.Forms Shell 索引標籤](tabs.md)指南中的[有效率的頁面載入](tabs.md#efficient-page-loading)。

## <a name="bootstrapping-a-shell-application"></a>進行 Shell 應用程式的啟動程序

藉由將 `App` 類別的 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 屬性設定為子類別化的 `Shell` 物件，以進行 Shell 應用程式的啟動程序：

```csharp
namespace Xaminals
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new AppShell();
        }
        ...
    }
}
```

在此範例中，`AppShell` 類別是衍生自 `Shell` 類別的 XAML 檔案，可描述應用程式的視覺階層。

## <a name="shell-appearance"></a>Shell 外觀

`Shell` 類別會定義可控制 Shell 應用程式外觀的下列屬性：

- `ShellBackgroundColor`，屬於 `Color` 類型，這是定義 Shell Chrome 背景色彩的一種附加屬性。 色彩將不會填入 Shell 內容的背景。
- `ShellDisabledColor`，屬於 `Color` 類型，這是定義遭停用之陰影文字和圖示色彩的一種附加屬性。
- `ShellForegroundColor`，屬於 `Color` 類型，這是定義陰影文字和圖示之色彩的一種附加屬性。
- `ShellTitleColor`，屬於 `Color` 類型，這是定義用於目前頁面標題之色彩的一種附加屬性。
- `ShellUnselectedColor`，屬於 `Color` 類型，這是定義用於 Shell Chrome 中未選取文字和圖示之色彩的一種附加屬性。

所有這些屬性都以 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件為後盾，也就是說，這些屬性可以是資料繫結的目標。

此外，這些屬性可以使用階層式樣式表 (CSS) 來設定。 如需詳細資訊，請參閱 [Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

## <a name="shell-content-layout"></a>Shell 內容版面配置

`Shell` 類別會定義影響 Shell 應用程式內容版面配置的下列屬性：

- `NavBarIsVisible`，屬於 `boolean` 類型，這是定義呈現頁面時是否應該顯示導覽列的一種附加屬性。 此屬性應該在頁面上設定，且其預設值為 `true`。
- `SetPaddingInsets`，屬於 `bool` 類型，這是控制頁面內容是否在任何 Shell Chrome 底下流動的一種附加屬性。 此屬性應該在頁面上設定，且其預設值為 `false`。
- `TabBarIsVisible`，屬於 `bool` 類型，這是定義呈現頁面時是否應該顯示索引標籤列的一種附加屬性。 此屬性應該在頁面上設定，且其預設值為 `true`。
- `TitleView`，屬於 `View` 類型，這是定義頁面之 `TitleView` 的一種附加屬性。 此屬性應該在頁面上設定。

所有這些屬性都以 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件為後盾，也就是說，這些屬性可以是資料繫結的目標。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/Xaminals/)
- [Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
