---
title: "設定與安裝"
description: "若要開發 watchOS 的設定"
ms.topic: article
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/05/2017
ms.openlocfilehash: f7e511d7f0a933ab7f29369e5e5f0aa46607c8f8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="installation"></a>安裝

watchOS 4 需要 macOS 利也 (10.12) 以 Xcode 9。

watchOS 1 原本需要 Xcode 7 OS X Yosemite (10.10)。

> [!WARNING]
> [2018 年 4 月 1 之後，無法接受 watchOS 1 更新](https://developer.apple.com/news/?id=11162017a)。 未來的更新必須使用 watchOS 2 SDK 或更新版本。使用 watchOS 建置建議 4 SDK。

## <a name="project-structure"></a>專案結構

監看式應用程式包含三個專案：

- **Xamarin.iOS iPhone 應用程式專案**-這是正常的 iPhone 專案，它可以是任何 Xamarin.iOS 範本。 監看式應用程式和其副檔名會集結內這個主專案中。

- **監看式擴充功能專案**-這是包含監看式應用程式的程式碼 （例如控制器類別）。

- **監看式應用程式專案**-監看式應用程式包含使用者介面分鏡腳本檔案，與 UI 的所有資源。

[監看式套件 Catalog 範例](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)中 Xamarin.Studio 方案看起來像這樣：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](installation-images/catalog-solution.png "Visual Studio 中的方案")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](installation-images/catalog-solution-vs.png "Visual Studio 中的方案")

-----

下載並執行[WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)若要開始使用範例。
從範例的螢幕可以找到上[控制項](~/ios/watchos/user-interface/index.md)頁面。


## <a name="creating-a-new-project"></a>建立新專案

您無法建立新 「 監看式解決方案 」...而是將監看式應用程式加入至現有的 iOS 應用程式。 請遵循下列步驟來建立監看式應用程式：

1. 如果您沒有現有的專案，首先選擇**檔案 > 新的方案**並建立 iOS 應用程式 (例如，**單一檢視應用程式**):

    [ ![](installation-images/cycle8-2-sml.png "選擇檔案 > 新的方案，並建立 iOS 應用程式")](installation-images/cycle8-2.png)

2. 一旦建立 iOS 應用程式 （或您打算使用您現有的 iOS 應用程式），以滑鼠右鍵按一下方案，然後選擇 **新增 > 加入新的專案...**.在**新專案**視窗選取**watchOS > 應用程式 > WatchKit 應用程式**:

    [ ![](installation-images/cycle8-6-sml.png "選取 watchOS > 應用程式 > WatchKit 應用程式")](installation-images/cycle8-6.png)

3. 下一個畫面可讓您選擇哪一個 iOS 應用程式專案應該會包含監看式應用程式：

    [ ![](installation-images/cycle8-7-sml.png "選擇哪一個 iOS 應用程式專案應該會包含監看式應用程式")](installation-images/cycle8-7.png)

4. 最後，選擇要儲存專案的位置 （並選擇性地啟用原始檔控制）：

    [ ![](installation-images/cycle8-8-sml.png "選擇要儲存專案的位置")](installation-images/cycle8-8.png)

5. 適用於 Mac 的 visual Studio 會自動設定[專案參考和**Info.plist**設定](~/ios/watchos/get-started/project-references.md)您。

## <a name="creating-the-watch-user-interface"></a>建立監看式使用者介面

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>使用 Xamarin iOS 設計工具

監看式應用程式上按兩下**Interface.storyboard**編輯使用 iOS 設計工具。 您可以將介面控制器和 UI 控制項拖曳至分鏡腳本從**工具箱**，並設定它們使用**屬性**板：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](installation-images/iosdesigner-sml.png "分鏡腳本設計工具中")](installation-images/iosdesigner.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](installation-images/iosdesigner-sml-vs.png "分鏡腳本設計工具中")](installation-images/iosdesigner-vs.png)

-----

您應該給予每個新的介面控制站**類別**選取它，然後輸入中的名稱**屬性**板 （這會自動會建立必要的 C# 程式碼後置檔案）：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](installation-images/iosdesigner-classname.png "類別提供每個新的介面控制站")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](installation-images/iosdesigner-classname-vs.png "類別提供每個新的介面控制站")

-----

建立由 segues **Ctrl + 拖曳**到另一個介面控制站上的按鈕、 表格或介面控制站。


### <a name="using-xcode-on-the-mac"></a>在 Mac 上使用 Xcode

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

您可以繼續使用 Xcode 建置您的使用者介面 Interface.storyboard 檔案上按一下滑鼠右鍵，然後選取**開啟 > Xcode 介面產生器**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Visual Studio 使用者也可以使用 Xcode 切換至直接使用 Mac 建置主應用程式會建立其使用者介面。
Visual Studio 中開啟您的方案適用於 Mac 和 Interface.storyboard 檔案上按一下滑鼠右鍵，然後選取**開啟 > Xcode 介面產生器**:

-----

![](installation-images/openwith-xcode.png "Xcode 介面產生器中開啟 Interface.storyboard")

如果使用 Xcode，然後您應該遵循相同的步驟，監看式應用程式與標準[iOS 應用程式的分鏡腳本](~/ios/user-interface/storyboards/index.md)(例如建立插座和動作**Ctrl + 拖曳**到**.h**標頭檔)。

當您儲存分鏡腳本中會自動加入 Xcode 介面產生器的插座及您建立的動作與 C# **.designer.cs**監看式擴充功能專案中的檔案。


### <a name="adding-additional-screens-in-xcode"></a>在 Xcode 中加入額外的螢幕

