---
title: watchOS 疑難排解
description: 本檔討論使用 Xamarin 進行 watchOS 開發的已知問題和因應措施。 它描述有問題的影像、手動新增介面控制器檔案、從命令列啟動 watch 應用程式等等。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 7e56eed866cb647bd654370d587b02bcaba04d4e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432810"
---
# <a name="watchos-troubleshooting"></a>watchOS 疑難排解

此頁面包含您可能會遇到之問題的其他資訊和因應措施。

- [已知問題](#knownissues)

- [從圖示影像中移除 Alpha 通道](#noalpha)

- [手動新增](#add) Xcode Interface Builder 的介面控制器檔案。

- [從命令列啟動 WatchApp](#command_line)。

<a name="knownissues"></a>

## <a name="known-issues"></a>已知問題

### <a name="general"></a>一般

<a name="deploy"></a>

- 較早版本的 Visual Studio for Mac 不正確地將其中一個 **AppleCompanionSettings** 圖示顯示為88x88 圖元;如果您嘗試提交給 App Store，這會導致 **遺失圖示錯誤** 。
    此圖示應為 Retina 畫面)  (29 個單位的87x87 圖元 **@3x** 。 您無法在 Visual Studio for Mac 中修正此問題-請在 Xcode 中編輯影像資產，或手動編輯檔案的 **Contents.js** 。

- 如果 Watch 延伸模組專案的 **Info. plist > WKApp** 套件組合識別碼未 [正確設定](~/ios/watchos/get-started/project-references.md) 為符合 Watch 應用程式套件組合 **識別碼**，則偵錯工具將無法連線，而且 Visual Studio for Mac 將會等候訊息「 *正在等候偵錯工具連接*」。

- **通知**模式支援偵錯工具，但可能不可靠。 重試有時會正常運作。 確認 Watch 應用程式的 **資訊 plist** `WKCompanionAppBundleIdentifier` 已設定為符合 iOS 父/容器應用程式的套件組合識別碼， (Ie. 在 iPhone) 上執行的應用程式。

- iOS 設計工具不會顯示快速概覽或通知介面控制器的進入點箭號。

- 您無法將兩個加入 `WKNotificationControllers` 至腳本。
    因應措施：腳本 `notificationCategory` XML 中的元素一律會以相同的方式插入 `id` 。 若要解決這個問題，您可以新增兩個 (或多個) 通知控制器、在文字編輯器中開啟分鏡腳本檔案，然後手動將 `id` 元素變更為唯一的。

    [![在文字編輯器中開啟分鏡腳本檔案，然後手動將 id 元素變更為唯一](troubleshooting-images/duplicate-id-sml.png)](troubleshooting-images/duplicate-id.png#lightbox)

- 當您嘗試啟動應用程式時，可能會看到錯誤「尚未建立應用程式」。 當啟始專案設定為 watch 延伸模組專案時，就會發生 **這種情況** 。
    修正方法是選取 **組建 > 全部重建** ，然後重新開機應用程式。

### <a name="visual-studio"></a>Visual Studio

Watch 套件的 iOS 設計工具支援 *需要* 正確設定解決方案。 如果未設定專案參考 (請參閱 [如何設定參考](~/ios/watchos/get-started/project-references.md)) 然後設計介面將無法正常運作。

<a name="noalpha"></a>

## <a name="removing-the-alpha-channel-from-icon-images"></a>從圖示影像中移除 Alpha 通道

圖示不應包含 Alpha 色板 (Alpha 通道會定義影像) 的透明區域，否則應用程式將會在 App Store 提交期間遭到拒絕，並出現類似下面的錯誤：

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

您可以輕鬆地使用 **預覽版** 應用程式移除 Mac OS X 上的 Alpha 通道：

1. 在 [ **預覽** ] 中開啟圖示影像，然後選擇 [檔案 **> 匯出**]。

2. 如果有 Alpha 色板，出現的對話方塊會包含 **Alpha** 核取方塊。

    ![如果有 Alpha 色板，則出現的對話方塊會包含 Alpha 核取方塊](troubleshooting-images/remove-alpha-sml.png)

3. *Untick* [ **Alpha** ] 核取方塊，並 **將檔案儲存** 至正確的位置。

4. 圖示影像現在應該會通過 Apple 的驗證檢查。

<a name="add"></a>

## <a name="manually-adding-interface-controller-files"></a>手動新增介面控制器檔案

> [!IMPORTANT]
> Xamarin 的 WatchKit 支援包括在 iOS 設計工具中設計監看式分鏡腳本 (在 Visual Studio for Mac 和 Visual Studio) ，不需要執行下列步驟。 只要在 Visual Studio for Mac Properties pad 中提供介面控制器的類別名稱，系統就會自動建立 c # 程式碼檔案。

*如果* 您使用 Xcode Interface Builder，請遵循下列步驟來為您的 watch 應用程式建立新的介面控制器，並啟用與 Xcode 的同步處理，以便在 c # 中使用輸出和動作：

1. 開啟 watch 應用程式的 **介面。** **Xcode Interface Builder**中的分鏡腳本。

    ![在 Xcode 中開啟分鏡腳本 Interface Builder](troubleshooting-images/add-6.png)

2. 將新的拖曳 `InterfaceController` 至腳本：

    ![InterfaceController](troubleshooting-images/add-1.png)

3. 您現在可以將控制項拖曳到介面控制器 (例如 標籤和按鈕) 但因為沒有 **.h** 標頭檔，所以您無法建立輸出或動作。 下列步驟將會建立必要的 **.h** 標頭檔。

    ![版面配置中的按鈕](troubleshooting-images/add-2.png)

4. 關閉腳本並返回 Visual Studio for Mac。 建立新的 c # 檔案 **MyInterfaceController.cs** (或您喜歡的任何名稱) 在 **watch 應用程式擴充** 功能專案中 (不是) 腳本的監看式應用程式本身。 加入下列程式碼， (更新命名空間、classname 和) 的函式名稱：

    ```csharp
    using System;
    using WatchKit;
    using Foundation;

    namespace WatchAppExtension  // remember to update this
    {
        public partial class MyInterfaceController // remember to update this
        : WKInterfaceController
        {
            public MyInterfaceController // remember to update this
            (IntPtr handle) : base (handle)
            {
            }
            public override void Awake (NSObject context)
            {
                base.Awake (context);
                // Configure interface objects here.
                Console.WriteLine ("{0} awake with context", this);
            }
            public override void WillActivate ()
            {
                // This method is called when the watch view controller is about to be visible to the user.
                Console.WriteLine ("{0} will activate", this);
            }
            public override void DidDeactivate ()
            {
                // This method is called when the watch view controller is no longer visible to the user.
                Console.WriteLine ("{0} did deactivate", this);
            }
        }
    }
    ```

5. 在**watch 應用程式擴充**功能專案中，建立另一個新的 c # 檔案**MyInterfaceController.designer.cs** ，並新增下列程式碼。 請務必更新命名空間、classname 和 `Register` 屬性：

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;

    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```

    > [!TIP]
    > 您可以 (選擇性地) 將此檔案拖曳至 Visual Studio for Mac Solution Pad 中的其他 c # 檔案，使其成為第一個檔案的子節點。 它會顯示如下：

    ![Solution pad](troubleshooting-images/add-5.png)

6. 選取 [ **組建 > 組建** ]，讓 Xcode 同步處理可透過我們所用的 `Register` 屬性) 來辨識新類別 (。

7. 以滑鼠右鍵按一下 watch 應用程式分鏡腳本檔案，然後選取 [開啟檔案]  **> Xcode Interface Builder，以**重新開啟分鏡腳本：

    ![在 Interface Builder 中開啟分鏡腳本](troubleshooting-images/add-6.png)

8. 選取您的新介面控制器，並為其提供您在上面定義的 classname，例如 `MyInterfaceController`.
    如果一切都正常運作，它應該會自動出現在 [ **類別：** ] 下拉式清單中，您可以從該處選取它。

    ![設定自訂類別](troubleshooting-images/add-4.png)

9. 選擇 Xcode 中的 [ **助理編輯器** ]， (具有兩個重迭圓形) 的圖示，讓您可以看到分鏡腳本和程式碼的並列：

    ![助理編輯器工具列專案](troubleshooting-images/add-7.png)

    當焦點在程式碼窗格中時，請確定您查看的是  **.h** 標頭檔，如果沒有在階層連結列上按一下滑鼠右鍵，並選取正確的檔案 (**MyInterfaceController**) 

    ![選取 MyInterfaceController](troubleshooting-images/add-8.png)

10. 您現在可以從分鏡腳本中的 **Ctrl + 拖曳** 至 **.h** 標頭檔中，建立輸出和動作。

    ![建立輸出和動作](troubleshooting-images/add-9.png)

    當您放開拖曳時，系統會提示您選取是否要建立輸出或動作，然後選擇其名稱：

    ![[輸出] 和 [動作] 對話方塊](troubleshooting-images/add-a.png)

11. 儲存分鏡腳本變更並關閉 Xcode 之後，請返回 Visual Studio for Mac。 它會偵測標頭檔變更，並自動將程式碼新增至 **designer.cs** 檔案：

    ```csharp
    [Register ("MyInterfaceController")]
    partial class MyInterfaceController
    {
        [Outlet]
        WatchKit.WKInterfaceButton myButton { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (myButton != null) {
                myButton.Dispose ();
                myButton = null;
            }
        }
    }
    ```

您現在可以參考 (的控制項，或在 c # 中執行) 的動作！

<a name="command_line"></a>

## <a name="launching-the-watch-app-from-the-command-line"></a>從命令列啟動 Watch 應用程式

> [!IMPORTANT]
> 您可以根據預設，以一般應用程式模式啟動 Watch 應用程式，也可以使用 Visual Studio for Mac 和 Visual Studio 中的[自訂執行參數](~/ios/watchos/get-started/installation.md#custommodes)，**快速概覽**或**通知**模式。

您也可以使用命令列來控制 iOS 模擬器。 用來啟動 watch 應用程式的命令列工具是 **mtouch**。

以下是在終端機) 中以一行形式執行的完整範例 (：

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

您需要更新以反映應用程式的參數為 `launchsimwatch` ：

### <a name="--launchsimwatch"></a>--launchsimwatch

IOS 應用程式主要應用程式套件組合的完整路徑 *，其中包含 watch 應用程式和延伸*模組。

> [!NOTE]
> 您需要提供的路徑是適用于 *iPhone 應用程式*檔案，也就是將部署至 iOS 模擬器，並同時包含 watch 延伸模組和 watch 應用程式的應用程式檔。

範例：

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

## <a name="notification-mode"></a>通知模式

若要測試應用程式的 [**通知** 模式](~/ios/watchos/platform/notifications.md)，請將 `watchlaunchmode` 參數設定為， `Notification` 並提供包含測試通知承載之 JSON 檔案的路徑。

通知模式 *需要* 承載參數。

例如，將下列引數新增至 mtouch 命令：

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```

## <a name="other-arguments"></a>其他引數

其餘的引數如下所述：

### <a name="--sdkroot"></a>--sdkroot

必要。 指定 Xcode (6.2 或更新版本) 的路徑。

範例：

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--裝置

要執行的模擬器裝置。 您可以使用下列兩種方式來指定這兩種方式：使用特定裝置的 udid，或使用執行時間和裝置類型的組合。

確切的值會因電腦而異，而且可以使用 Apple 的 **simctl** 工具來查詢：

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

範例：

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**執行時間和裝置類型**

範例：

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```

## <a name="related-links"></a>相關連結

- [WatchKitCatalog (範例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (範例) ](/samples/xamarin/ios-samples/watchos-watchtables)