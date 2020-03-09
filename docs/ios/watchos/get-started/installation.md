---
title: 在 Xamarin 中安裝和使用 watchOS
description: 本檔說明如何安裝和使用 watchOS 搭配 Xamarin。 它討論安裝、watchOS 專案結構、如何使用 iOS 設計工具、Xcode 整合，以及提供疑難排解秘訣。
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/05/2017
ms.openlocfilehash: f986099011dbccb0eb43c62d253ee497d46ca08e
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915556"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>在 Xamarin 中安裝和使用 watchOS

watchOS 4 需要具有 Xcode 9 的 macOS Sierra （10.12）。

watchOS 1 原本需要使用 Xcode 7 的 OS X Yosemite （10.10）。

> [!WARNING]
> [2018 年4月1日之後，將不會接受 watchOS 1 更新](https://developer.apple.com/news/?id=11162017a)。 未來的更新必須使用 watchOS 2 SDK 或更新版本;建議使用 watchOS 4 SDK 來建立。

## <a name="project-structure"></a>專案結構

監看式應用程式是由三個專案所組成：

- **Xamarin IOS iPhone 應用程式專案**-這是一般的 iphone 專案，可以是任何一個 Xamarin 範本。 監看式應用程式及其延伸模組將會配套在此主要專案內。

- **監看延伸專案**-這包含監看式應用程式的程式碼（例如控制器類別）。

- **監看應用程式專案**-這包含具有 Watch 應用程式所有 UI 資源的使用者介面分鏡腳本檔案。

「[監看套件類別目錄」範例](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)解決方案在 Xamarin 中看起來像這樣：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![](installation-images/catalog-solution.png "The solution in Visual Studio")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](installation-images/catalog-solution-vs.png "The solution in Visual Studio")

-----

下載並執行[WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)範例，以開始使用。
您可以在 [[控制項](~/ios/watchos/user-interface/index.md)] 頁面上找到範例中的畫面。

## <a name="creating-a-new-project"></a>建立新專案

您無法建立新的「監看式解決方案」 .。。相反地，您可以將 Watch 應用程式新增至現有的 iOS 應用程式。 請遵循下列步驟來建立監看式應用程式：