當您新增其他畫面 （除了什麼預設包含範本中） 您使用 Xcode 介面產生器的分鏡腳本**您必須手動加入 C# 程式碼檔案**每個新介面控制站。

請參閱[進階指示如何將新的介面控制站新增至分鏡腳本上](~/ios/watchos/troubleshooting.md#add)。

*Xamarin iOS 設計工具會自動執行此，不需要任何手動步驟。*


## <a name="building"></a>建置

類似其他 iOS 專案，建置包含監看式應用程式的專案。 建立程序將會導致 iPhone 應用程式 (.app)，其中包含 watch 擴充功能 (.appex)，其中包含程式碼較不監看式應用程式 (.app)。


## <a name="launching"></a>啟動

您可以啟動監看式應用程式在模擬器中使用 Visual Studio for Mac 或 Visual Studio （開始 Mac 建置主機上）。

有兩種模式啟動 WatchKit 應用程式：

 - 一般應用程式模式 （預設值），以及
- [通知](~/ios/watchos/platform/notifications.md)（這需要測試通知裝載以 JSON 格式）。

### <a name="xcode-8-support"></a>Xcode 8 支援

一旦安裝 Xcode 8 （或更新版本） 時，Apple Watch 模擬器是分開 iOS 模擬器 (不同於[Xcode 6](#xcode6)，其中顯示為*外部顯示器*)。
當您選取的監看式應用程式專案，並設為啟始專案時，將會顯示模擬器清單中*iOS 模擬器*可選 （如下所示）。

[ ![](installation-images/xs-xcode8-watchos3-sml.png "選取模擬器類型")](installation-images/xs-xcode8-watchos3.png)

當您開始偵錯，*兩個*模擬器應該開始的 iOS 模擬器*和*Apple Watch 模擬器。 使用**命令 + Shift + H**瀏覽至 [監看式] 功能表和時鐘朝; 並使用**硬體**功能表來設定**強制觸控壓力**。 捲動滑鼠則 trackpad 上會模擬使用數位皇冠。

#### <a name="troubleshooting"></a>疑難排解

就會顯示下列錯誤**應用程式輸出**如果您嘗試啟動沒有成對的監看式模擬器：

```csharp
error MT0000: Unexpected error - Please file a bug report at http://bugzilla.xamarin.com
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

請參閱[Apple 論壇](https://forums.developer.apple.com/thread/7783)如需設定模擬器中，如果預設值不是工作指示。


<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 和 watchOS 1

您必須進行*監看式擴充功能專案***啟始專案**之前執行或偵錯應用程式。 您無法"start"監看式應用程式本身，如果您選擇的 iOS 應用程式然後就會開始正常的 iOS 模擬器中。

根據預設監看式應用程式會啟動在正常**應用程式**模式 （不摘要 」 或 「 通知模式），從 Visual Studio for Mac 的**執行**或**偵錯**命令。

使用 Xcode 6 時只 iPhone 5、 iPhone 5 秒、 iPhone 6 和 iPhone 6 Plus 可以啟動其中一個外部顯示器**Apple Watch-38 公釐**或**Apple Watch-42 公釐**會監看式應用程式顯示。

**注意：**請記住，監看畫面未自動顯示在 iOS 模擬器中使用 Xcode 6 時。
使用**硬體 > 外接式顯示器**功能表顯示 [監看式] 畫面。

<a name="custommodes" />

## <a name="launching-notification-mode"></a>啟動通知模式

請參閱[通知頁面](~/ios/watchos/platform/notifications.md)資訊如何處理程式碼中的通知。


Visual Studio for Mac 可以開始監看式應用程式，以通知_啟動模式_通知：



監看式應用程式專案上按一下滑鼠右鍵，然後選擇 **執行與 > 自訂組態...**:


[![](installation-images/runwith-customparams-sml.png "執行自訂組態")](installation-images/runwith-customparams.png)


這會開啟**自訂參數**視窗，您可以在其中選取**通知**（並提供 JSON 裝載），然後按下**執行**在模擬器中啟動監看式應用程式：


[![](installation-images/runwith-execargs-sml.png "設定的通知和內容")](installation-images/runwith-execargs.png)



## <a name="debugging"></a>偵錯

在 Visual Studio for Mac 和 Visual Studio 支援偵錯。
請記得通知模式中偵錯時提供通知的 JSON 檔案。 這個螢幕擷取畫面顯示叫用監看式應用程式中的偵錯中斷點：

![](installation-images/debug-sml.png "這個螢幕擷取畫面顯示叫用監看式應用程式中的偵錯中斷點")

之後啟動指示您會得到上執行應用程式監看式**iOS 模擬器 （監看式）**。
您可以選取的通知模式**偵錯 > 開啟系統記錄檔**(**CMD + /**)，並使用`Console.WriteLine`程式碼中。

### <a name="debugging-lifecycle-event-handlers"></a>偵錯的生命週期事件處理常式

<!--
To test the functionality in your  and 
    methods, use the **Hardware > Lock** command in the iOS Simulator.
    Locking will trigger the `DidDeactivate` method and the watch simulator
    will indicate that it has been locked. Swipe the iOS Simulator to unlock,
    which triggers the `WillActivate` method of the watch app.
-->

WatchOS 範本檔案 (例如`InterfaceController`， `ExtensionDelegate`， `NotificationController`，和`ComplicationController`) 會隨附其已實作的必要存留週期方法。 新增`Console.WriteLine`呼叫和讀取**應用程式輸出**以深入了解事件生命週期。



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [第一個監看式應用程式的視訊](http://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的 WatchKit 秘訣](https://developer.apple.com/watchkit/tips/)
