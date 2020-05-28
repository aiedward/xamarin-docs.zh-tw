---
標題： [建立 Xamarin.Forms Shell 應用程式] 描述： ' 建立 Xamarin.Forms shell 應用程式的流程是建立可子類別化 shell 類別的 XAML 檔案，將應用程式類別的 MainPage 屬性設定為子類別化的 shell 物件，然後在子類別化的 shell 類別中描述應用程式的視覺階層。
assetid： ms. 技術： author： ms. 作者： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="create-a-xamarinforms-shell-application"></a>建立 Xamarin.Forms Shell 應用程式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

建立 Shell 應用程式的流程如下所示 Xamarin.Forms ：

1. 建立新的 Xamarin.Forms 應用程式，或載入您想要轉換成 Shell 應用程式的現有應用程式。
1. 將 XAML 檔案新增至子類別化 `Shell` 類別的共用程式碼專案。 如需詳細資訊，請參閱[將 Shell 類別子類別化](#subclass-the-shell-class)。
1. 將 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 應用程式類別的屬性設定 `App` 為子類別化的 `Shell` 物件。 如需詳細資訊，請參閱[進行 Shell 應用程式啟動程序](#bootstrap-the-shell-application)。
1. 在子類別化 `Shell` 類別中描述應用程式視覺階層。 如需詳細資訊，請參閱[描述應用程式視覺階層](#describe-the-visual-hierarchy-of-the-application)。

## <a name="subclass-the-shell-class"></a>將 Shell 類別子類別化

建立 Shell 應用程式的第一個步驟 Xamarin.Forms ，是將 XAML 檔案加入至子類別的共用程式碼專案中 `Shell` 。 這個檔案可命名為任何名稱，但建議命名為 **AppShell**。 下列程式碼範例顯示新建的 **AppShell.xaml** 檔案：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell">

</Shell>
```

下列範例顯示程式碼後置檔案 **AppShell.xaml.cs**：

```csharp
using Xamarin.Forms;

namespace Xaminals
{
    public partial class AppShell : Shell
    {
        public AppShell()
        {
            InitializeComponent();
        }
    }
}
```

## <a name="bootstrap-the-shell-application"></a>進行 Shell 應用程式啟動程序

建立子類別化物件的 XAML 檔案之後 `Shell` ，應該將類別的屬性設定為子類別化的 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) `App` `Shell` 物件：

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

在此範例中，`AppShell` 類別是衍生自 `Shell` 類別的 XAML 檔案。

> [!NOTE]
> 雖然可建置空白 Shell 應用程式，但執行它將會導致擲回 `InvalidOperationException`。

## <a name="describe-the-visual-hierarchy-of-the-application"></a>描述應用程式視覺階層

建立 Shell 應用程式的最後一個步驟， Xamarin.Forms 是在子類別中描述應用程式的視覺階層 `Shell` 。 子類別化 `Shell` 類別由三個主要階層式物件所組成：

- `FlyoutItem` 或 `TabBar`。 `FlyoutItem` 代表飛出視窗中的一或多個項目，當應用程式的導覽模式包含飛出視窗時應使用它。 `TabBar` 代表底部索引標籤列，當應用程式的導覽模式是從底部索引標籤開始時應使用它。 每個 `FlyoutItem` 物件或 `TabBar` 物件都是 `Shell` 物件的子系。
- `Tab`，代表分組內容，可透過底部索引標籤導覽。 每個 `Tab` 物件都是 `FlyoutItem` 物件或 `TabBar` 物件的子系。
- `ShellContent`，代表應用程式中的 `ContentPage` 物件。 每個 `ShellContent` 物件都是一個 `Tab` 物件的子系。 當多個 `ShellContent` 物件出現在 `Tab` 中時，物件將可透過頂端索引標籤導覽。

這些物件都不代表任何使用者介面，而是代表應用程式視覺階層的組織。 Shell 將會採用這些物件，並產生用於內容的導覽使用者介面。

下列 XAML 會顯示子類別化 `Shell` 類別的範例：

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

執行時，此 XAML 會顯示 `CatsPage`，因為它是子類別化 `Shell` 類別中宣告之內容的第一個項目：

[![Shell 應用程式在 iOS 和 Android 上的螢幕擷取畫面](create-images/cats.png "Shell 應用程式")](create-images/cats-large.png#lightbox "Shell 應用程式")

按下漢堡圖示，或從左邊撥動可顯示飛出視窗：

[![iOS 與 Android 上的 Shell 飛出視窗螢幕擷取畫面](create-images/flyout-reduced.png "Shell 飛出視窗")](create-images/flyout-reduced-large.png#lightbox "Shell 飛出視窗")

> [!IMPORTANT]
> 在 Shell 應用程式中，屬於物件子系的每個都 [`ContentPage`](xref:Xamarin.Forms.ContentPage) `ShellContent` 是在應用程式啟動期間建立的。 使用此方法新增其他 `ShellContent` 物件將會導致在應用程式啟動期間建立其他頁面，進而可能導致啟動經驗不佳。 不過，Shell 也能夠依需求建立頁面，以回應導覽。 如需詳細資訊，請參閱[ Xamarin.Forms Shell](tabs.md)索引標籤指南中的[有效率的頁面載入](tabs.md#efficient-page-loading)。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
