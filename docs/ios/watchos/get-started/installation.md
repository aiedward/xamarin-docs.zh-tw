---
title: 安裝並在 Xamarin 中使用 watchOS
description: 本文件說明如何安裝和搭配 Xamarin 使用 watchOS。 它討論安裝、 watchOS 專案結構，如何使用 iOS 設計工具、 Xcode 整合，並提供疑難排解的秘訣。
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 12/05/2017
ms.openlocfilehash: 790e32131822e83f092b352b91b4461392150657
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768049"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>安裝並在 Xamarin 中使用 watchOS

watchOS 4 需要 macOS Sierra (10.12) 使用 Xcode 9。

watchOS 1 原本需要 Xcode 7 的 OS X Yosemite (10.10)。

> [!WARNING]
> [watchOS 1 更新將不會接受在 2018 年 4 月 1 日之後](https://developer.apple.com/news/?id=11162017a)。 未來的更新必須使用 watchOS 2 SDK 或更新版本;運用 watchOS 建置建議 4 SDK。

## <a name="project-structure"></a>專案結構

Watch 應用程式是由三個專案所組成：

- **IPhone 應用程式的 Xamarin.iOS 專案**-這是正常的 iPhone 專案，它可以是任何 Xamarin.iOS 範本。 Watch 應用程式和其擴充功能將會配套在此主要專案內。

- **監看延伸專案**-這包含監看式應用程式的程式碼（例如控制器類別）。

- **監看應用程式專案**-這包含具有 Watch 應用程式所有 UI 資源的使用者介面分鏡腳本檔案。

[監看式套件 Catalog 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)在 Xamarin.Studio 中的方案看起來像這樣：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](installation-images/catalog-solution.png "在 Visual Studio 方案")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/catalog-solution-vs.png "在 Visual Studio 方案")

-----

下載並執行[WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)範例著手。
來自範例的螢幕可於[控制項](~/ios/watchos/user-interface/index.md)頁面。

## <a name="creating-a-new-project"></a>建立新專案

您無法建立新 「 監看式解決方案 」...而是將 Watch 應用程式新增至現有的 iOS 應用程式。 請遵循下列步驟來建立監控應用程式：

