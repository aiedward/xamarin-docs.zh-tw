---
title: Mac 平台設定
description: 這篇文章說明如何將 Mac 專案新增至 Xamarin.Forms 專案，將會產生能夠在 macOS Sierra 和 macOS El Capitan 上執行的應用程式。
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
ms.openlocfilehash: 3a488b3a9f729da5d4bee8c1262190b15c2e9240
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60902674"
---
# <a name="mac-platform-setup"></a>Mac 平台設定

![預覽](~/media/shared/preview.png)

在開始之前，建立 （或使用現有的） 的 Xamarin.Forms 專案。 您只能新增 Visual Studio for mac 中使用的 Mac 應用程式

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**藉由將 macOS 專案新增至 Xamarin.Forms， [Xamarin University](https://university.xamarin.com/)**

## <a name="adding-a-mac-app"></a>新增 Mac 應用程式

請遵循下列指示來將 Mac 應用程式將在 macOS Sierra 和 macOS El Capitan 上執行：

1. 在 Visual Studio for Mac，以滑鼠右鍵按一下現有的 Xamarin.Forms 方案，然後選擇 **新增 > 新增專案...**

2. 在 [**新的專案**視窗中選擇**Mac > 應用程式 > Cocoa 應用程式**按**下一步]**。

3. 型別**應用程式名稱**（並選擇性地選擇 [停駐項目不同的名稱），然後按**下一步]**。

4. 檢閱設定然後按**建立**。 這些步驟如下所示：

    ![動畫的指示，說明如何新增 Cocoa 應用程式](mac-images/add-macos-proj.gif)

5. 在 Mac 專案中，以滑鼠右鍵按一下**封裝 > 新增套件...** 來新增[Xamarin.Forms](https://www.nuget.org/packages/Xamarin.Forms/) NuGet。 您也應該更新為使用相同版本的 Xamarin.Forms NuGet 套件的其他專案。

6. 在 Mac 專案中，以滑鼠右鍵按一下**參考**並新增至 Xamarin.Forms 專案 （共用專案或.NET Standard 程式庫專案） 的參考。

    ![新增 Xamarin.Forms 共用程式碼專案的參考](mac-images/references-sml.png)

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

8. 更新`AppDelegate`初始化 Xamarin.Forms，建立視窗，以及載入 Xamarin.Forms 應用程式 (記住設定適當`Title`)。 _如果您需要初始化其他相依性，請執行，這裡也。_

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

9. 按兩下**Main.storyboard**在 Xcode 中編輯。 選取 [ **] 視窗**並_取消核取_**是初始的控制器**核取方塊 （這是因為上述程式碼會建立一個視窗）：

    [![取消核取 在 Xcode 中是初始的控制器核取方塊](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

    您可以編輯功能表系統中移除不必要的項目分鏡腳本。

10. 最後，新增任何本機資源 （例如。 影像檔） 從現有的平台專案所需。

11. Mac 專案現在應在 macOS 上執行您的 Xamarin.Forms 程式碼 ！

## <a name="next-steps"></a>後續步驟

### <a name="styling"></a>樣式

最近所做的變更至`OnPlatform`您現在可以將目標平台的任何數字。 這包括 macOS。

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

請注意，您可能也加倍像這樣的平台上： `<On Platform="iOS, macOS" ...>`。

### <a name="window-size-and-position"></a>視窗大小和位置

您可以調整的初始大小和位置中的視窗`AppDelegate`:

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>已知問題

這會是預覽，因此，您應該預期不是所有項目是用於生產環境。 以下是您在將 macOS 新增至您的專案可能會遇到的幾件事：

### <a name="not-all-nugets-are-ready-for-macos"></a>並非所有的 Nuget 已可供 macOS

您可能會發現，您使用的程式庫的一些尚不支援 macOS。 在此情況下，您必須將要求傳送至專案的維護程式用來將它加入。 沒有支援，您可能需要尋找替代項目。

### <a name="missing-xamarinforms-features"></a>遺漏的 Xamarin.Forms 功能

並非所有的 Xamarin.Forms 功能都已在此預覽版中完成的。 如需詳細資訊，請參閱 <<c0> [ 平台支援 macOS 狀態](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support-macOS-Status)中[Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms) GitHub 存放庫。

## <a name="related-links"></a>相關連結

- [Xamarin.Mac](~/mac/index.yml)
