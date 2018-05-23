---
title: watchOS 疑難排解
description: 已知的問題和因應措施 watchOS 開發問題。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 6e7a7dd09d65b88831136662d8718886aaf483c5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="watchos-troubleshooting"></a>watchOS 疑難排解

此頁面包含其他資訊和因應措施仍在開發的功能。 部分這些因應措施僅適用於我們的預覽版本。

- [已知問題](#knownissues)

- [移除圖示影像的 Alpha 色板](#noalpha)

- [手動加入介面的控制器檔案](#add)Xcode 介面產生器。

- [正在啟動從命令列 WatchApp](#command_line)。

<a name="knownissues" />

## <a name="known-issues"></a>已知問題

### <a name="general"></a>一般

<a name="deploy" />

- 較舊版本的 Visual Studio for Mac 不正確地顯示其中一個**AppleCompanionSettings**圖示為 88 x 88 像素為單位; 會導致**遺漏圖示錯誤**如果您嘗試以提交至應用程式存放區。
    這個圖示應該是 87 x 87 像素 (29 單位**@3x** Retina 螢幕)。 您無法在 Visual Studio 中修正此問題適用於 Mac 的影像資產在 Xcode 中編輯，或是手動編輯**Contents.json**檔案 (以符合[這個範例](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132))。

- 如果監看式擴充功能專案**Info.plist > WKApp 套件組合識別碼**不[正確設定](~/ios/watchos/get-started/project-references.md)以符合監看式應用程式的**套件組合識別碼**，偵錯工具將無法連接和視覺化Studio for Mac 將會等候訊息 *「 正在等候偵錯工具要連接 」*。

- 中支援偵錯**通知**模式，但可以是不可靠。 重試一次將有時運作。 確認監看式應用程式的**Info.plist** `WKCompanionAppBundleIdentifier`設定為符合的 iOS 父/容器應用程式套件組合識別碼 (ie。 在 iPhone 執行的一個)。

- iOS 設計工具不會顯示摘要或通知介面控制站的進入點箭號。

- 您無法加入兩個`WKNotificationControllers`分鏡腳本。
    因應措施：`notificationCategory`分鏡腳本 XML 中的項目一律已插入具有相同`id`。 若要解決這個問題，您可以加入兩個 （或以上） 的通知控制站、 文字編輯器中開啟分鏡腳本檔案，然後手動變更`id`是唯一的項目。

    [![](troubleshooting-images/duplicate-id-sml.png "開啟分鏡腳本檔案的文字編輯器中，並手動變更是唯一的 id 項目")](troubleshooting-images/duplicate-id.png#lightbox)

- 您可能會看到錯誤 「 應用程式尚未建置 」 時，嘗試啟動應用程式。 這會發生**清除**當啟始專案設定為 監看式擴充功能專案中。
    修正方法是選取**建置 > 全部重建**，然後重新啟動應用程式。

### <a name="visual-studio"></a>Visual Studio

IOS 設計工具支援監看式套件*需要*正確地設定方案。 若未設定專案參考 (請參閱[如何將參考](~/ios/watchos/get-started/project-references.md)) 則設計介面將無法正常運作。

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>移除圖示影像的 Alpha 色板

圖示不應包含 alpha 色板 （alpha 色板定義影像的透明區域），否則應用程式將會遭到拒絕，錯誤如下所示的應用程式市集提交期間：

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

所以可以輕鬆地移除 Mac OS X 使用 alpha 色板**預覽**應用程式：

1. 開啟中的圖示影像**預覽**，然後選擇 **檔案 > 匯出**。

2. 出現的對話方塊會包含**Alpha** alpha 色板是否有核取方塊。

    ![](troubleshooting-images/remove-alpha-sml.png "如果 alpha 色板存在於出現的對話方塊會包含 Alpha 核取方塊")

3. *Untick* **Alpha**核取方塊和**儲存**檔案的正確位置。

4. 圖示影像應該立即通過 Apple 的驗證檢查。


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>手動加入介面的控制器檔案

> [!IMPORTANT]
> Xamarin 的 WatchKit 支援包括設計監看式分鏡腳本 iOS 設計工具中 （在 Visual Studio for Mac 和 Visual Studio），不需要如下所述的步驟。 Visual Studio for Mac 屬性填補和檔案會自動建立的 C# 程式碼中的類別名稱只是提供介面控制站。


*如果*使用 Xcode 介面產生器，請遵循下列步驟來建立監看式應用程式的新介面控制站，並啟用具有 Xcode 的同步處理，以便插座和動作可以使用 C# 中：

1. 開啟 監看式應用程式的**Interface.storyboard**中**Xcode 介面產生器**。
    
    ![](troubleshooting-images/add-6.png "在 Xcode 介面產生器中開啟分鏡腳本")

2. 拖曳新`InterfaceController`到分鏡腳本：

    ![](troubleshooting-images/add-1.png "InterfaceController")

3. 您可以現在將控制項拖曳到介面控制器 （例如。 標籤和按鈕），但您無法建立插座或動作，因為沒有任何 **.h**標頭檔。 下列步驟可能會造成需要 **.h**建立標頭檔。

    ![](troubleshooting-images/add-2.png "版面配置中的按鈕")

4. 關閉分鏡腳本，並返回 Visual Studio for mac。 建立新的 C# 檔案**MyInterfaceController.cs** （或是您想要的任何名稱） 中**監看應用程式擴充功能**專案 （不監看式應用程式本身的分鏡腳本的位置）。 加入下列程式碼 （更新命名空間、 類別名稱和建構函式名稱）：

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

5. 建立另一個新 C# 檔案**MyInterfaceController.designer.cs**中**監看應用程式擴充功能**專案並加入下列程式碼。 請務必更新命名空間、 類別名稱和`Register`屬性：

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
    
    提示： 您可以 （選擇性） 將這個檔案拖曳 Mac 方案填補的其他 C# 檔案在 Visual Studio 中的第一個檔案的子節點。 它看起來像這樣：
    
    ![](troubleshooting-images/add-5.png "方案填補")

6. 選取**建置 > 建置所有**使 Xcode 同步處理將會辨識新的類別 (透過`Register`屬性)，我們使用。

7. 重新開啟 監看式應用程式的分鏡腳本檔案上按一下滑鼠右鍵，然後選取 分鏡腳本**開啟 > Xcode 介面產生器**:

    ![](troubleshooting-images/add-6.png "在介面產生器中開啟分鏡腳本")

8. 選取新的介面控制站，並提供您定義以上版本，例如類別名稱。 `MyInterfaceController`.
如果所有項目已正確運作，它應該會出現在自動**類別：** 下拉式清單，而且您可以從該處選取它。

    ![](troubleshooting-images/add-4.png "設定自訂的類別")

9. 選擇**助理編輯器**，讓您可以查看分鏡腳本和程式碼的並存檢視中 Xcode （使用兩個重疊圓形圖示）：

    ![](troubleshooting-images/add-7.png "小幫手編輯器的工具列項目")

    當焦點是在程式碼 窗格中時，請確定您正在查看 **.h**標頭檔，而且如果未在軌跡列中以滑鼠右鍵按一下並選取正確的檔案 (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "選取 MyInterfaceController")

10. 您現在可以建立插座和動作**Ctrl + 拖曳**到分鏡腳本從 **.h**標頭檔。

    ![](troubleshooting-images/add-9.png "建立插座和動作")

    當您放開拖曳時，您將會提示您選取要建立輸出或動作，並選擇其名稱：

    ![](troubleshooting-images/add-a.png "輸出和動作對話方塊")

11. 儲存分鏡腳本變更並關閉 Xcode，回到 Visual Studio for mac。 它會偵測到標頭檔的變更，並自動將程式碼加入 **.designer.cs**檔案：


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


您可以現在參考的控制項 （或實作的動作） 在 C# ！


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>啟動監看式應用程式，從命令列

> [!IMPORTANT]
> 您可以啟動監看式應用程式一般的應用程式模式中，根據預設，而且在**概覽**或**通知**模式使用[自訂執行中參數](~/ios/watchos/get-started/installation.md#custommodes)適用於 Mac 的 Visual Studio 中，Visual Studio。


您也可以使用命令列，以控制 iOS 模擬器。 用來啟動 監看式應用程式的命令列工具是**mtouch**。

以下是完整的範例 （以單行終端機中執行）：

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

您需要更新以反映您的應用程式的參數是`launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

主應用程式套件組合的完整路徑*包含監看式應用程式和延伸模組的 iOS 應用程式*。

> [!NOTE]
> 您需要提供的路徑為*iPhone 應用程式的.app 檔*，也就是一個，將會部署至 iOS 模擬器和包含 watch 擴充功能和監看式應用程式。

範例：

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>通知模式

若要測試應用程式的[**通知**模式](~/ios/watchos/platform/notifications.md)，將`watchlaunchmode`參數`Notification`並提供包含測試通知裝載在 JSON 檔案的路徑。

內容參數是*必要*通知模式。

例如，將這些引數加入至 mtouch 命令：

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>其他引數

其餘的引數說明如下：

### <a name="--sdkroot"></a>--sdkroot

必要。 指定 Xcode （6.2 或更新版本） 的路徑。

範例：

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>-裝置

要執行的模擬器裝置。 這可以指定兩種方式，使用特定裝置，udid，或使用執行階段和裝置類型的組合。

實際值機器，而異，並可透過 Apple 的查詢**simctl**工具：

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

範例：

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**執行階段和裝置類型**

範例：

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