1. 如果您沒有現有的專案，請先選擇 檔案 **> 新方案**，然後建立 iOS 應用程式（例如，**單一 View 應用程式**）：

    [![](installation-images/cycle8-2-sml.png "Choose File > New Solution and create an iOS app")](installation-images/cycle8-2.png#lightbox)

2. 建立 iOS 應用程式（或您打算使用現有的 iOS 應用程式）之後，以滑鼠右鍵按一下方案，然後選擇 [**新增] > [加入新專案**]。在 [**新增專案**] 視窗中，選取 [ **WatchOS] > 應用程式 > WatchKit 應用程式**：

    [![](installation-images/cycle8-6-sml.png "Select watchOS > App > WatchKit App")](installation-images/cycle8-6.png#lightbox)

3. 下一個畫面可讓您選擇應包含監看式應用程式的 iOS 應用程式專案：

    [![](installation-images/cycle8-7-sml.png "Choose which iOS app project should include the watch app")](installation-images/cycle8-7.png#lightbox)

4. 最後，選擇要儲存專案的位置（並選擇性地啟用原始檔控制）：

    [![](installation-images/cycle8-8-sml.png "Choose the location to save the project")](installation-images/cycle8-8.png#lightbox)

5. Visual Studio for Mac 會自動為您[設定專案參考和**plist**設定](~/ios/watchos/get-started/project-references.md)。

## <a name="creating-the-watch-user-interface"></a>建立監看式使用者介面

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>使用 Xamarin iOS 設計工具

按兩下 [監看式] 應用程式的**介面。** 使用 iOS 設計工具進行編輯的腳本。 您可以從 [**工具箱**] 將介面控制器和 UI 控制項拖曳至分鏡腳本，並使用**Properties** pad 加以設定：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![](installation-images/iosdesigner-sml.png "The storyboard in the Designer")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![](installation-images/iosdesigner-sml-vs.png "The storyboard in the Designer")](installation-images/iosdesigner-vs.png#lightbox)

-----

您應該選取一個類別，然後在**Properties** pad 中輸入名稱（這會自動建立必要C#的程式碼後置檔案），為每個新介面控制器提供一個**類別**：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![](installation-images/iosdesigner-classname.png "Give each new interface controller a Class")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](installation-images/iosdesigner-classname-vs.png "Give each new interface controller a Class")

-----

**Ctrl +** 從按鈕、表格或介面控制器拖曳至另一個介面控制器，以建立 segue。

### <a name="using-xcode-on-the-mac"></a>在 Mac 上使用 Xcode

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

您可以繼續使用 Xcode 來建立使用者介面，方法是以滑鼠右鍵按一下介面. 分鏡腳本檔案，然後選取 [**開啟方式]，> Xcode Interface Builder**：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Visual Studio 使用者也可以使用 Xcode 來建立使用者介面，方法是切換至直接使用 Mac 組建主機。
在 Visual Studio for Mac 中開啟您的方案，然後以滑鼠右鍵按一下介面. 分鏡腳本檔案，然後選取 [**開啟方式] > Xcode Interface Builder**：

-----

![](installation-images/openwith-xcode.png "Open the Interface.storyboard in Xcode Interface Builder")

如果使用 Xcode，您應該遵循針對一般[iOS 應用程式](~/ios/user-interface/storyboards/index.md)分鏡腳本監看應用程式的相同步驟（例如，藉由**Ctrl + 拖曳**至 **.h**標頭檔來建立輸出和動作）。

當您在 Xcode 中儲存腳本時 Interface Builder 它會自動將您建立的輸出和動作新增C#至 watch 擴充功能專案中的**designer.cs**檔案。

### <a name="adding-additional-screens-in-xcode"></a>在 Xcode 中新增其他畫面

當您使用 Interface Builder Xcode 將其他畫面新增至您的分鏡腳本時（預設為超過範本中包含的內容），**您必須手動C#** 新增每個新介面控制器的程式碼檔案。

請參閱[Advanced 指示，以瞭解如何將新的介面控制器加入至分](~/ios/watchos/troubleshooting.md#add)鏡腳本。

*Xamarin iOS 設計工具會自動執行此操作，不需要任何手動步驟。*

## <a name="building"></a>建置

包含監看式應用程式組建的專案，與其他 iOS 專案類似。 建立程式會產生一個包含監看式擴充功能（. appex）的 iPhone 應用程式（. app），而此延伸模組又包含無程式碼監看式應用程式（. app）。

## <a name="launching"></a>正在

您可以使用 Visual Studio for Mac 或 Visual Studio （在 Mac 組建主機上啟動），啟動模擬器中的監看式應用程式。

有兩種模式可啟動 WatchKit 應用程式：

- 一般應用程式模式（預設值）和
- [通知](~/ios/watchos/platform/notifications.md)（需要 JSON 格式的測試通知承載）。

### <a name="xcode-8-support"></a>Xcode 8 支援

一旦安裝了 Xcode 8 （或更新版本），Apple Watch 模擬器會與 iOS 模擬器分開（不同于[Xcode 6](#xcode6)，它們會顯示為*外部顯示器*）。
當您選取 [監看式應用程式] 專案並將它設為 [啟始專案] 時，模擬器清單會顯示*iOS*模擬器供您選擇（如下所示）。

[![](installation-images/xs-xcode8-watchos3-sml.png "Selecting the Simulator type")](installation-images/xs-xcode8-watchos3.png#lightbox)

當您開始進行調試時，應該啟動*兩個*模擬器-iOS 模擬器*和*Apple Watch 模擬器。 使用**Command + Shift + H**流覽至 [監看式] 功能表和 [時鐘臉部];並使用 [**硬體**] 功能表來設定**Force Touch 壓力**。 軌跡板或滑鼠的滾動會使用 Digital Crown 來模擬。

#### <a name="troubleshooting"></a>疑難排解

如果您嘗試啟動至沒有配對監看的模擬器，**應用程式輸出**中將會出現下列錯誤：

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

如需設定模擬器的指示，請參閱[Apple 的論壇](https://forums.developer.apple.com/thread/7783)（如果預設值不適用）。

<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 和 watchOS 1

您必須先將*監看式擴充功能專案*設為**啟始專案**，然後再執行或對應用程式進行偵錯工具。 您不能「啟動」監看式應用程式本身，如果您選擇 iOS 應用程式，它就會在 iOS 模擬器中正常啟動。

根據預設，監看式應用程式會從 Visual Studio for Mac 的**執行**或**Debug**命令，以標準**應用程式**模式（而非 [概覽] 或 [通知] 模式）啟動

使用 Xcode 6 時，只有 iPhone 5、iPhone 5 秒、iPhone 6 和 iPhone 6 Plus 可以啟用**Apple Watch-38mm**或**Apple Watch-42mm**的外部顯示器，其中會顯示監看式應用程式。

> [!NOTE]
> 請記住，使用 Xcode 6 時，[監看式] 畫面不會自動出現在 iOS 模擬器中。
> 使用 [**硬體 > 外部顯示**] 功能表來顯示 [監看式] 畫面。

<a name="custommodes" />

## <a name="launching-notification-mode"></a>正在啟動通知模式

如需如何在程式碼中處理通知的詳細資訊，請參閱[通知頁面](~/ios/watchos/platform/notifications.md)。

Visual Studio for Mac 可以使用通知_啟動模式_來啟動監看式應用程式，以取得通知：

以滑鼠右鍵按一下 [監看式應用程式] 專案，然後選擇 [以 **> 自訂設定執行**]：

[![](installation-images/runwith-customparams-sml.png "Running a Custom Configuration")](installation-images/runwith-customparams.png#lightbox)

這會開啟 [**自訂參數**] 視窗，您可以在其中選取 [**通知**] （並提供 JSON 承載），然後按下 [**執行**] 以啟動模擬器中的監看式應用程式：

[![](installation-images/runwith-execargs-sml.png "Setting the Notification and Payload")](installation-images/runwith-execargs.png#lightbox)

## <a name="debugging"></a>偵錯

Visual Studio for Mac 和 Visual Studio 都支援調試。
請記得在 [通知] 模式中進行調試時，提供通知 JSON 檔案。 這個螢幕擷取畫面顯示在監看式應用程式中叫用的 debug 中斷點：

![](installation-images/debug-sml.png "This screenshot shows a debug breakpoint being hit in a watch app")

遵循啟動指示之後，您會得到在 IOS 模擬器上執行的監看式應用程式 **（watch）** 。
在 [通知模式] 中，您可以選取 [ **Debug] > 開啟 [系統記錄**檔（**CMD +/** ）]，然後在程式碼中使用 `Console.WriteLine`。

### <a name="debugging-lifecycle-event-handlers"></a>偵錯工具生命週期事件處理常式

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

WatchOS 範本檔案（例如 `InterfaceController`、`ExtensionDelegate`、`NotificationController`和 `ComplicationController`）隨附已實作為其所需的生命週期方法。 新增 `Console.WriteLine` 呼叫，並讀取**應用程式輸出**，以進一步瞭解事件生命週期。

## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [第一次監看應用程式影片](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的 WatchKit 秘訣](https://developer.apple.com/watchkit/tips/)
