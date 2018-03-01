---
title: "Mac 平台設定"
description: "Xamarin.Forms 現在具有 Mac 平台支援預覽"
ms.topic: article
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
ms.openlocfilehash: bda207796d1019f8188176acce055d782cb9e32d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="mac-platform-setup"></a>Mac 平台設定

![預覽](~/media/shared/preview.png)

開始之前，建立 （或使用現有的） Xamarin.Forms 專案。
您只能新增 Mac 應用程式使用 Visual Studio for mac。

## <a name="adding-a-mac-app"></a>新增 Mac 應用程式

請遵循這些指示來新增利也和 Mac OS X El Capitan macOS 執行的 Mac 應用程式：

1. 在 Visual Studio for Mac，以滑鼠右鍵按一下現有的 Xamarin.Forms 方案，然後選擇 **新增 > 加入新的專案...**

2. 在**新專案**視窗選擇**Mac > 應用程式 > Cocoa 應用程式**按**下一步**。

3. 型別**應用程式名稱**（並選擇性地選擇不同的停駐項目名稱），然後按下**下一步**。

4. 檢閱設定然後按**建立**。 這些步驟如下所示：

  ![動畫顯示如何加入 Cocoa 應用程式的指示](mac-images/add-macos-proj.gif)

5. 在 Mac 專案中，以滑鼠右鍵按一下**封裝 > 新增套件...**新增[Xamarin.Forms/2.3.5.235-pre2](https://www.nuget.org/packages/Xamarin.Forms/2.3.5.235-pre2) NuGet。 您應該一併更新至此版本的其他專案。

6. 在 Mac 專案中，以滑鼠右鍵按一下**參考**並將參考加入 Xamarin.Forms （共用專案或 PCL）。

  ![加入 Xamarin.Forms 共用程式碼專案的參考](mac-images/references-sml.png)

7. 更新**Main.cs**初始化`AppDelegate`:

    ```csharp
    static class MainClass
    {
        static void Main(string[] args)
        {
            NSApplication.Init();
            NSApplication.SharedApplication.Delegate = new AppDelegate(); // add this line
            NSApplication.Main(args);
        }
    }
    ```

8. 更新`AppDelegate`初始化 Xamarin.Forms，建立一個視窗，以及載入 Xamarin.Forms 應用程式 (記住設定適當`Title`)。 _如果您有其他相依性，需要進行初始化時，此步驟中執行以及。_

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.MacOS;
    // also add a using for the Xamarin.Forms project, if the namespace is different to this file
    ...
    [Register("AppDelegate")]
    public class AppDelegate : FormsApplicationDelegate
    {
        NSWindow window;
        public AppDelegate()
        {
            var style = NSWindowStyle.Closable | NSWindowStyle.Resizable | NSWindowStyle.Titled;

            var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);
            window = new NSWindow(rect, style, NSBackingStore.Buffered, false);
            window.Title = "Xamarin.Forms on Mac!"; // choose your own Title here
            window.TitleVisibility = NSWindowTitleVisibility.Hidden;
        }

        public override NSWindow MainWindow
        {
            get { return window; }
        }

        public override void DidFinishLaunching(NSNotification notification)
        {
            Forms.Init();
            LoadApplication(new App());
            base.DidFinishLaunching(notification);
        }
    }
    ```

9. 按兩下**Main.storyboard**在 Xcode 中編輯。 選取**視窗**和_取消核取_**是初始控制器**核取方塊 （這是因為上述程式碼會建立一個視窗）：

  [ ![取消核取 在 Xcode 中的是初始控制器核取方塊](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png)

  您可以編輯功能表系統中移除不必要的項目分鏡腳本。

10. 最後，會加入任何本機資源 （例如。 影像檔） 從現有的平台專案所需。

11. Xamarin.Forms 程式現在應該執行 Mac 專案上 macOS ！

## <a name="next-steps"></a>後續步驟

### <a name="styling"></a>樣式

最近所做的變更至`OnPlatform`您現在可以將目標平台的任何數字。 其中包含 macOS。

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

請注意，您也可能會像這樣的平台上連按兩下： `<On Platform="iOS, macOS" ...>`。

### <a name="window-size-and-position"></a>視窗大小和位置

您可以調整的初始大小和位置中的視窗`AppDelegate`:

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>已知問題

這是預覽，因此，您應該預期不是所有項目是生產環境就緒。 以下是當您新增 macOS 至您的專案可能會遇到的幾件事：

### <a name="not-all-nugets-are-ready-for-macos"></a>並非所有 NuGets 都已做好 macOS

封裝必須在 macOS 專案中的目標"xamarinmac20"。 您可能會發現，部分您所使用的程式庫目前還不支援 macOS。

在此情況下，您必須將要求傳送至專案的維護程式用來將它加入。 直到他們有支援，您可能需要尋找替代項目。

### <a name="missing-xamarinforms-features"></a>遺漏 Xamarin.Forms 功能

並非所有 Xamarin.Forms 功能都都已完成在此預覽中;以下是一些尚未實作功能的清單：

* 頁尾
* 映像 – 外觀
* ListView – ScrollTo，UnevenRows 支援，重新整理，SeparatorColor，SeparatorVisibility
* MasterDetailPage – BackgroundColor
* 瀏覽 – InsertPageBefore
* OpenGLRenderer
* 選擇器 – Bindable/Observable 實作
* TabbedPage – BarBackgroundColor，BarTextColor
* TableView – UnevenRows
* ViewCell – IsEnabled，ForceUpdateSize
* WebView – 大部分 WebNavigationEvents


## <a name="related-links"></a>相關連結

- [Xamarin.Mac](~/mac/index.yml)
