---
title: Mac 平臺設定
description: 本文說明如何將 Mac 專案新增至 Xamarin.Forms 專案，以產生可在 macOS Sierra 和 MacOS El Capitan 上執行的應用程式。
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5a5d26813719bfe807fff6745683fa13bde4429e
ms.sourcegitcommit: a0de974875f8fa1a29f7abc990137246789ad85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2021
ms.locfileid: "100630146"
---
# <a name="mac-platform-setup"></a>Mac 平臺設定

:::image type="icon" source="~/media/shared/preview.png" border="false":::

開始之前，請建立 (或使用現有的) Xamarin.Forms 專案。 您只能使用 Visual Studio for Mac 新增 Mac 應用程式。

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**將 macOS 專案新增至 Xamarin.Forms 影片**

## <a name="adding-a-mac-app"></a>新增 Mac 應用程式

遵循這些指示來新增將在 macOS Sierra 和 macOS El Capitan 上執行的 Mac 應用程式：

1. 在 Visual Studio for Mac 中，以滑鼠右鍵按一下現有的 Xamarin.Forms 方案，然後選擇 [**加入] > [加入新專案**]。

2. 在 [**新增專案**] 視窗中，選擇 [ **Mac > App > Cocoa 應用程式**]，然後按 **[下一步**

3. 輸入 **應用程式名稱** (，並選擇性地為 Dock 專案) 選擇不同的名稱，然後按 **[下一步]**。

4. 檢查設定，然後按 [ **建立**]。 這些步驟如下所示：

    ![顯示如何新增 Cocoa 應用程式的動畫指示](mac-images/add-macos-proj.gif)

5. 在 Mac 專案中，以滑鼠右鍵按一下 **套件，> 新增套件**] 以新增 NuGet。 [Xamarin.Forms](https://www.nuget.org/packages/Xamarin.Forms/) 您也應該更新其他專案，以使用相同版本的 Xamarin.Forms NuGet 套件。

6. 在 Mac 專案中，以滑鼠右鍵按一下 [ **參考** ]，然後加入專案的參考， Xamarin.Forms (共用專案或 .NET Standard 程式庫專案) 。

    ![將參考新增至：：：非 loc (Xamarin. Forms) ：：： shared code 專案](mac-images/references-sml.png)

7. 更新 **Main.cs** 以初始化 `AppDelegate` ：

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

8. 更新 `AppDelegate` 以初始化 Xamarin.Forms 、建立視窗和載入 Xamarin.Forms 應用程式 (記得設定適當的 `Title`) 。 _如果您有其他需要初始化的相依性，也請在此進行。_

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

9. 按兩下 [ **Main** ]，以在 Xcode 中編輯。 選取 **視窗** ，並 _取消_ 核取 [ **為初始控制器** ] 核取方塊 (這是因為上述程式碼會建立視窗) ：

    [![取消核取 Xcode 中的 [是初始控制器] 核取方塊](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

    您可以編輯腳本中的功能表系統來移除不必要的專案。

10. 最後， (例如，新增任何本機資源。 從所需的現有平臺專案) 影像檔案。

11. Mac 專案現在應該會在 macOS 上執行您的程式 Xamarin.Forms 代碼！

## <a name="next-steps"></a>後續步驟

### <a name="styling"></a>樣式

有了最近所做 `OnPlatform` 的變更，您現在可以將目標設為任何數目的平臺。 這包括 macOS。

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

請注意，您也可以在這類平臺上加倍： `<On Platform="iOS, macOS" ...>` 。

### <a name="window-size-and-position"></a>視窗大小和位置

您可以在中調整視窗的初始大小和位置 `AppDelegate` ：

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>已知問題

這是預覽功能，因此您應該預期並非所有專案都已就緒。 以下是您在專案中新增 macOS 時可能會遇到的幾件事：

### <a name="not-all-nugets-are-ready-for-macos"></a>並非所有 Nuget 都準備好進行 macOS

您可能會發現您使用的一些程式庫尚未支援 macOS。 在此情況下，您必須將要求傳送至專案的維護程式以將它加入。 在支援之前，您可能需要尋找替代方案。

### <a name="missing-xamarinforms-features"></a>遺漏的 Xamarin.Forms 功能

並非所有 Xamarin.Forms 功能都已在此預覽版中完成。 如需詳細資訊，請參閱 GitHub 存放庫中的 [平臺支援 MacOS 狀態](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support-macOS-Status) [Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms) 。

## <a name="related-links"></a>相關連結

- [Xamarin.Mac](~/mac/index.yml)
