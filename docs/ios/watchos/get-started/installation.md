---
title: 在 Xamarin 中安裝和使用 watchOS
description: 本檔說明如何安裝和使用 watchOS 搭配 Xamarin。 它討論安裝、watchOS 專案結構、如何使用 iOS 設計工具、Xcode 整合，並提供疑難排解秘訣。
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/05/2017
ms.openlocfilehash: 89d074e14f2a66d472f06acecf42127a7ff44c4c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437377"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>在 Xamarin 中安裝和使用 watchOS

watchOS 4 需要使用 Xcode 9 的 macOS Sierra (10.12) 。

watchOS 1 最初所需的 OS X Yosemite (10.10) 搭配 Xcode 7。

> [!WARNING]
> [2018 年4月1日之後，將不會接受 watchOS 1 更新](https://developer.apple.com/news/?id=11162017a)。 未來的更新必須使用 watchOS 2 SDK 或更新版本;建議使用 watchOS 4 SDK 建立。

## <a name="project-structure"></a>專案結構

Watch 應用程式是由三個專案所組成：

- **Xamarin. Ios iPhone 應用程式專案** -這是一般的 iPhone 專案，它可以是任何的 Xamarin iOS 範本。 Watch 應用程式及其延伸模組將會配套在此主要專案內。

- **監看延伸模組專案** -這包含程式碼 (例如 Watch 應用程式) 的控制器類別。

- **監看應用程式專案** -這包含消費者介面分鏡腳本檔案，其中包含 Watch 應用程式的所有 UI 資源。

[Watch 套件目錄範例](/samples/xamarin/ios-samples/watchos-watchkitcatalog)解決方案在 Xamarin 中看起來像這樣：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![Visual Studio 的解決方案](installation-images/catalog-solution.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Visual Studio 的解決方案](installation-images/catalog-solution-vs.png)

-----

下載並執行 [WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog) 範例以開始著手。
您可以在 [ [控制項](~/ios/watchos/user-interface/index.md) ] 頁面上找到範例中的畫面。

## <a name="creating-a-new-project"></a>建立新專案

您無法建立新的「監看式解決方案」 .。。相反地，您可以將 Watch 應用程式新增至現有的 iOS 應用程式。 遵循下列步驟來建立 watch 應用程式：

1. 如果您沒有現有的專案，請先選擇 [檔案] **> [新增方案** ]，然後建立 iOS 應用程式 (例如， **單一視圖應用程式**) ：

    [![選擇 [檔案] > [新增方案]，然後建立 iOS 應用程式](installation-images/cycle8-2-sml.png)](installation-images/cycle8-2.png#lightbox)

2. 一旦建立 iOS 應用程式 (或您打算使用現有的 iOS 應用程式) ，請以滑鼠右鍵按一下方案，然後選擇 [新增] **> 加入新專案 ...** 在 [ **新增專案** ] 視窗中，選取 [ **WatchOS > 應用程式 > WatchKit 應用程式**：

    [![選取 watchOS > 應用程式 > WatchKit 應用程式](installation-images/cycle8-6-sml.png)](installation-images/cycle8-6.png#lightbox)

3. 下一個畫面可讓您選擇要包含 watch 應用程式的 iOS 應用程式專案：

    [![選擇應包含 watch 應用程式的 iOS 應用程式專案](installation-images/cycle8-7-sml.png)](installation-images/cycle8-7.png#lightbox)

4. 最後，選擇要儲存專案 (的位置，並選擇性地啟用原始檔控制) ：

    [![選擇要儲存專案的位置](installation-images/cycle8-8-sml.png)](installation-images/cycle8-8.png#lightbox)

5. Visual Studio for Mac 會自動為您 [設定專案參考和 **資訊 plist** 設定](~/ios/watchos/get-started/project-references.md) 。

## <a name="creating-the-watch-user-interface"></a>建立 Watch 消費者介面

<a name="designer"></a>

### <a name="using-the-xamarin-ios-designer"></a>使用 Xamarin iOS 設計工具

按兩下 watch 應用程式的 **介面。** 分鏡腳本可使用 iOS 設計工具進行編輯。 您可以從 [ **工具箱** ] 將介面控制器和 UI 控制項拖曳至腳本，並使用 [ **屬性** ] 面板進行設定：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![設計工具中的分鏡腳本](installation-images/iosdesigner-sml.png)](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![設計工具中的分鏡腳本](installation-images/iosdesigner-sml-vs.png)](installation-images/iosdesigner-vs.png#lightbox)

-----

您應該透過選取類別，然後在**Properties** pad 中輸入名稱，來為每個新的介面控制器提供一個**類別** (這會自動建立所需的 c # 程式碼後置檔案) ：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![為每個新的介面控制器提供一個類別](installation-images/iosdesigner-classname.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![為每個新的介面控制器提供一個類別](installation-images/iosdesigner-classname-vs.png)

-----

使用 **Ctrl +** 從按鈕、資料表或介面控制器拖曳至另一個介面控制器來建立 segue。

### <a name="using-xcode-on-the-mac"></a>在 Mac 上使用 Xcode

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

您可以繼續使用 Xcode 來建立使用者介面，方法是在介面上按一下滑鼠右鍵，然後選取 [開啟檔案] **> Xcode Interface Builder**：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Visual Studio 使用者也可以使用 Xcode 來建立使用者介面，方法是切換至直接使用 Mac 組建主機。
在 Visual Studio for Mac 中開啟您的方案，然後以滑鼠右鍵按一下介面，然後選取 [ **開啟方式] > Xcode Interface Builder**：

-----

![開啟介面。 Xcode 中的分鏡腳本 Interface Builder](installation-images/openwith-xcode.png)

如果使用 Xcode，則您應該遵循相同的步驟來監看應用程式，如同一般的 [iOS 應用程式](~/ios/user-interface/storyboards/index.md) 分鏡腳本 (例如， **Ctrl + 拖曳** 至 **.h** 標頭檔) 來建立輸出和動作。

當您將分鏡腳本儲存在 Xcode Interface Builder 時，它會自動將您建立的 [輸出] 和 [動作] 新增至 watch 擴充功能專案中的 **designer.cs** 檔案。

### <a name="adding-additional-screens-in-xcode"></a>在 Xcode 中新增其他畫面

當您新增其他畫面 (範本中預設不包含的內容) 使用 Xcode Interface Builder 您必須手動新增每個新介面控制器 **的 c # 程式碼** 檔案。

請參閱有關 [如何將新的介面控制器新增至腳本的 Advanced 指示](~/ios/watchos/troubleshooting.md#add)。

*Xamarin iOS 設計工具會自動執行此操作，不需要任何手動步驟。*

## <a name="building"></a>建置

包含 watch 應用程式的專案會像其他 iOS 專案一樣建立。 建立程式會產生 iPhone 應用程式 (。包含 watch 延伸模組的應用程式)  (. appex) ，其中包含不限程式碼的監看式應用程式 (。

## <a name="launching"></a>啟動

您可以使用 Visual Studio for Mac 或 Visual Studio (在 Mac 組建主機) 上啟動，在模擬器中啟動 watch 應用程式。

有兩種模式可啟動 WatchKit 應用程式：

- 一般應用程式模式 (預設) ，以及
- [通知](~/ios/watchos/platform/notifications.md) (需要 JSON 格式的測試通知承載) 。

### <a name="xcode-8-support"></a>Xcode 8 支援

一旦安裝了 Xcode 8 (或更新版本的) ，Apple Watch 模擬器與 iOS 模擬器不同 (與 [Xcode 6](#xcode6)不同，它們會顯示為 *外部顯示*) 。
當您選取 [監看式] 應用程式專案並使其成為啟始專案時，[模擬器] 清單會顯示 *iOS* 模擬器來選擇 (，如下列) 所示。

[![選取模擬器類型](installation-images/xs-xcode8-watchos3-sml.png)](installation-images/xs-xcode8-watchos3.png#lightbox)

當您開始進行調試時，應該會啟動 *兩個* 模擬器-iOS 模擬器 *和* Apple Watch 模擬器。 使用 **命令 + Shift + H** 流覽至監看式功能表和時鐘臉部;然後使用 [ **硬體** ] 功能表來設定 **Force Touch 壓力**。 軌跡板或滑鼠的滾動將會使用 Digital Crown 模擬。

#### <a name="troubleshooting"></a>疑難排解

如果您嘗試啟動至沒有配對監看的模擬器， **應用程式輸出** 中將會出現下列錯誤：

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

如果預設值無法運作，請參閱 [Apple 的論壇](https://forums.developer.apple.com/thread/7783) 以取得設定模擬器的指示。

<a name="xcode6"></a>

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 和 watchOS 1

您必須先將 *watch 延伸模組專案* 設為 **啟始專案** ，然後再執行或對應用程式進行偵錯工具。 您無法「啟動」 watch 應用程式本身，如果您選擇 iOS 應用程式，則會在 iOS 模擬器中正常啟動。

根據預設，watch 應用程式會以一般 **應用程式** 模式啟動， (不會) 從 Visual Studio for Mac 的 **執行** 或 **調試** 程式命令。

使用 Xcode 6 時，只有 iPhone 5、iPhone 5 秒、iPhone 6 和 iPhone 6 Plus 可以啟用要顯示 Watch 應用程式的 **Apple Watch-38mm** 或 **Apple Watch-42mm** 的外部顯示器。

> [!NOTE]
> 請記住，使用 Xcode 6 時，[監看式] 畫面不會自動出現在 iOS 模擬器中。
> 使用 [ **外部顯示** ] 功能表上的 [硬體] > 顯示 [監看式] 畫面。

<a name="custommodes"></a>

## <a name="launching-notification-mode"></a>啟動通知模式

如需如何在程式碼中處理通知的詳細資訊，請參閱 [ [通知] 頁面](~/ios/watchos/platform/notifications.md) 。

Visual Studio for Mac 可以啟動 watch 應用程式，並提供通知的 _啟動模式_ ：

以滑鼠右鍵按一下 watch 應用程式專案，然後選擇 [以 **> 自訂設定執行**]：

[![執行自訂設定](installation-images/runwith-customparams-sml.png)](installation-images/runwith-customparams.png#lightbox)

這會開啟 [ **自訂參數** ] 視窗，您可以在其中選取 **通知** (並提供 JSON 承載) ，然後按 [ **執行** ] 以啟動模擬器中的 watch 應用程式：

[![設定通知和承載](installation-images/runwith-execargs-sml.png)](installation-images/runwith-execargs.png#lightbox)

## <a name="debugging"></a>偵錯

Visual Studio for Mac 和 Visual Studio 都支援偵錯工具。
在通知模式中進行調試時，請記得提供通知 JSON 檔案。 此螢幕擷取畫面顯示在監看式應用程式中叫用的偵錯工具中斷點：

![此螢幕擷取畫面顯示在監看式應用程式中遇到的偵錯工具中斷點](installation-images/debug-sml.png)

遵循啟動指示之後，您將會看到在 IOS 模擬器上執行的 watch 應用程式 ** (監看) **。
在通知模式中，您可以選取 [ **Debug] > 開啟 [系統記錄** 檔] (**CMD +/**) ，並 `Console.WriteLine` 在您的程式碼中使用。

### <a name="debugging-lifecycle-event-handlers"></a>偵錯工具生命週期事件處理常式

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

WatchOS 範本檔 (例如 `InterfaceController` 、 `ExtensionDelegate` 、 `NotificationController` 和 `ComplicationController`) 隨附于其所需的生命週期方法。 新增 `Console.WriteLine` 呼叫和讀取 **應用程式輸出** ，以進一步瞭解事件生命週期。

## <a name="related-links"></a>相關連結

- [WatchKitCatalog (範例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [第一次觀看應用程式影片](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Apple 的 WatchKit 秘訣](https://developer.apple.com/watchkit/tips/)