1. 如果您沒有現有的專案，先選擇**檔案 > 新的解決方案**並建立 iOS 應用程式 (例如**單一檢視應用程式**):

    [![](installation-images/cycle8-2-sml.png "選擇 檔案 > 新的方案，並建立 iOS 應用程式")](installation-images/cycle8-2.png#lightbox)

2. 一旦建立 iOS 應用程式 （或您打算使用您現有的 iOS 應用程式），以滑鼠右鍵按一下方案，然後選擇 **新增 > 新增專案...** .在 **新的專案**視窗中選取**watchOS > 應用程式 > WatchKit 應用程式**:

    [![](installation-images/cycle8-6-sml.png "選取 watchOS > 應用程式 > WatchKit 應用程式")](installation-images/cycle8-6.png#lightbox)

3. 下一個畫面可讓您選擇哪一個 iOS 應用程式專案應包含監看式應用程式：

    [![](installation-images/cycle8-7-sml.png "選擇哪一個 iOS 應用程式專案應包含監看式應用程式")](installation-images/cycle8-7.png#lightbox)

4. 最後，選擇要儲存專案的位置 （並選擇性地啟用原始檔控制中）：

    [![](installation-images/cycle8-8-sml.png "選擇要儲存專案的位置")](installation-images/cycle8-8.png#lightbox)

5. Visual Studio for Mac 會自動設定[專案參考並**Info.plist**設定](~/ios/watchos/get-started/project-references.md)您。

## <a name="creating-the-watch-user-interface"></a>建立監看式使用者介面

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>使用 Xamarin iOS 設計工具

按兩下位於 watch 應用程式**Interface.storyboard**編輯使用 iOS 設計工具。 您可以將介面控制器和 UI 控制項拖曳至從分鏡腳本**工具箱**，並設定它們使用**屬性**板：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](installation-images/iosdesigner-sml.png "分鏡腳本設計工具中")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](installation-images/iosdesigner-sml-vs.png "分鏡腳本設計工具中")](installation-images/iosdesigner-vs.png#lightbox)

-----

您應該給予每個新的介面控制器**類別**選取它，然後再輸入中的名稱**屬性**板 (這會建立所需C#程式碼後置檔案會自動):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](installation-images/iosdesigner-classname.png "提供每個新的介面控制器類別")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/iosdesigner-classname-vs.png "提供每個新的介面控制器類別")

-----

建立的 segue **Ctrl + 拖曳**從按鈕、 表格或介面的控制器，到另一個介面控制器。

### <a name="using-xcode-on-the-mac"></a>在 Mac 上使用 Xcode

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

您可以繼續使用 Xcode 來建置您的使用者介面 Interface.storyboard 檔案上按一下滑鼠右鍵，然後選取**開啟 > Xcode Interface Builder**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio 的使用者也可以使用 Xcode 來建置其使用者介面，藉由切換至直接使用 「 Mac 組建主機。
開啟 Mac 在 Visual Studio 中的方案然後 Interface.storyboard 檔案上按一下滑鼠右鍵並選取**開啟 > Xcode Interface Builder**:

-----

![](installation-images/openwith-xcode.png "Xcode 介面產生器中開啟 Interface.storyboard")

如果使用 Xcode，然後您應該遵循相同的步驟，與一般的 watch 應用程式[iOS 應用程式的分鏡腳本](~/ios/user-interface/storyboards/index.md)(例如建立輸出和動作所**Ctrl + 拖曳**到 **.h**標頭檔)。

當您儲存分鏡腳本中將會自動加入的 Xcode Interface Builder 的輸出和您建立的動作C# **。 designer.cs**監看式延伸模組專案中的檔案。

### <a name="adding-additional-screens-in-xcode"></a>在 Xcode 中新增其他畫面

當您新增更多的畫面 （除了根據預設，項目都會包含在範本中） 來使用 Xcode 的 Interface Builder 分鏡腳本**您必須手動加入C#程式碼檔案**每個新介面控制站。

請參閱[如何將新的介面控制器加入至分鏡腳本上的進階指示](~/ios/watchos/troubleshooting.md#add)。

*Xamarin iOS 設計工具會自動執行此，不需要任何手動步驟。*

## <a name="building"></a>建置

包含監看式應用程式的專案組建等其他 iOS 專案。 建置程序會導致包含監看式延伸模組 (.appex)，後者又包含程式碼較不監看式應用程式 (.app) 的 iPhone 應用程式 (.app)。

## <a name="launching"></a>啟動

您可以啟動監看式應用程式在模擬器中使用 Visual Studio for Mac 或 Visual Studio （在 Mac 組建主機上啟動）。

有兩種模式啟動 WatchKit 應用程式：

- 一般應用程式模式 （預設值），以及
- [通知](~/ios/watchos/platform/notifications.md)（需要 JSON 格式的測試通知承載）。

### <a name="xcode-8-support"></a>Xcode 8 支援

一旦安裝 Xcode 8 （或更新版本） 時，Apple Watch 模擬器是分開 iOS 模擬器 (不同於[Xcode 6](#xcode6)，其中顯示作為*外部顯示器*)。
當您選取的監看式應用程式專案，並讓啟始專案時，模擬器清單會顯示*iOS 模擬器*可從中選擇 （如下所示）。

[![](installation-images/xs-xcode8-watchos3-sml.png "選取模擬器類型")](installation-images/xs-xcode8-watchos3.png#lightbox)

當您啟動偵錯*兩個*模擬器應該開始-iOS 模擬器*和*Apple Watch 模擬器。 使用 **命令 + Shift + H**瀏覽至 監看式功能表和時鐘表面，並使用**硬體**功能表來設定**強制觸控壓力**。 捲動滑鼠軌跡板上，將會模擬使用數位皇冠。

#### <a name="troubleshooting"></a>疑難排解

下列的錯誤會出現在**應用程式輸出**如果您嘗試啟動模擬器，並沒有配對的監看式：

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

請參閱[Apple 論壇](https://forums.developer.apple.com/thread/7783)如需有關設定模擬器，如果預設值無效。

<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 和 watchOS 1

您必須先 *監看式延伸模組專案* **啟始專案** 之前執行或偵錯應用程式。 您無法"start"watch 應用程式本身，和如果您選擇的 iOS 應用程式接著就會開始正常的 iOS 模擬器中。

預設 watch 應用程式會在標準模式中啟動**應用程式**從 Visual Studio for Mac 的模式 （不摘要 」 或 「 通知模式）**執行**或是**偵錯**命令。

使用 Xcode 6 時僅 iphone 5 以上、 iPhone 5 秒、 iPhone 6 及 iPhone 6 Plus 可以啟用適用於外部顯示器**Apple Watch-38 公釐**或是**Apple Watch-42 公釐**會監看式應用程式顯示此項目。

> [!NOTE]
> 請記住，使用 Xcode 6 時，[監看式] 畫面不會自動出現在 iOS 模擬器中。
> 使用**硬體 > 外接式顯示器之間**功能表顯示 [監看式] 畫面。

<a name="custommodes" />

## <a name="launching-notification-mode"></a>啟動通知模式

請參閱[通知頁面](~/ios/watchos/platform/notifications.md)資訊如何處理在程式碼中的通知。

Visual Studio for Mac 可以啟動監看式應用程式，以通知_啟動模式_通知：

監看式應用程式專案上按一下滑鼠右鍵，然後選擇 **執行與 > 自訂組態...** :

[![](installation-images/runwith-customparams-sml.png "執行自訂組態")](installation-images/runwith-customparams.png#lightbox)

這會開啟**自訂參數**視窗中，您可以選取**通知**（並提供 JSON 承載），然後按**執行**在模擬器中啟動監看式應用程式：

[![](installation-images/runwith-execargs-sml.png "設定通知和裝載")](installation-images/runwith-execargs.png#lightbox)

## <a name="debugging"></a>偵錯

在 Visual Studio for Mac 和 Visual Studio 支援偵錯。
請記得通知模式中偵錯時提供通知的 JSON 檔案。 此螢幕擷取畫面顯示偵錯中斷點的監看式應用程式中叫用：

![](installation-images/debug-sml.png "此螢幕擷取畫面顯示在監看式應用程式中叫用偵錯中斷點")

之後的啟動指示您將得到上執行應用程式監看式**iOS 模擬器 （監看式）** 。
您可以選取通知模式**偵錯 > 開啟系統記錄檔**(**CMD + /** )，並使用`Console.WriteLine`程式碼中。

### <a name="debugging-lifecycle-event-handlers"></a>偵錯的生命週期事件處理常式

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

WatchOS 範本檔案 (例如`InterfaceController`， `ExtensionDelegate`， `NotificationController`，和`ComplicationController`) 隨附於已實作其所需的生命週期方法。 新增`Console.WriteLine`呼叫，並閱讀**應用程式輸出**以深入了解事件生命週期。

## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Watch 應用程式上的影片](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的 WatchKit 秘訣](https://developer.apple.com/watchkit/tips/)
