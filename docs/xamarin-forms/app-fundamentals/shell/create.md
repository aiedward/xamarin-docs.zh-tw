---
title: 建立 Xamarin.Forms Shell 應用程式
description: 建立 Xamarin.Forms shell 應用程式的程式是建立將 shell 類別子類別化的 XAML 檔案、將應用程式之應用程式類別的 MainPage 屬性設定為子類別化的 shell 物件，然後在子類別化 shell 類別中描述應用程式的視覺化階層。
ms.prod: xamarin
ms.assetid: 2A51D78F-6CD5-4BC4-A62E-11CEFA799987
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/15/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f02deecbd372e7125fe13aaed37ad08f2cc4faf6
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757589"
---
# <a name="create-a-xamarinforms-shell-application"></a>建立 Xamarin.Forms Shell 應用程式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

建立 Xamarin.Forms Shell 應用程式的程式如下所示：

1. 建立新的 Xamarin.Forms 應用程式，或載入您想要轉換成 Shell 應用程式的現有應用程式。
1. 將 XAML 檔案新增至共用程式碼專案，子類別化 [`Shell`](xref:Xamarin.Forms.Shell) 類別。 如需詳細資訊，請參閱[將 Shell 類別子類別化](#subclass-the-shell-class)。
1. 將 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 應用程式類別的屬性設定 `App` 為子類別化的 [`Shell`](xref:Xamarin.Forms.Shell) 物件。 如需詳細資訊，請參閱[進行 Shell 應用程式啟動程序](#bootstrap-the-shell-application)。
1. 描述子類別化類別中的應用程式視覺階層 [`Shell`](xref:Xamarin.Forms.Shell) 。 如需詳細資訊，請參閱[描述應用程式視覺階層](#describe-the-visual-hierarchy-of-the-application)。

如需如何建立 Shell 應用程式的逐步解說，請參閱 [建立 Xamarin.Forms 應用程式快速入門](~/get-started/quickstarts/app.md)。

## <a name="subclass-the-shell-class"></a>將 Shell 類別子類別化

建立 Shell 應用程式的第一個步驟 Xamarin.Forms 是將 XAML 檔案新增至子類別化類別的共用程式碼專案 [`Shell`](xref:Xamarin.Forms.Shell) 。 這個檔案可命名為任何名稱，但建議命名為 **AppShell**。 下列程式碼範例顯示新建的 **AppShell.xaml** 檔案：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="MyApp.AppShell">

</Shell>
```

下列範例顯示程式碼後置檔案 **AppShell.xaml.cs**：

```csharp
using Xamarin.Forms;

namespace MyApp
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

建立子類別化物件的 XAML 檔案之後 [`Shell`](xref:Xamarin.Forms.Shell) ， [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 應該將類別的屬性設為子類別化 `App` `Shell` 物件：

```csharp
namespace MyApp
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

在此範例中， `AppShell` 類別是衍生自類別的 XAML 檔案 [`Shell`](xref:Xamarin.Forms.Shell) 。

> [!WARNING]
> 雖然空白的 Shell 應用程式將會建立，但嘗試執行它會導致擲回 `InvalidOperationException` 。

## <a name="describe-the-visual-hierarchy-of-the-application"></a>描述應用程式視覺階層

建立 Shell 應用程式的最後一個步驟 Xamarin.Forms ，是在子類別化類別中描述應用程式的視覺化階層 [`Shell`](xref:Xamarin.Forms.Shell) 。 子類別化 `Shell` 類別由三個主要階層式物件所組成：

1. [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 或 [`TabBar`](xref:Xamarin.Forms.TabBar) 。 `FlyoutItem`代表飛出視窗中的一或多個專案，且應在應用程式的導覽模式需要飛出視窗時使用。 代表底部索引標籤列 `TabBar` ，當應用程式的導覽模式以底部索引標籤開始，且不需要飛出視窗時，就應該使用此選項。 每個 `FlyoutItem` 物件或 `TabBar` 物件都是物件的子系 [`Shell`](xref:Xamarin.Forms.Shell) 。
1. [`Tab`](xref:Xamarin.Forms.Tab)，代表分組的內容，可依底部索引標籤導覽。 每個 `Tab` 物件都是 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 物件或物件的子系 [`TabBar`](xref:Xamarin.Forms.TabBar) 。
1. [`ShellContent`](xref:Xamarin.Forms.ShellContent)，代表每個索引標籤的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件。每個 `ShellContent` 物件都是物件的子系 [`Tab`](xref:Xamarin.Forms.Tab) 。 當多個 `ShellContent` 物件出現在 `Tab` 中時，物件將可透過頂端索引標籤導覽。

這些物件不代表任何使用者介面，而是應用程式視覺階層的組織。 Shell 將會採用這些物件，並產生用於內容的導覽使用者介面。

下列 XAML 顯示子類別化類別的範例 [`Shell`](xref:Xamarin.Forms.Shell) ：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    ...
    <FlyoutItem FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png"
                          ContentTemplate="{DataTemplate views:CatsPage}" />
            <ShellContent Title="Dogs"
                          Icon="dog.png"
                          ContentTemplate="{DataTemplate views:DogsPage}" />
        </Tab>
        <!--
        Shell has implicit conversion operators that enable the Shell visual hierarchy to be simplified.
        This is possible because a subclassed Shell object can only ever contain a FlyoutItem object or a TabBar object,
        which can only ever contain Tab objects, which can only ever contain ShellContent objects.

        The implicit conversion automatically wraps the ShellContent objects below in Tab objects.
        -->
        <ShellContent Title="Monkeys"
                      Icon="monkey.png"
                      ContentTemplate="{DataTemplate views:MonkeysPage}" />
        <ShellContent Title="Elephants"
                      Icon="elephant.png"
                      ContentTemplate="{DataTemplate views:ElephantsPage}" />
        <ShellContent Title="Bears"
                      Icon="bear.png"
                      ContentTemplate="{DataTemplate views:BearsPage}" />
    </FlyoutItem>
    ...
</Shell>
```

執行時，此 XAML 會顯示 `CatsPage` ，因為它是子類別目錄類別中宣告之內容的第一個專案 [`Shell`](xref:Xamarin.Forms.Shell) ：

[![螢幕擷取畫面： iOS 和 Android 上的 Shell 應用程式](create-images/cats.png)](create-images/cats-large.png#lightbox)

按下漢堡圖示，或從左邊撥動可顯示飛出視窗：

[![螢幕擷取畫面： iOS 和 Android 上的 Shell 飛出視窗](create-images/flyout.png)](create-images/flyout-large.png#lightbox)

因為 [`FlyoutDisplayOptions`](xref:Xamarin.Forms.ShellGroupItem.FlyoutDisplayOptions) 屬性設為，所以會在飛出視窗中顯示多個專案 `AsMultipleItems` 。 如需詳細資訊，請參閱 [飛出視窗顯示選項](flyout.md#flyout-display-options)。

> [!IMPORTANT]
> 在 Shell 應用程式中，會依需求建立頁面，以回應導覽。 這是藉由使用 [`DataTemplate`](xref:Xamarin.Forms.Xaml.DataTemplateExtension) 標記延伸，將 [`ContentTemplate`](xref:Xamarin.Forms.ShellContent.ContentTemplate) 每個物件的屬性設定 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件來完成。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [建立 Xamarin.Forms 應用程式快速入門](~/get-started/quickstarts/app.md)
